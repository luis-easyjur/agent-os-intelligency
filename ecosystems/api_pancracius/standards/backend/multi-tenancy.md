# Padrões de Multi-Tenancy e Múltiplos Bancos de Dados no Fênix

## Visão Geral

O Fênix implementa multi-tenancy através de **múltiplos bancos de dados**, onde cada banco representa uma instância diferente do sistema (iturn, oabes, oabmg). O isolamento de dados é garantido através da seleção dinâmica da conexão de banco de dados baseada em headers HTTP.

## Arquitetura Multi-Tenant

### Bancos de Dados Disponíveis

O sistema suporta múltiplos bancos de dados definidos no enum `BancoDeDados`:

```php
namespace App\Enums;

enum BancoDeDados: string
{
    case iturn = 'advogados_iturn';
    case oabes = 'advogados_oabes';
    case oabmg = 'advogados_oabmg';
}
```

### Conexões de Banco

As conexões são definidas em `config/database.php`:

```php
'connections' => [
    'iturn' => [
        'driver' => 'mysql',
        'host' => env('DB_HOST_ITURN'),
        'database' => 'advogados_iturn',
        // ...
    ],
    'oabes' => [
        'driver' => 'mysql',
        'host' => env('DB_HOST_OABES'),
        'database' => 'advogados_oabes',
        // ...
    ],
    'oabmg' => [
        'driver' => 'mysql',
        'host' => env('DB_HOST_OABMG'),
        'database' => 'advogados_oabmg',
        // ...
    ],
],
```

## Seleção Dinâmica de Banco

### Middleware DatabaseMiddleware

O `DatabaseMiddleware` é responsável por selecionar o banco de dados baseado no header HTTP `database`:

```php
$database = $request->header('database');
DB::setDefaultConnection($database);
```

### Header Obrigatório

Todas as requisições devem incluir o header:
```
database: iturn
```

Valores válidos: `iturn`, `oabes`, `oabmg`

## Isolamento de Dados

### Por Escritório (id_empresa)

Dentro de cada banco, os dados são isolados por escritório através do campo `id_empresa`:

```php
// Exemplo: Buscar processos do escritório do usuário logado
$user = User::getCurrent();
$processos = Processo::where('id_empresa', $user->id_empresa)->get();
```

### Por Banco de Dados

Dados de diferentes bancos são completamente isolados - não há compartilhamento entre `iturn`, `oabes` e `oabmg`.

## Padrões de Uso

### Em Models

Models podem especificar conexão explícita:

```php
namespace App\Models\Assinaturas;

use Illuminate\Database\Eloquent\Model;

class Assinatura extends Model
{
    protected $connection = 'iturn';
    protected $table = 'tb_assinaturas_produtos_ia';
    // ...
}
```

### Em Queries

```php
// Usar conexão específica
$escritorio = Escritorio::on('iturn')->find(1);
$escritorioOabes = Escritorio::on('oabes')->find(1);

// Usar conexão padrão (configurada pelo middleware)
$processos = Processo::where('id_empresa', $id_empresa)->get();
```

### Em Repositories

```php
namespace App\Repositories\Escritorios;

class EscritorioRepository extends ModelRepository
{
    public function find(int $id, string $connection = null): ?Escritorio
    {
        if ($connection) {
            return Escritorio::on($connection)->find($id);
        }
        return Escritorio::find($id);
    }
}
```

### Em Services

```php
namespace App\Services\Assinaturas;

class Cria extends AssinaturaService
{
    public function criaAssinatura(CriaAssinaturaDto $dto): Assinatura
    {
        // O banco já está configurado pelo middleware
        // Mas podemos validar se o escritório pertence ao banco correto
        $bancoEsperado = $dto->banco->name;
        $bancoAtual = DB::getDefaultConnection();
        
        if ($bancoEsperado !== $bancoAtual) {
            throw new InvalidDatabaseSelectedException();
        }
        
        // Continuação da lógica...
    }
}
```

## Validação de Consistência

### Escritório vs Banco

É importante validar que o escritório pertence ao banco correto:

```php
$escritorio = Escritorio::on('iturn')->find($id_empresa);

// Validar se escritório existe no banco atual
if (!$escritorio) {
    throw new EscritorioNaoEncontradoException();
}

// Validar se banco do request corresponde ao banco do escritório
$bancoEsperado = $escritorio->getConnection()->getName();
if ($bancoEsperado !== DB::getDefaultConnection()) {
    throw new InvalidDatabaseSelectedException();
}
```

## Boas Práticas

- **Sempre validar header database**: Middleware deve validar presença e validade do header
- **Isolamento por id_empresa**: Sempre filtrar dados por `id_empresa` do usuário logado
- **Validação de consistência**: Validar que escritório pertence ao banco especificado
- **Conexão explícita quando necessário**: Usar `on('connection')` quando precisar acessar banco específico
- **Documentação**: Documentar qual banco cada funcionalidade usa
- **Testes**: Testar isolamento de dados em diferentes bancos

## Exemplos Reais

### Validação de Banco em Request

```php
namespace App\Http\Requests\Assinaturas;

class CriaAssinaturaRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'banco' => ['required', 'in:advogados_iturn,advogados_oabes,advogados_oabmg'],
            'id_empresa' => ['required', 'integer'],
            'id_produto_ia' => ['required', 'integer'],
        ];
    }
    
    public function withValidator($validator)
    {
        $validator->after(function ($validator) {
            $banco = $this->input('banco');
            $bancoHeader = $this->header('database');
            
            // Validar se banco do request corresponde ao header
            $bancoEsperado = BancoDeDados::from($banco)->name;
            if ($bancoEsperado !== $bancoHeader) {
                $validator->errors()->add('banco', 'Banco do request não corresponde ao header');
            }
        });
    }
}
```

### Trabalhando com Múltiplos Bancos

```php
// Verificar se escritório existe em ambos os bancos
$escritorioIturn = Escritorio::on('iturn')->find(1);
$escritorioOabes = Escritorio::on('oabes')->find(1);

// Criar assinatura no banco correto
$banco = BancoDeDados::iturn;
DB::setDefaultConnection($banco->name);
$assinatura = Assinatura::create([...]);
```

## Tratamento de Erros

Exceções específicas para multi-tenancy:

- `NoDatabaseSelectedException`: Header `database` não fornecido
- `InvalidDatabaseSelectedException`: Banco especificado não é válido
- `EscritorioNaoEncontradoException`: Escritório não existe no banco especificado

## Testes

Em testes, sempre configurar o header `database`:

```php
beforeEach(function() {
    $this->actor = $this->withHeader('database', 'iturn')
        ->withHeader('accept', 'application/json');
});
```

Para testar múltiplos bancos:

```php
it('funciona com escritorio de banco diverso', function() {
    $escritorio = Escritorio::factory()->connection('oabes')->create();
    
    $data = [
        'banco' => 'advogados_oabes',
        'id_empresa' => $escritorio->id,
        // ...
    ];
    
    $response = $this->withHeader('database', 'oabes')
        ->post('/api/assinaturas', $data);
    
    $response->assertCreated();
});
```
