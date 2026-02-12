# Padrões de Banco de Dados

## Regra Fundamental de Segurança

**NUNCA executar scripts ou comandos que alterem dados ou estrutura do banco de dados diretamente.**

A IA deve apenas executar operações de leitura (SELECT, DESCRIBE, SHOW, EXPLAIN).

## Operações Permitidas

- Executar queries SELECT para consultar dados
- Criar scripts de leitura/consulta (SELECT) e executá-los
- Verificar estrutura de tabelas (DESCRIBE, SHOW)
- Consultar informações do banco (SHOW TABLES, SHOW DATABASES)
- Explicar o que as queries fazem
- Usar comandos MCP `bd` e `execute_query` para consultas

## Operações Proibidas

- Executar UPDATE, INSERT, DELETE diretamente
- Executar ALTER TABLE, CREATE TABLE, DROP TABLE
- Criar e executar scripts que modifiquem dados ou estrutura
- Usar ferramentas de terminal para alterar o banco
- Executar qualquer comando DDL ou DML que altere dados
- Executar migrações que alterem estrutura
- Modificar constraints, índices, ou relacionamentos

## Tratamento de Solicitações de Alteração

Quando o usuário solicitar alterações no banco:

1. Lembrar que não é possível fazer isso diretamente
2. Fornecer a query SQL completa e comentada
3. Explicar o que a query faz
4. Deixar o usuário executar manualmente
5. Alertar sobre possíveis impactos (backup, transações, etc)

## Estrutura do Banco de Dados do APP-GED

O APP-GED utiliza MySQL/MariaDB com um banco de dados próprio, separado do projeto principal, focado no armazenamento de metadados de arquivos e controle de filas de processamento.

### Arquitetura do Banco de Dados

O sistema mantém isolamento de dados por empresa (multi-tenant), com quase todas as tabelas possuindo o campo `company_id` que identifica a empresa proprietária dos dados.

**Regra crítica**: Sempre filtrar por `company_id` em todas as consultas.

#### Por que é obrigatório filtrar por `company_id`?

1. **Segurança**: Garante isolamento de dados entre empresas
2. **Contexto**: O sistema sempre opera no contexto de uma empresa específica
3. **Integridade**: Previne vazamento de dados entre empresas
4. **Performance**: Consultas filtradas são mais eficientes

#### O que sempre fazer em consultas

- Sempre incluir `WHERE company_id = [valor]` ou `WHERE tabela.company_id = [valor]` em queries SELECT
- Sempre verificar se a tabela possui `company_id` antes de consultar
- Sempre usar JOINs com filtro de `company_id` quando consultar múltiplas tabelas
- Sempre considerar o contexto da empresa ao gerar queries

#### Exemplos de consultas corretas

```sql
-- Consulta com filtro de empresa
SELECT * FROM files WHERE company_id = 123;

-- JOIN com filtro de empresa
SELECT f.*, q.status 
FROM files f
INNER JOIN queue_items q ON f.id = q.file_id
WHERE f.company_id = 123 AND q.company_id = 123;

-- Consulta com múltiplas condições incluindo empresa
SELECT * FROM files 
WHERE company_id = 123 
  AND status = 'active'
  AND created_at >= '2024-01-01';
```

#### O que nunca fazer

- Nunca consultar sem filtrar por `company_id` (exceto tabelas que não possuem esse campo)
- Nunca retornar dados de múltiplas empresas em uma única consulta sem filtro explícito
- Nunca assumir que uma consulta está no contexto correto sem verificar o `company_id`

### Tabelas Principais do APP-GED

#### Tabelas de Arquivos
- `files` (id) - Metadados de arquivos armazenados

