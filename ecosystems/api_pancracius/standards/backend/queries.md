# Padrões de Consultas ao Banco de Dados

## Boas Práticas Gerais

- **Prevenir SQL Injection**: Sempre usar prepared statements através do Query Builder ou Eloquent; nunca interpolar entrada do usuário diretamente em strings SQL
- **Evitar N+1 Queries**: Usar JOINs no QueryBuilder para buscar dados relacionados em uma única query ao invés de múltiplas queries
- **Selecionar Apenas Dados Necessários**: Solicitar apenas as colunas necessárias ao invés de usar SELECT * para melhor performance
- **Indexar Colunas Estratégicas**: Indexar colunas usadas em cláusulas WHERE, JOIN e ORDER BY para otimização de queries
- **Usar Transações para Mudanças Relacionadas**: Envolver operações de banco relacionadas em transações para manter consistência de dados
- **Definir Timeouts de Query**: Implementar timeouts para prevenir queries que impactem a performance do sistema
- **Cachear Queries Caras**: Cachear resultados de queries complexas ou executadas frequentemente quando apropriado

## Padrões Específicos do Projeto

### Contexto de Escritório

Todas as consultas devem considerar o contexto do escritório, utilizando campos de relacionamento como `id_empresa` ou `id_escritorio` quando apropriado.

### Ferramentas de Acesso

O Fênix utiliza as ferramentas nativas do Laravel para acesso ao banco de dados:

- **Eloquent ORM**: Principal forma de interação com o banco de dados
- **Query Builder**: Para consultas mais complexas quando necessário
- **Raw Queries**: Apenas em casos específicos onde a performance é crítica

**Boas Práticas:**
- Preferir Eloquent ORM sempre que possível
- Usar Query Builder para consultas complexas (JOINs, subqueries, agregações)
- Evitar raw queries SQL diretas, usar parâmetros vinculados quando necessário
- Usar prepared statements através do Query Builder ou Eloquent para prevenir SQL injection
