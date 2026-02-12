# Padrões de Consultas ao Banco de Dados

## Boas Práticas Gerais

- **Prevenir SQL Injection**: Sempre usar `db_escape_string()` ou métodos do QueryBuilder; nunca interpolar entrada do usuário diretamente em strings SQL
- **Evitar N+1 Queries**: Usar JOINs no QueryBuilder para buscar dados relacionados em uma única query ao invés de múltiplas queries
- **Selecionar Apenas Dados Necessários**: Solicitar apenas as colunas necessárias ao invés de usar SELECT * para melhor performance
- **Indexar Colunas Estratégicas**: Indexar colunas usadas em cláusulas WHERE, JOIN e ORDER BY para otimização de queries
- **Usar Transações para Mudanças Relacionadas**: Envolver operações de banco relacionadas em transações para manter consistência de dados
- **Definir Timeouts de Query**: Implementar timeouts para prevenir queries que impactem a performance do sistema
- **Cachear Queries Caras**: Cachear resultados de queries complexas ou executadas frequentemente quando apropriado

## Padrões Específicos do Projeto

### Multi-Tenancy

Todas as consultas devem incluir filtro por `id_empresa` para garantir isolamento de dados entre empresas.

### Ferramentas de Acesso

**Regra obrigatória**: Sempre usar uma das duas ferramentas abaixo, nunca escrever queries SQL diretas.

- **QueryBuilder** (`database/QueryBuilder.php`): **SEMPRE usar para queries complexas** (JOINs, subqueries, agregações, etc)
- **Functions.php** (`database/functions.php`): **SEMPRE usar para queries simples** (SELECT simples, INSERT, UPDATE, DELETE básicos)

**Nunca** implementar lógicas de acesso ao banco fora dessas abordagens. **Nunca** escrever queries SQL diretas sem usar uma dessas ferramentas.

### Escape de Strings

Sempre usar `db_escape_string()` da `database/functions.php` ao interpolar valores em queries SQL.
