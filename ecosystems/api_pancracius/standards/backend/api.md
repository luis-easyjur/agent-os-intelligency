# Padrões e Convenções de Endpoints de API para o Fênix

## Contexto do Sistema

O Fênix é um sistema jurídico que fornece diversas funcionalidades através de APIs:

1. **Gestão de Processos Jurídicos**: Cadastro, consulta e gestão de processos, áreas de processo e tipos de ação
2. **Gestão de Assinaturas**: Assinaturas de produtos, pacotes de petições e consumo de pacotes
3. **Gestão de Documentos**: Modelos de documentos, tipos de modelos e insert prévio
4. **Gestão Financeira**: Contratos, receitas, faturas e notas fiscais
5. **Integrações**: Webhooks e APIs para comunicação com sistemas externos (Hubspot, Asaas, Google, Discord)
6. **Autenticação e Autorização**: Login tradicional e via Google, gestão de usuários e permissões

## Boas Práticas

- **Design RESTful**: Seguir princípios REST com URLs baseadas em recursos e métodos HTTP apropriados (GET, POST, PUT, PATCH, DELETE)
- **Nomenclatura Consistente**: Usar convenções de nomenclatura consistentes, em minúsculas, com hífen ou underscore para endpoints
- **Versionamento**: Implementar estratégia de versionamento de API para gerenciar mudanças que quebram compatibilidade
- **Substantivos no Plural**: Usar substantivos no plural para endpoints de recursos (ex: `/assinaturas`, `/processos`) para consistência
- **Recursos Aninhados**: Limitar profundidade de aninhamento a 2-3 níveis para manter URLs legíveis
- **Parâmetros de Query**: Usar parâmetros de query para filtragem, ordenação, paginação e busca
- **Códigos de Status HTTP**: Retornar códigos de status HTTP apropriados (200, 201, 400, 404, 500, etc.)
- **Validação**: Implementar validação de entrada usando Form Requests do Laravel
- **Middleware**: Usar middleware para autenticação e verificação de permissões
- **API Resources**: Usar API Resources para transformação de dados consistente

## Endpoints Principais do Fênix

### Autenticação

- **POST /api/auth/login** - Autenticação de usuário
- **POST /api/auth/login/google** - Autenticação via Google
- **GET /api/auth/me** - Obter dados do usuário atual (requer autenticação)

### Processos Jurídicos

- **GET /api/processos/areas** - Listar áreas de processo

### Assinaturas

- **POST /api/assinaturas** - Criar nova assinatura
- **GET /api/assinaturas/existe** - Verificar se assinatura existe
- **POST /api/assinaturas/sync/{produto}** - Sincronizar assinatura
- **GET /api/assinaturas/{assinatura}/timeline** - Obter timeline de uma assinatura
- **GET /api/assinaturas/{assinatura}/pacotes** - Listar pacotes de uma assinatura
- **POST /api/assinaturas/{assinatura}/pacotes** - Criar novo pacote
- **PUT /api/assinaturas/{assinatura}/pacotes/{pacote}** - Atualizar pacote
- **DELETE /api/assinaturas/{assinatura}/pacotes/{pacote}** - Excluir pacote
- **POST /api/assinaturas/{assinatura}/consome** - Registrar consumo de pacote

### Documentos

- **POST /api/documentos/insert** - Criar insert prévio de documento
- **DELETE /api/documentos/insert/{documento}** - Cancelar insert prévio

### Modelos de Documento e Petição

- **GET /api/modelosDeDocumento/peticoes** - Buscar modelos de petição
- **GET /api/modelosDeDocumento/peticoes/categorias** - Listar categorias disponíveis
- **GET /api/modelosDeDocumento/peticoes/{id_modelo_de_peticao}** - Obter detalhes de um modelo de petição
- **POST /api/modelosDeDocumento/peticoes/{id_modelo_de_peticao}/incrementa** - Incrementar ranking de uso
- **GET /api/modelosDeDocumento/preview** - Listar modelos para preview
- **GET /api/modelosDeDocumento/preview/{modelo}** - Obter preview de um modelo
- **GET /api/modelosDeDocumento/tipos** - Listar tipos de modelos de documento
- **GET /api/modelosDeDocumento/{modelo}** - Obter detalhes de um modelo de documento

### Financeiro

- **POST /api/financeiro/contratos/{contrato}/sincronizar-hubspot** - Sincronizar contrato com Hubspot
- **DELETE /api/financeiro/contratos/{idContrato}/hubspot** - Deletar contrato do Hubspot
- **POST /api/financeiro/receitas/nf/asaas/cancelar/{notaFiscal}** - Cancelar nota fiscal Asaas

### Usuários

- **GET /api/users** - Listar usuários
- **GET /api/users/autocomplete** - Autocomplete de usuários
- **GET /api/users/behavior** - Comportamento de listagem de usuários
- **PUT /api/users/{user}/habilita** - Habilitar usuário
- **PUT /api/users/{user}/desabilita** - Desabilitar usuário

### Grupos

- **GET /api/grupos** - Listar grupos
- **GET /api/grupos/autocomplete** - Autocomplete de grupos

### Agendas

- **POST /api/agenda/workflow/deslocar-data-workflow** - Deslocar data de workflow

### Feedbacks

- **POST /api/feedbacks** - Criar feedback

### Termos de Produto

- **GET /api/termos/{produto}** - Verificar aceite de termos de produto
- **POST /api/termos/{produto}** - Registrar aceite de termos de produto

### Webhooks e Integrações

- **POST /api/webhook/hubspot** - Webhook para integração com Hubspot (definido em `routes/webhook/hubspot.php`)
- **POST /discord/interactions** - Interações do Discord (sem middleware de banco de dados)

### Outros

- **PUT /api/relatorios/excel/assistentes/{assistente_relatorio}** - Atualizar assistente de relatório Excel
- **POST /api/test/analise-ia/processar-agora** - Processar análise IA imediatamente (teste)
