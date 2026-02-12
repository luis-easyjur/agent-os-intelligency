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

## Estrutura do Banco de Dados

O projeto utiliza MySQL/MariaDB com arquitetura multi-tenant baseada em empresas.

### Arquitetura Multi-Tenant

O sistema é multi-tenant baseado em empresas. Quase todas as tabelas possuem o campo `id_empresa` que identifica a empresa proprietária dos dados.

**Regra crítica**: Sempre filtrar por `id_empresa` em todas as consultas.

#### Por que é obrigatório filtrar por `id_empresa`?

1. **Segurança**: Garante isolamento de dados entre empresas
2. **Contexto**: O sistema sempre opera no contexto de uma empresa específica
3. **Integridade**: Previne vazamento de dados entre empresas
4. **Performance**: Consultas filtradas são mais eficientes

#### O que sempre fazer em consultas

- Sempre incluir `WHERE id_empresa = [valor]` ou `WHERE tabela.id_empresa = [valor]` em queries SELECT
- Sempre verificar se a tabela possui `id_empresa` antes de consultar
- Sempre usar JOINs com filtro de `id_empresa` quando consultar múltiplas tabelas
- Sempre considerar o contexto da empresa ao gerar queries

#### Exemplos de consultas corretas

```sql
-- Consulta com filtro de empresa (usando QueryBuilder ou functions.php)
SELECT * FROM tb_proposta WHERE id_empresa = 123;

-- JOIN com filtro de empresa
SELECT p.*, u.nome 
FROM tb_proposta p
INNER JOIN tb_users_advogados u ON p.responsavel = u.id
WHERE p.id_empresa = 123 AND u.id_empresa = 123;

-- Consulta com múltiplas condições incluindo empresa
SELECT * FROM tb_processos 
WHERE id_empresa = 123 
  AND status = 1
  AND data_cadastro >= '2024-01-01';
```

#### O que nunca fazer

- Nunca consultar sem filtrar por `id_empresa` (exceto tabelas que não possuem esse campo)
- Nunca retornar dados de múltiplas empresas em uma única consulta sem filtro explícito
- Nunca assumir que uma consulta está no contexto correto sem verificar o `id_empresa`

### Tabelas Principais

**Todas as tabelas começam com o prefixo `tb_`.**

#### Tabelas de Usuários e Acesso
- `tb_users_advogados` (id) - Usuários do sistema
- `tb_escritorio_advogados` (id) - Escritórios/empresas

#### Tabelas de Processos
- `tb_processos` (id_processo) - Processos jurídicos principais
- `tb_processos_incidentes` (id) - Incidentes dos processos
- `tb_processos_partes` (id) - Partes envolvidas nos processos
- `tb_andamentos_contencioso` (id) - Andamentos de processos contenciosos
- `tb_andamentos_consultivos` (id) - Andamentos de processos consultivos

#### Tabelas de Pessoas
- `tb_pessoas_advogados` (id) - Pessoas (clientes, advogados, terceiros)
- `tb_pessoas_vinculadas` (id) - Vínculos entre pessoas

#### Tabelas de Agenda
- `tb_agenda_advogados` (id) - Agendamentos e eventos
- `tb_agenda_subtipo` (id) - Subtipos de eventos
- `tb_agenda_envolvidos` (id) - Pessoas envolvidas em eventos

#### Tabelas Financeiras
- `tb_receitas` (id) - Receitas
- `tb_despesas` (id) - Despesas
- `tb_fatura` (id) - Faturas
- `tb_boletos` (id) - Boletos
- `tb_banco_advogados` (id) - Contas bancárias

#### Tabelas de Contratos
- `tb_contrato` (id) - Contratos
- `tb_contrato_regra` (id) - Regras de contrato
- `tb_contrato_envolvidos` (id) - Envolvidos nos contratos

#### Tabelas de Oportunidades
- `tb_oportunidade_advogados` (id) - Oportunidades de negócio
- `tb_proposta` (id) - Propostas comerciais

#### Tabelas de Logs e Auditoria
- `tb_logs` (id) - Logs gerais do sistema
- `tb_notifications` (id) - Notificações do sistema

### Padrões de Nomenclatura

- **Todas as tabelas começam com `tb_`**
- Chaves primárias são simplesmente `id` (INT ou BIGINT), não `id_[nome_tabela]`
- Campos de data variam: `data_cadastro`, `data_vencimento`, `criado_em`, etc (DATE, DATETIME ou TIMESTAMP)
- Status geralmente em campos `status` (VARCHAR)
- **Este banco NÃO utiliza foreign keys** - relacionamentos são mantidos apenas logicamente

### Tabelas que Possuem `id_empresa`

Quase todas as tabelas do sistema possuem `id_empresa` (BIGINT), incluindo:
- `tb_users_advogados`, `tb_escritorio_advogados`
- `tb_processos`, `tb_processos_incidentes`, `tb_processos_partes`
- `tb_pessoas_advogados`, `tb_agenda_advogados`
- `tb_receitas`, `tb_despesas`, `tb_fatura`
- `tb_contrato`, `tb_oportunidade_advogados`, `tb_proposta`
- `tb_logs`, `tb_notifications`

## Ferramentas de Acesso ao Banco

Para interações com o banco de dados, utilize uma das seguintes opções, de acordo com o contexto e os padrões já utilizados no local do código:

### QueryBuilder

A classe `QueryBuilder`, localizada em `database/QueryBuilder.php`, deve ser usada para consultas mais complexas ou estruturadas, que exigem maior flexibilidade e reutilização.

### Functions.php

As funções utilitárias disponíveis em `database/functions.php` são recomendadas para operações mais simples ou pontuais.

### Regra Geral

Evite implementar lógicas de acesso ao banco fora dessas abordagens. Escolha a opção mais adequada para manter a consistência e a organização do projeto.
