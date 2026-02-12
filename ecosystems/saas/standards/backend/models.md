# Padrões de Tabelas e Estrutura de Banco de Dados

## Contexto do Projeto

Este projeto utiliza MySQL/MariaDB com acesso direto via mysqli. Não há ORM ou sistema de modelos estruturado.

## Nomenclatura de Tabelas

**Todas as tabelas do banco de dados começam com o prefixo `tb_`.**

Exemplos de tabelas reais:
- `tb_processos` - Processos jurídicos
- `tb_pessoas_advogados` - Pessoas (clientes, advogados, etc)
- `tb_agenda_advogados` - Agendamentos
- `tb_receitas` - Receitas financeiras
- `tb_despesas` - Despesas financeiras
- `tb_contrato` - Contratos
- `tb_users_advogados` - Usuários do sistema

## Chaves Primárias

As chaves primárias seguem o padrão simples `id` (INT ou BIGINT), não `id_[nome_tabela]`.

Exemplos:
- `tb_processos.id` (não `id_processo`)
- `tb_pessoas_advogados.id` (não `id_pessoa`)
- `tb_agenda_advogados.id` (não `id_agenda`)

## Boas Práticas

- **Nomenclatura de Tabelas**: Todas as tabelas devem começar com `tb_`
- **Chaves Primárias**: Usar simplesmente `id` como chave primária
- **Timestamps**: Incluir campos de data quando necessário (ex: `data_cadastro`, `data_vencimento`)
- **Multi-Tenancy**: Incluir campo `id_empresa` (BIGINT) em todas as tabelas que precisam de isolamento por empresa
- **Integridade de Dados**: Usar constraints de banco de dados (NOT NULL, UNIQUE) para impor regras no nível do banco
- **Foreign Keys**: **Este banco NÃO utiliza foreign keys**. Relacionamentos são mantidos apenas logicamente no código
- **Tipos de Dados Apropriados**: Escolher tipos de dados que correspondam ao propósito e requisitos de tamanho
- **Índices Estratégicos**: Indexar campos frequentemente consultados (especialmente `id_empresa`)
- **Status como ENUM ou VARCHAR**: Usar campos `status` com valores consistentes (VARCHAR é mais comum)
- **Evitar Super-Normalização**: Balancear normalização com necessidades práticas de performance de queries