#### Tabelas de Processamento em Massa
- `mass_billings` (id) - Faturamentos em massa
- `billing_actions` (id) - Ações de faturamento
- `mass_billing_emails` (id) - Emails relacionados a faturamentos em massa
- `mass_timesheets` (id) - Timesheets em massa
- `timesheet_actions` (id) - Ações de timesheet
- `mass_disapprove_timesheets` (id) - Reprovação de timesheets em massa
- `disapprove_timesheet_actions` (id) - Ações de reprovação de timesheet
- `mass_cancel_revenues` (id) - Cancelamento de receitas em massa
- `cancel_revenue_actions` (id) - Ações de cancelamento de receita

#### Tabelas de Integração
- `mass_push_lawsuits` (id) - Envio em massa de processos
- `manage_push_lawsuit` (id) - Gerenciamento de envio de processos
- `disable_offices` (id) - Desativação de escritórios
- `report_management` (id) - Gerenciamento de relatórios

#### Tabelas de Autenticação e Autorização
- `users` (id) - Usuários do sistema
- `permissions` (id) - Permissões
- `roles` (id) - Papéis (roles)
- `role_has_permissions` - Relação entre papéis e permissões
- `model_has_roles` - Relação entre modelos e papéis
- `model_has_permissions` - Relação entre modelos e permissões

#### Tabelas de Sistemas Integrados
- `solucionare_name_logins` (id) - Logins para o sistema Solucionare
- `login_send_emails` (id) - Emails enviados para login

#### Tabelas de Logs e Auditoria
- `audits` (id) - Logs de auditoria de modelos
- `failed_jobs` (id) - Trabalhos falhos
- `personal_access_tokens` (id) - Tokens de acesso pessoal

### Padrões de Nomenclatura

- **Tabelas em inglês e no plural**
- Chaves primárias são simplesmente `id` (INT ou BIGINT)
- Campos de data seguem padrão: `created_at`, `updated_at`, `deleted_at` (TIMESTAMP)
- Status geralmente em campos `status` (VARCHAR ou ENUM)
- **Este banco utiliza foreign keys** para garantir integridade referencial

### Tabelas que Possuem `company_id`

A maioria das tabelas do sistema possuem `company_id` (BIGINT), incluindo:
- `files`
- `mass_billings`, `billing_actions`, `mass_billing_emails`
- `mass_timesheets`, `timesheet_actions`
- `mass_disapprove_timesheets`, `disapprove_timesheet_actions`
- `mass_cancel_revenues`, `cancel_revenue_actions`
- `mass_push_lawsuits`, `manage_push_lawsuit`
- `disable_offices`
- `report_management`

## Ferramentas de Acesso ao Banco de Dados no Laravel

Para interações com o banco de dados, o APP-GED utiliza os recursos nativos do Laravel:

### Eloquent ORM

O Eloquent ORM é a principal forma de interação com o banco de dados. Exemplos:

```php
// Buscar todos os arquivos de uma empresa
$files = File::where('company_id', $companyId)->get();

// Criar um novo arquivo
$file = File::create([
    'company_id' => $companyId,
    'user_id' => $userId,
    'filename' => $filename,
    // outros campos
]);
```

### Query Builder

Para consultas mais complexas, o Query Builder do Laravel oferece maior flexibilidade:

```php
// Consulta com joins e condições complexas
$results = DB::table('files')
    ->join('mass_billings', 'files.model_id', '=', 'mass_billings.id')
    ->where('files.company_id', $companyId)
    ->where('files.model_type', 'App\\Models\\MassBilling')
    ->select('files.*', 'mass_billings.status')
    ->get();
```

### Raw Queries

Em casos específicos onde a performance é crítica, queries SQL diretas podem ser utilizadas:

```php
$results = DB::select(
    'SELECT f.*, mb.status 
     FROM files f 
     JOIN mass_billings mb ON f.model_id = mb.id 
     WHERE f.company_id = ? AND f.model_type = ?',
    [$companyId, 'App\\Models\\MassBilling']
);
```

### Repositories

O padrão Repository é implementado em algumas partes do sistema para abstrair a lógica de acesso a dados:

```php
// Exemplo de uso de um repositório
$fileRepository = app(FileRepository::class);
$files = $fileRepository->findByCompany($companyId);
```
