# Padrões de Migrações no Fênix

## Contexto do Projeto

O Fênix utiliza o sistema de migrações nativo do Laravel para gerenciar alterações no banco de dados. As migrações estão localizadas no diretório `database/migrations/` do projeto.

## Estrutura de Migrations

As migrações seguem a estrutura padrão do Laravel:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateProcessosTable extends Migration
{
    public function up(): void
    {
        Schema::create('tb_processos', function (Blueprint $table) {
            $table->id('id_processo');
            $table->unsignedBigInteger('id_empresa')->nullable();
            $table->string('numero', 40);
            $table->integer('area');
            $table->text('observacoes');
            // outros campos
            // NOTA: Esta tabela não usa timestamps padrão
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('tb_processos');
    }
}
```

## Convenções de Nomenclatura

1. **Nomes de Arquivos**: Seguir padrão do Laravel com timestamp - `YYYY_MM_DD_HHMMSS_nome_da_migration.php`
2. **Classes**: Nome da classe em PascalCase, descrevendo a ação - `CreateProcessosTable`, `AddColumnToAssinaturasTable`
3. **Tabelas**: **Todas as tabelas devem ter prefixo `tb_`** - `tb_processos`, `tb_assinaturas_produtos_ia`, `tb_contrato`
4. **Colunas**: Nome em português ou inglês, snake_case - `id_empresa`, `id_escritorio`, `numero`
5. **Chaves Primárias**: Geralmente `id`, mas podem variar (ex: `id_processo` na tabela `tb_processos`)

## Boas Práticas

- **Método up() e down()**: Sempre implementar ambos os métodos para permitir rollback
- **Migrações Atômicas**: Cada migração deve fazer uma única alteração lógica
- **Documentação de Propósito**: Adicionar comentários explicando o propósito da migração
- **Foreign Keys**: Utilizar chaves estrangeiras para manter integridade referencial
- **Índices**: Adicionar índices em colunas frequentemente consultadas, especialmente `id_empresa`
- **Soft Delete**: Usar `$table->softDeletes()` para tabelas que requerem exclusão lógica
- **Transações**: Utilizar transações para migrações complexas
- **Contexto de Escritório**: Tabelas que armazenam dados de escritórios devem ter campos de relacionamento como `id_empresa` ou `id_escritorio`
- **Timestamps**: Por padrão não usar timestamps, apenas quando necessário

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
