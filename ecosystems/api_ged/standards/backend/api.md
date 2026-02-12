# Padrões e Convenções de Endpoints de API para o APP-GED

## Contexto do Microserviço

O APP-GED atua como um microserviço para o projeto principal APP, fornecendo diversas funcionalidades através de APIs:

1. **Gerenciamento de Arquivos**: Upload, download, armazenamento e manipulação de documentos digitais
2. **Processamento em Massa**: Operações em lote para diferentes tipos de recursos
3. **Integração com Sistemas Externos**: Webhooks e APIs para comunicação com outros sistemas
4. **Gerenciamento de Usuários e Permissões**: Autenticação e autorização de usuários

## Boas Práticas

- **Design RESTful**: Seguir princípios REST com URLs baseadas em recursos e métodos HTTP apropriados (GET, POST, PUT, PATCH, DELETE)
- **Nomenclatura Consistente**: Usar convenções de nomenclatura consistentes, em minúsculas, com hífen ou underscore para endpoints
- **Versionamento**: Implementar estratégia de versionamento de API para gerenciar mudanças que quebram compatibilidade
- **Substantivos no Plural**: Usar substantivos no plural para endpoints de recursos (ex: `/files`, `/roles`) para consistência
- **Recursos Aninhados**: Limitar profundidade de aninhamento a 2-3 níveis para manter URLs legíveis
- **Parâmetros de Query**: Usar parâmetros de query para filtragem, ordenação, paginação e busca
- **Códigos de Status HTTP**: Retornar códigos de status HTTP apropriados (200, 201, 400, 404, 500, etc.)
- **Validação**: Implementar validação de entrada usando Form Requests do Laravel
- **Middleware**: Usar middleware para autenticação e verificação de permissões
- **API Resources**: Usar API Resources para transformação de dados consistente

## Endpoints Principais do APP-GED

### Gerenciamento de Arquivos

- **GET /api/files** - Listar arquivos
- **GET /api/files/{id}** - Obter detalhes de um arquivo específico
- **POST /api/files** - Fazer upload de um novo arquivo
- **DELETE /api/files/{id}** - Excluir um arquivo
- **POST /api/files/download** - Download de um arquivo
- **POST /api/files/migration** - Migrar arquivos entre sistemas
- **GET /api/files/storage-size/{companyId}** - Obter tamanho do armazenamento de uma empresa
- **DELETE /api/files/delete-for-office** - Excluir arquivos de um escritório
- **DELETE /api/files/mass/delete-ged-files** - Excluir múltiplos arquivos GED

### Arquivos Estáticos

- **POST /api/static** - Armazenar arquivo estático
- **DELETE /api/static** - Excluir arquivo estático
- **POST /api/static/tmp** - Armazenar arquivo temporário
- **POST /api/static/tmp/drop** - Remover arquivo temporário
- **POST /api/static/credentials/google** - Armazenar credenciais do Google
- **GET /api/static/credentials/google** - Obter credenciais do Google
- **DELETE /api/static/credentials/google** - Remover credenciais do Google

### Processamento em Massa

- **POST /api/mass-billing** - Criar faturamento em massa
- **GET /api/mass-billing/{id}** - Obter detalhes de um faturamento em massa
- **POST /api/mass-timesheet** - Criar timesheet em massa
- **GET /api/mass-timesheet/disapprove/list/{id}** - Listar timesheets reprovados
- **POST /api/mass-timesheet/disapprove** - Reprovar timesheets em massa
- **POST /api/mass-cancel-revenue** - Cancelar receitas em massa
- **POST /api/solucionare/push-lawsuit/mass** - Processar ações judiciais em massa

### Relatórios e Gestão

- **POST /api/report/print** - Gerar relatório para impressão
- **POST /api/report-management/create** - Criar gerenciamento de relatório
- **POST /api/disable-office** - Desativar escritório
- **GET /api/disable-office/{id}** - Obter detalhes de desativação de escritório

### Autenticação e Segurança

- **POST /api/auth/login** - Autenticação de usuário
- **POST /api/auth/logout** - Encerrar sessão
- **POST /api/auth/refresh** - Renovar token
- **GET /api/auth/me** - Obter dados do usuário atual

### Usuários e Permissões

- **GET /api/user** - Obter usuário autenticado
- **GET /api/user/{user}/permissions** - Obter permissões do usuário
- **POST /api/user/{user}/permissions/{permission}** - Conceder permissão ao usuário
- **DELETE /api/user/{user}/permissions/{permission}** - Revogar permissão do usuário
- **GET /api/user/{user}/roles** - Obter papéis do usuário
- **POST /api/user/{user}/roles/{role}** - Atribuir papel ao usuário
- **DELETE /api/user/{user}/roles/{role}** - Remover papel do usuário
- **GET /api/roles** - Listar papéis
- **POST /api/roles** - Criar papel
- **GET /api/roles/{role}/permissions** - Obter permissões do papel
- **POST /api/roles/{role}/permissions/{permission}** - Conceder permissão ao papel
- **DELETE /api/roles/{role}/permissions/{permission}** - Revogar permissão do papel

### Webhooks e Integrações

- **POST /api/webhook/asaas/{database}/{office}/{action}** - Webhook para integração com Asaas
