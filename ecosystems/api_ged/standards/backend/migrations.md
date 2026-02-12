# Padrões de Migrações no APP-GED

## Contexto do Projeto

O APP-GED utiliza o sistema de migrações nativo do Laravel para gerenciar alterações no banco de dados. As migrações estão localizadas no diretório `database/migrations/` do projeto.

## Estrutura de Migrations

As migrações seguem a estrutura padrão do Laravel:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateFilesTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('files', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('path');
            $table->unsignedBigInteger('company_id');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('files');
    }
}
```

## Convenções de Nomenclatura

1. **Nomes de Arquivos**: Seguir padrão do Laravel com timestamp - `YYYY_MM_DD_HHMMSS_nome_da_migration.php`
2. **Classes**: Nome da classe em PascalCase, descrevendo a ação - `CreateFilesTable`, `AddColumnToFilesTable`
3. **Tabelas**: Nomes em inglês, plural e snake_case - `files`, `queue_items`, `file_versions`
4. **Colunas**: Nome em inglês, snake_case - `company_id`, `created_at`, `file_path`

## Boas Práticas

- **Método up() e down()**: Sempre implementar ambos os métodos para permitir rollback
- **Migrações Atômicas**: Cada migração deve fazer uma única alteração lógica
- **Documentação de Propósito**: Adicionar comentários explicando o propósito da migração
- **Foreign Keys**: Utilizar chaves estrangeiras para manter integridade referencial
- **Índices**: Adicionar índices em colunas frequentemente consultadas, especialmente `company_id`
- **Soft Delete**: Usar `$table->softDeletes()` para tabelas que requerem exclusão lógica
- **Transações**: Utilizar transações para migrações complexas
- **Esquema Multi-tenant**: Todas as tabelas que armazenam dados de empresas devem ter uma coluna `company_id`

## Execução de Migrações

- **Desenvolvimento**: Rodar migrações usando `php artisan migrate`
- **Rollback**: Reverter última migração com `php artisan migrate:rollback`
- **Recriar Banco**: Em desenvolvimento, usar `php artisan migrate:fresh` quando necessário
- **Seeding**: Popular tabelas com dados iniciais usando seeders - `php artisan db:seed`

## Modificações de Esquema em Produção

- **Backup**: SEMPRE fazer backup do banco antes de executar migrações em produção
- **Downtime**: Considerar o impacto em tabelas grandes e potencial downtime
- **Testes Prévios**: Testar todas as migrações em ambiente de desenvolvimento antes
- **Comunicação**: Comunicar alterações de esquema à equipe antes de aplicar em produção
