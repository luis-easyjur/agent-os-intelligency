# Padrões e Convenções de Endpoints de API

## Boas Práticas

- **Design RESTful**: Seguir princípios REST com URLs baseadas em recursos claras e métodos HTTP apropriados (GET, POST, PUT, PATCH, DELETE)
- **Nomenclatura Consistente**: Usar convenções de nomenclatura consistentes, em minúsculas, com hífen ou underscore para endpoints em toda a API
- **Versionamento**: Implementar estratégia de versionamento de API (caminho da URL ou headers) para gerenciar mudanças que quebram compatibilidade sem perturbar clientes existentes
- **Substantivos no Plural**: Usar substantivos no plural para endpoints de recursos (ex: `/users`, `/products`) para consistência
- **Recursos Aninhados**: Limitar profundidade de aninhamento a 2-3 níveis no máximo para manter URLs legíveis e manuteníveis
- **Parâmetros de Query**: Usar parâmetros de query para filtragem, ordenação, paginação e busca ao invés de criar endpoints separados
- **Códigos de Status HTTP**: Retornar códigos de status HTTP apropriados e consistentes que reflitam com precisão a resposta (200, 201, 400, 404, 500, etc.)
- **Headers de Rate Limiting**: Incluir informações de rate limit em headers de resposta para ajudar clientes a gerenciar seu uso
