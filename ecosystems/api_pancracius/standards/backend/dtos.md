# Padrões de DTOs (Data Transfer Objects) no Fênix

## Visão Geral

O Fênix utiliza extensivamente **DTOs (Data Transfer Objects)** para transferência estruturada de dados entre camadas da aplicação. Os DTOs são organizados por domínio e seguem padrões específicos para diferentes contextos de uso.

## Estrutura de Organização

Os DTOs estão organizados em `app/DTO/` seguindo a estrutura de domínios:

```
app/DTO/
├── Assinatura/
│   ├── DadosInsert/
│   ├── ParametrosService/
│   └── Pacotes/
├── Auth/
│   └── Requests/
├── Common/
├── Documentos/
├── Financeiro/
└── ...
```

## Tipos de DTOs

### DadosInsert

DTOs que estendem `DadosInsert` são usados para inserção de dados no banco. Possuem funcionalidades automáticas:

```php
namespace App\DTO\Assinatura\DadosInsert;

use App\DTO\Common\DadosInsert;

class DadosCriaAssinatura extends DadosInsert
{
    public int $id_empresa;
    public int $id_produto_ia;
    protected string $label_criador = 'user_cadastro';

    public function toArray(): array
    {
        // Método herdado que:
        // 1. Valida os dados
        // 2. Adiciona id_empresa do usuário logado (se não existir)
        // 3. Adiciona user_cadastro com ID do usuário logado
        return parent::toArray();
    }
}
```

**Características:**
- Adiciona automaticamente `id_empresa` do usuário logado se não fornecido
- Adiciona campo de criador (`user_cadastro` por padrão) com ID do usuário
- Valida dados antes de converter para array

### DadosUpdate

DTOs que estendem `DadosUpdate` são usados para atualização de dados:

```php
namespace App\DTO\Common;

use Illuminate\Database\Eloquent\Model;

class DadosUpdate
{
    public Model $model;

    protected function validate()
    {}

    public function toArray(): array
    {
        $this->validate();
        $data = get_object_vars($this);
        unset($data['model']);
        return $data;
    }
}
```

### ParametrosService

DTOs usados para passar parâmetros entre services:

```php
namespace App\DTO\Assinatura\ParametrosService;

use App\Models\Assinaturas\Produtos\Produto;
use App\Models\Escritorio;
use App\Enums\BancoDeDados;

class CriaAssinaturaDto
{
    public function __construct(
        public Escritorio $escritorio,
        public Produto $produto,
        public BancoDeDados $banco,
        public ?Carbon $data_expiracao = null
    ) {}

    public static function deRequest(CriaAssinaturaRequest $request): self
    {
        // Factory method para criar DTO a partir de Request
    }
}
```

### Requests DTOs

DTOs simples para dados de requisições HTTP:

```php
namespace App\DTO\Auth\Requests;

class LoginDTO
{
    public function __construct(
        public string $email,
        public string $password
    ) {}
}
```

## Padrões de Uso

### Criação de DTOs a partir de Requests

```php
namespace App\Http\Requests\Assinaturas;

use App\DTO\Assinatura\ParametrosService\CriaAssinaturaDto;

class CriaAssinaturaRequest extends FormRequest
{
    public function loadDto(): CriaAssinaturaDto
    {
        $data = $this->validated();
        return CriaAssinaturaDto::deRequest($this);
    }
}
```

### Uso em Services

```php
namespace App\Services\Assinaturas;

use App\DTO\Assinatura\ParametrosService\CriaAssinaturaDto;
use App\DTO\Assinatura\DadosInsert\DadosCriaAssinatura;

class Cria extends AssinaturaService
{
    public function criaAssinatura(CriaAssinaturaDto $dto_criacao): Assinatura
    {
        // Converter DTO de parâmetros para DTO de inserção
        $dados_insert = DadosCriaAssinatura::deCriaAssinaturaDto($dto_criacao);
        
        // Usar DTO de inserção no repository
        $assinatura = $this->repository->criaAssinatura($dados_insert);
        
        return $assinatura;
    }
}
```

### Factory Methods

DTOs frequentemente possuem métodos estáticos de factory:

```php
class CriaAssinaturaDto
{
    public static function deRequest(CriaAssinaturaRequest $request): self
    {
        $data = $request->validated();
        // Lógica de construção
        return new self(...);
    }
    
    public static function deSincronizacao(Produto $produto): self
    {
        // Outro método de factory
    }
}
```

## Boas Práticas

- **Organização por domínio**: Agrupar DTOs por domínio funcional
- **Separação de responsabilidades**: DTOs diferentes para diferentes contextos (Insert, Update, Service params)
- **Factory methods**: Usar métodos estáticos para criar DTOs a partir de diferentes fontes
- **Validação**: Implementar validação nos DTOs quando necessário
- **Imutabilidade quando possível**: Preferir propriedades readonly quando os dados não devem mudar
- **Type hints**: Sempre usar type hints para propriedades e parâmetros
- **Documentação**: Documentar DTOs complexos com PHPDoc

## DTOs Comuns

### DadosInsert

Classe base para DTOs de inserção:
- Adiciona automaticamente `id_empresa` do usuário logado
- Adiciona campo de criador (`user_cadastro` por padrão)
- Método `toArray()` para conversão

### DadosUpdate

Classe base para DTOs de atualização:
- Requer referência ao modelo sendo atualizado
- Método `toArray()` para conversão

### PaginationDTO

DTO para parâmetros de paginação:

```php
namespace App\DTO\Common;

class PaginationDTO
{
    public function __construct(
        public int $page = 1,
        public int $perPage = 15,
        public ?string $search = null
    ) {}
}
```

## Exemplos Reais

### DTO de Inserção

```php
namespace App\DTO\Assinatura\DadosInsert;

use App\DTO\Assinatura\ParametrosService\CriaAssinaturaDto;
use App\DTO\Common\DadosInsert;

class DadosCriaAssinatura extends DadosInsert
{
    public int $id_empresa;
    public int $id_produto_ia;
    public string $banco;
    public ?string $data_expiracao = null;

    public static function deCriaAssinaturaDto(CriaAssinaturaDto $dto): self
    {
        return new self(
            id_empresa: $dto->escritorio->id,
            id_produto_ia: $dto->produto->id,
            banco: $dto->banco->value,
            data_expiracao: $dto->data_expiracao?->format('Y-m-d')
        );
    }
}
```

### DTO de Parâmetros de Service

```php
namespace App\DTO\Assinatura\ParametrosService;

class VerificaAssinaturaExisteDto
{
    public function __construct(
        public string $banco,
        public int $id_empresa,
        public int $id_produto_ia
    ) {}
}
```
