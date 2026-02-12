# Padrões de Modelos e Estrutura do APP-GED

## Contexto do Projeto

O APP-GED é um microsserviço para gerenciamento de arquivos e processamento em massa, construído com Laravel e utilizando MySQL/MariaDB com Eloquent ORM.

## Modelos (Models) do Laravel

O projeto segue o padrão MVC do Laravel, onde cada tabela do banco de dados possui um modelo correspondente na pasta `app/Models`.

Modelos principais do APP-GED:
- `File` - Gerenciamento de arquivos
- `MassBilling` - Faturamento em massa
- `MassTimesheet` - Timesheet em massa
- `MassCancelRevenue` - Cancelamento de receitas em massa
- `User` - Usuários do sistema
- `Role` e `Permission` - Papéis e permissões (Spatie Permission)
- `DisableOffice` - Desativação de escritório

## Nomenclatura

### Tabelas
- Nomes em inglês e plural (padrão Laravel)
- Sem prefixos (diferente do projeto principal que usa prefixo `tb_`)
- Exemplos: `files`, `mass_billings`, `mass_timesheets`

### Modelos
- Nome no singular, PascalCase, correspondendo à tabela
- Exemplo: tabela `files` -> modelo `File`
- Exemplo: tabela `mass_billings` -> modelo `MassBilling`

## Estrutura de Modelos

### Propriedades Comuns
```php
// Lista de campos que podem ser preenchidos em massa
protected $fillable = ['campo1', 'campo2', 'campo3'];

// OU lista de campos que não podem ser preenchidos em massa
protected $guarded = ['id', 'created_at', 'updated_at'];

// Campos que devem ser tratados como tipos específicos
protected $casts = [
    'is_active' => 'boolean',
    'data' => 'array',
    'settings' => 'object',
    'started_at' => 'datetime',
];

// Campos ocultos nas serializações
protected $hidden = ['password', 'remember_token'];

// Campos incluídos nas serializações
protected $appends = ['full_name', 'formatted_date'];
```

## Chaves Primárias

As chaves primárias seguem o padrão Laravel:
- Nome da coluna: `id`
- Tipo: Geralmente `bigIncrements` (BIGINT UNSIGNED AUTO_INCREMENT)
- Configurável através da propriedade `$primaryKey` do modelo

## Timestamps e Auditoria

- Todos os modelos incluem colunas de timestamp: `created_at` e `updated_at`
- Modelos que precisam de auditoria utilizam o package `owen-it/laravel-auditing`
- Implementação de auditoria:

```php
use OwenIt\Auditing\Contracts\Auditable as InterfaceAuditable;
use OwenIt\Auditing\Auditable;

class File extends Model implements InterfaceAuditable
{
    use Auditable;
    // ...
}
```

## Boas Práticas

- **Eloquent ORM**: Utilizar recursos do Eloquent para consultas e relacionamentos
- **Isolamento Multi-tenant**: Garantir que todas as consultas filtrem por `company_id`
- **Relacionamentos**: Definir métodos para relacionamentos entre modelos (hasMany, belongsTo, etc.)
- **Form Requests**: Implementar validação em classes Form Request separadas, não no modelo
- **API Resources**: Usar API Resources para transformação de dados na resposta
- **Factory e Seeders**: Implementar para facilitar testes e desenvolvimento

## Exemplo Real do APP-GED

### Modelo File
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use OwenIt\Auditing\Contracts\Auditable as InterfaceAuditable;
use OwenIt\Auditing\Auditable;

class File extends Model implements InterfaceAuditable
{
    use HasFactory, Auditable;

    protected $guarded = ['id', 'created_at', 'updated_at'];

    // Relação com o usuário que fez upload
    public function user()
    {
        return $this->belongsTo(User::class);
    }

    // Scope para filtrar arquivos ativos
    public function scopeForCompany($query, $companyId)
    {
        return $query->where('company_id', $companyId);
    }
}
```

### Estrutura da Tabela File

```php
Schema::create('files', function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->unsignedBigInteger('company_id');
    $table->unsignedBigInteger('user_id');
    $table->unsignedBigInteger('model_id');
    $table->string('model_type', 256);
    $table->string('db_name', 256);
    $table->string('filename');
    $table->string('url');
    $table->string('mime_type');
    $table->string('extension', 10);
    $table->double('size', 8, 2);
    $table->timestamps();
});
```
