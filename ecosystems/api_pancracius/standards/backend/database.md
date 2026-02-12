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

## Estrutura do Banco de Dados do Fênix

O Fênix utiliza MySQL/MariaDB com suporte a múltiplas conexões de banco de dados. A conexão padrão é `iturn` (banco `advogados_iturn`), mas o sistema suporta outras conexões como `oabes`. O sistema é focado em gestão jurídica, com tabelas para processos, assinaturas, documentos, financeiro e integrações.

**Importante**: Alguns modelos podem especificar explicitamente a conexão usando `protected $connection = 'iturn';`

### Arquitetura do Banco de Dados

O sistema mantém isolamento de dados por escritório, com muitas tabelas possuindo campos de relacionamento como `id_empresa` ou `id_escritorio` que identificam o escritório proprietário dos dados.

**Regra importante**: Sempre considerar o contexto do escritório ao realizar consultas, especialmente em operações que envolvem dados sensíveis.

#### Contexto de Escritório

1. **Segurança**: Garante isolamento de dados entre escritórios
2. **Contexto**: O sistema sempre opera no contexto de um escritório específico
3. **Integridade**: Previne vazamento de dados entre escritórios
4. **Performance**: Consultas filtradas são mais eficientes

#### O que sempre fazer em consultas

- Verificar se a tabela possui campos de relacionamento com escritório antes de consultar
- Usar JOINs com filtros apropriados quando consultar múltiplas tabelas
- Considerar o contexto do escritório ao gerar queries
- Utilizar os métodos do modelo User para obter o contexto atual quando necessário

#### Exemplos de consultas corretas

```sql
-- Consulta de processos com filtro de escritório
SELECT * FROM tb_processos WHERE id_empresa = 123;

-- JOIN com filtro de escritório
SELECT p.*, a.nome as area_nome
FROM tb_processos p
INNER JOIN tb_processos_areas a ON p.area = a.id
WHERE p.id_empresa = 123;

-- Consulta com múltiplas condições
SELECT * FROM tb_assinaturas_produtos_ia 
WHERE id_empresa = 123 
  AND status = 'ATIVO';
```

### Tabelas Principais do Fênix

**IMPORTANTE: Todas as tabelas têm prefixo `tb_`**

#### Tabelas de Processos Jurídicos
- `tb_processos` (id_processo) - Processos jurídicos
- `tb_processos_areas` (id) - Áreas de processo
- `tb_processos_tipo_acao` (id) - Tipos de ação de processo
- `tb_indices_taxas` (id) - Índices e taxas para correção monetária

#### Tabelas de Assinaturas
- `tb_assinaturas_produtos_ia` (id) - Assinaturas de produtos
- `tb_pacotes_produtos_ia` (id) - Pacotes de petições
- `tb_consumo_tokens_produtos_ia` (id) - Consumo de pacotes
- `tb_produtos_ia` (id) - Produtos disponíveis
- `tb_termos_produtos_beta` (id) - Aceite de termos de produtos

#### Tabelas de Documentos
- `tb_documentos` (id) - Documentos
- `tb_modelodocumentos` (id) - Modelos de documentos
- `tb_modelodocumentostipo` (id) - Tipos de modelos de documentos
- `tb_modelo_documentos_easyjur` (id) - Modelos de petições

#### Tabelas Financeiras
- `tb_contrato` (id) - Contratos
- `tb_contrato_envolvidos` (id) - Envolvidos nos contratos
- `tb_receitas` (id) - Receitas
- `tb_fatura` (id) - Faturas
- `tb_notas_fiscais` (id) - Notas fiscais (integração Asaas)
- `tb_asaas_accounts` (id) - Contas Asaas

#### Tabelas de Agendas
- `tb_agenda_advogados` (id) - Agendas de processos
- `tb_feriados_advogados` (id) - Feriados

#### Tabelas de Usuários e Escritórios
- `tb_users_advogados` (id) - Usuários do sistema
- `tb_escritorio_advogados` (id) - Escritórios
- `tb_grupos_pessoas` (id) - Grupos
- `tb_cliente_grupos` - Relação entre entidades e grupos
- `tb_sessoes_usuarios` (id) - Sessões de usuários

#### Tabelas de Configurações
- `tb_config_agents` (id) - Configurações de agentes
- `tb_config_escritorios` (id) - Configurações de escritórios

