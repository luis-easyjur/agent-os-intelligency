# Padrões de Modelos e Estrutura do Fênix

## Contexto do Projeto

O Fênix é um sistema jurídico construído com Laravel e utilizando MySQL/MariaDB com Eloquent ORM.

## Modelos (Models) do Laravel

O projeto segue o padrão MVC do Laravel, onde cada tabela do banco de dados possui um modelo correspondente na pasta `app/Models`, organizados por domínio.

Modelos principais do Fênix:
- `Processo` - Processos jurídicos
- `AreaDeProcesso` - Áreas de processo
- `Assinatura` - Assinaturas de produtos
- `Pacote` - Pacotes de petições
- `ConsumoDePacote` - Consumo de pacotes
- `Documento` - Documentos
- `ModeloDeDocumento` - Modelos de documentos
- `Contrato` - Contratos
- `Receita` - Receitas
- `User` - Usuários do sistema
- `Escritorio` - Escritórios
- `BaseModel` - Classe base para modelos

## Nomenclatura

### Tabelas
- **Todas as tabelas têm prefixo `tb_`**
- Nomes em português ou inglês, plural e snake_case
- Exemplos: `tb_processos`, `tb_assinaturas_produtos_ia`, `tb_contrato`, `tb_documentos`, `tb_users_advogados`

### Modelos
- Nome no singular, PascalCase, correspondendo à tabela
- Organizados por domínio em subpastas
- **Modelos devem definir `protected $table` quando o nome da tabela não segue a convenção do Laravel**
- Exemplo: tabela `tb_processos` -> modelo `Processo` em `app/Models/Processos/` (define `$table = 'tb_processos'`)
- Exemplo: tabela `tb_assinaturas_produtos_ia` -> modelo `Assinatura` em `app/Models/Assinaturas/` (define `$table = 'tb_assinaturas_produtos_ia'`)

## Estrutura de Modelos

### BaseModel

A maioria dos modelos estendem de `BaseModel` que possui configurações comuns:

```php
class BaseModel extends Model
{
    use HasFactory;

    protected $guarded = ['id'];
    public $timestamps = false;
}
```

**Exceções importantes:**
- `User` estende `Authenticatable` diretamente (não BaseModel)
- `Assinatura` estende `Model` diretamente (não BaseModel)
- Modelos que precisam de timestamps customizados podem estender Model diretamente

### Propriedades Comuns
```php
// Lista de campos que podem ser preenchidos em massa
protected $fillable = ['campo1', 'campo2', 'campo3'];

// OU lista de campos que não podem ser preenchidos em massa
protected $guarded = ['id'];

// Timestamps desabilitados por padrão (configurado no BaseModel)
public $timestamps = false;

// Campos que devem ser tratados como tipos específicos
protected $casts = [
    'status' => StatusEnum::class,
    'data' => 'array',
    'valor' => 'decimal:2',
];

// Campos ocultos nas serializações
protected $hidden = ['password'];

// Campos incluídos nas serializações
protected $appends = ['nome_completo', 'data_formatada'];
```

## Chaves Primárias

As chaves primárias podem variar:
- **Padrão**: Nome da coluna `id` (INT ou BIGINT)
- **Exceções**: Algumas tabelas usam nomes customizados (ex: `id_processo` na tabela `tb_processos`)
- Configurável através da propriedade `$primaryKey` do modelo quando necessário
- Exemplo: `protected $primaryKey = 'id_processo';`

## Timestamps e Auditoria

- Por padrão, timestamps estão desabilitados (`$timestamps = false` no BaseModel)
- Modelos que precisam de timestamps devem definir `CREATED_AT` e `UPDATED_AT` customizados
- Exemplo: `Assinatura` usa `criado_em` e `atualizado_em`:
  ```php
  public const string CREATED_AT = 'criado_em';
  public const string UPDATED_AT = 'atualizado_em';
  ```
- Sistema de logs customizado através de Services de Log organizados por domínio
- Traits para formatação e preparação de dados de log

## Boas Práticas

- **Eloquent ORM**: Utilizar recursos do Eloquent para consultas e relacionamentos
- **Contexto de Escritório**: Considerar o contexto do escritório ao realizar consultas
- **Relacionamentos**: Definir métodos para relacionamentos entre modelos (hasMany, belongsTo, etc.)
- **Traits**: Utilizar traits para funcionalidades comuns (TemEscritorio, TemProcesso, TemGrupos, etc.)
- **Enums**: Utilizar enums para valores fixos (Module, StatusUsuario, TipoUsuario, etc.)
- **DTOs**: Utilizar DTOs para transferência de dados entre camadas
- **Form Requests**: Implementar validação em classes Form Request separadas, não no modelo
- **API Resources**: Usar API Resources para transformação de dados na resposta
- **Factory e Seeders**: Implementar para facilitar testes e desenvolvimento

## Exemplo Real do Fênix

### Modelo Processo
```php
<?php

namespace App\Models\Processos;

use App\Models\BaseModel;
use App\Models\Traits\Common\TemAreaProcesso;
use App\Models\Traits\Common\TemEscritorio;

class Processo extends BaseModel
{
    use TemEscritorio, TemGrupos, TemAreaProcesso, TemAdvogado, TemCliente;

    protected Module $module = Module::PROCESSOS;
    protected $table = 'tb_processos';
    protected $primaryKey = 'id_processo';

    protected function casts(): array
    {
        return [
            'id_cliente' => 'integer',
            'id_contrario' => 'integer',
            'area' => 'integer',
            'representante' => 'integer',
        ];
    }

    // Relação com área de processo
    public function area()
    {
        return $this->belongsTo(AreaDeProcesso::class, 'area');
    }
}
```

### Modelo User
```php
<?php

namespace App\Models\Usuarios;

use Illuminate\Foundation\Auth\User as Authenticatable;
use Laravel\Sanctum\HasApiTokens;
use App\Models\Traits\User\TemPermissoes;
use App\Models\Traits\Common\TemEscritorio;

class User extends Authenticatable
{
    use HasApiTokens, TemPermissoes, TemEscritorio;

    protected $table = 'tb_users_advogados';
    protected $guarded = ['id'];

    // Método estático para obter usuário atual
    public static function getCurrent(): ?self
    {
        return auth()->user();
    }
}
```

### Modelo Assinatura
```php
<?php

namespace App\Models\Assinaturas;

use Illuminate\Database\Eloquent\Model;
use App\Models\Traits\Common\TemEscritorioDeBanco;

class Assinatura extends Model
{
    use TemEscritorioDeBanco, HasFactory;

    protected $table = 'tb_assinaturas_produtos_ia';
    protected $connection = 'iturn';
    protected $guarded = ['id'];

    public const string CREATED_AT = 'criado_em';
    public const string UPDATED_AT = 'atualizado_em';

    protected function casts(): array
    {
        return [
            'banco' => BancoDeDados::class,
            'status' => StatusAtividade::class
        ];
    }
}
```