#### Tabelas de Integrações
- `tb_publicacoes_pendentes` (id) - Publicações pendentes
- `tb_pub_pendentes_analise_ia` (id) - Análise IA de publicações
- `tb_publicacoes_premium` (id) - Publicações premium
- `tb_filtros_intimacoes` (id) - Filtros de intimação
- `tb_nomes_publicacao` (id) - Nomes monitorados em publicações

#### Tabelas de Logs e Auditoria
- `tb_logs` (id) - Logs de operações
- `failed_jobs` (id) - Trabalhos falhos (tabela padrão do Laravel, sem prefixo)
- `personal_access_tokens` (id) - Tokens de acesso pessoal (tabela padrão do Laravel/Sanctum, sem prefixo)

#### Tabelas Auxiliares
- `tb_pessoas_advogados` (id) - Pessoas (clientes, advogados, etc.)
- `tb_cidades` (id) - Cidades
- `tb_modulo_grupos` (id) - Módulos do sistema
- `tb_feedback` (id) - Feedbacks

### Padrões de Nomenclatura

- **Todas as tabelas têm prefixo `tb_`**
- **Tabelas em português ou inglês, no plural**
- Chaves primárias geralmente são `id` (INT ou BIGINT), mas podem variar (ex: `id_processo`)
- Campos de data podem seguir padrões diferentes:
  - Timestamps padrão: `created_at`, `updated_at` (quando habilitados)
  - Timestamps customizados: `criado_em`, `atualizado_em`, `data_cadastro`
- Status geralmente em campos `status` (VARCHAR, INT ou ENUM)
- **Este banco utiliza foreign keys** para garantir integridade referencial

### Campos de Relacionamento com Escritório

Muitas tabelas do sistema possuem campos de relacionamento com escritório, como:
- `id_empresa` - ID do escritório/empresa
- `id_escritorio` - ID do escritório

Tabelas principais que possuem relacionamento com escritório:
- `tb_processos`, `tb_assinaturas_produtos_ia`, `tb_contrato`, `tb_receitas`
- `tb_documentos`, `tb_agenda_advogados`, `tb_users_advogados`
- E outras tabelas de domínio principal

## Ferramentas de Acesso ao Banco de Dados no Laravel

Para interações com o banco de dados, o Fênix utiliza os recursos nativos do Laravel:

### Eloquent ORM

O Eloquent ORM é a principal forma de interação com o banco de dados. Exemplos:

```php
// Buscar todos os processos de um escritório
$processos = Processo::where('id_empresa', $escritorioId)->get();

// Criar um novo processo (usando id_processo como chave primária)
$processo = Processo::create([
    'id_empresa' => $escritorioId,
    'numero' => $numero,
    'area' => $areaId,
    // outros campos
]);

// Obter usuário atual (método estático do modelo User)
$user = User::getCurrent();

// Modelos com conexão específica
$assinatura = Assinatura::on('iturn')->where('id_empresa', $escritorioId)->get();
```

### Query Builder

Para consultas mais complexas, o Query Builder do Laravel oferece maior flexibilidade:

```php
// Consulta com joins e condições complexas
$results = DB::table('tb_processos')
    ->join('tb_processos_areas', 'tb_processos.area', '=', 'tb_processos_areas.id')
    ->where('tb_processos.id_empresa', $escritorioId)
    ->select('tb_processos.*', 'tb_processos_areas.nome as area_nome')
    ->get();
```

### Raw Queries

Em casos específicos onde a performance é crítica, queries SQL diretas podem ser utilizadas:

```php
$results = DB::select(
    'SELECT p.*, a.nome as area_nome 
     FROM tb_processos p 
     JOIN tb_processos_areas a ON p.area = a.id 
     WHERE p.id_empresa = ?',
    [$escritorioId]
);
```

### Repositories

O padrão Repository é implementado em algumas partes do sistema para abstrair a lógica de acesso a dados. Repositories geralmente estendem `ModelRepository` e implementam a interface `Repository`:

```php
// Exemplo de uso de um repositório
$assinaturaRepository = app(AssinaturaRepository::class);
$assinatura = $assinaturaRepository->criaAssinatura($dadosInsert);

// Repositories podem trabalhar com múltiplas conexões de banco
$escritorioRepository = app(EscritorioRepository::class);
$escritorio = $escritorioRepository->find($id, 'iturn');
```
