# Arquitetura do 123jus-api

## Visão Geral

O 123jus-api é um sistema jurídico Node.js/TypeScript focado em gestão de propostas, orientações, pendencias, demandas e profissionais. A arquitetura é orientada a API REST e segue o padrão MVC adaptado para Express.js.

## Características Principais

- **Sistema Jurídico**: Plataforma para gestão de propostas, demandas e profissionais jurídicos
- **Apenas Backend**: Não possui frontend, apenas APIs para serem consumidas pelo projeto frontend (123jus-ui)
- **Node.js/TypeScript**: Utiliza Node.js com TypeScript para type safety e melhor desenvolvimento
- **Express.js**: Framework web minimalista e flexível para APIs REST
- **API REST**: Todas as funcionalidades são expostas via API REST

## Arquitetura MVC Adaptada para Express.js

O projeto segue uma arquitetura MVC adaptada para Express.js, com separação clara de responsabilidades:

### Controladores (Controllers)
- Localizados em `src/api/controllers/`
- Cada controlador é responsável por um recurso específico (PropostaController, OrientacaoController, PendenciaController, etc.)
- Seguem o padrão REST para endpoints API
- Utilizam Services para lógica de negócio
- Retornam respostas padronizadas usando SuccessReturn e ErrorReturn

### Tipos (Types/Interfaces)
- Localizados em `src/types/`
- Definem interfaces TypeScript para entidades de domínio (IProposta, IOrientacao, IPendencia, etc.)
- Garantem tipagem forte em toda a aplicação
- Representam estruturas de dados e contratos entre camadas

### Repositórios (Repositories)
- Localizados em `src/api/repositories/`
- Implementam o padrão Repository para abstrair a camada de persistência
- Utilizam Knex.js para queries ao banco de dados
- BaseRepositories como classe base com operações CRUD comuns
- Permitem substituir implementações sem afetar os controladores

### Serviços (Services)
- Localizados em `src/api/services/`
- Organizados por domínio (PropostaService, OrientacaoService, PendenciaService, etc.)
- Contêm a lógica de negócio principal
- São utilizados pelos controladores para processar as solicitações
- Classes de serviço específicas para operações complexas

### Validators
- Localizados em `src/api/validators/`
- Classes de validação para entrada de dados
- Validam regras de negócio e integridade de dados
- Retornam erros padronizados quando validação falha

## Estrutura de Diretórios

### src/
Core do aplicativo Node.js/TypeScript
- `api/controllers/` - Controladores API
  - `PropostaController.ts` - Gestão de propostas e demandas
  - `OrientacaoController.ts` - Gestão de orientações
  - `PendenciaController.ts` - Gestão de pendencias
  - `ComentarioController.ts` - Gestão de comentários
  - `UsuarioController.ts` - Gestão de usuários/profissionais
  - `OperadorController.ts` - Gestão de operadores
  - `EscritorioSolicitanteController.ts` - Gestão de escritórios solicitantes
- `api/repositories/` - Repositórios de acesso a dados
  - `BaseRepositories.ts` - Classe base com operações CRUD
  - `PropostaRepositories.ts` - Acesso a dados de propostas
  - `OrientacaoRepositories.ts` - Acesso a dados de orientações
  - `Connection.ts` - Configuração do Knex.js
- `api/services/` - Serviços de negócio organizados por domínio
  - `PropostaService.ts` - Lógica de negócio de propostas
  - `OrientacaoService.ts` - Lógica de negócio de orientações
  - `PendenciaService.ts` - Lógica de negócio de pendencias
  - `StorageService.ts` - Serviço de armazenamento de arquivos (S3/Local)
- `api/validators/` - Validadores de entrada
  - `PropostaValidator.ts` - Validação de propostas
  - `OrientacaoValidator.ts` - Validação de orientações
  - `ValidatorDefault.ts` - Validações comuns
- `api/middlewares/` - Middlewares Express
  - `auth.ts` - Middleware de autenticação JWT
  - `acessoAdmin.ts` - Middleware de acesso administrativo
  - `acessoOperadorAdmin.ts` - Middleware de acesso operador/admin
- `types/` - Interfaces e tipos TypeScript
  - `IProposta.ts` - Interface de proposta
  - `IOrientacao.ts` - Interface de orientação
  - `IPendencia.ts` - Interface de pendencia
  - `IUsuario.ts` - Interface de usuário
- `routes/` - Definições de rotas
  - `index.ts` - Agregador de rotas
  - `Proposta.routes.ts` - Rotas de propostas
  - `Orientacao.routes.ts` - Rotas de orientações
  - `Login.routes.ts` - Rotas de autenticação
- `helpers/` - Funções auxiliares
  - `serviceDefault/` - Helpers de resposta (SuccessReturn, ErrorReturn)
  - `email/` - Serviço de email e templates
  - `uploads/` - Helpers de upload de arquivos
- `config/` - Arquivos de configuração
  - `config.ts` - Configurações da aplicação
- `jobs/` - Jobs e tarefas agendadas
  - `CronJobs.ts` - Configuração de jobs cron
- `app.ts` - Configuração do Express
- `server.ts` - Ponto de entrada da aplicação

### migrations/
Migrações de banco de dados
- Arquivos JavaScript com timestamp para versionamento do banco
- Executados via `node migration.js up`

### mcp/
Servidor MCP (Model Context Protocol)
- `mysql/` - Servidor MCP para consultas MySQL

## Padrões de Código

- **TypeScript Strict Mode**: Usar modo strict do TypeScript
- **SOLID**: Aplicar princípios SOLID
- **Clean Code**: Nomes descritivos, funções pequenas, responsabilidade única
- **Type Safety**: Usar tipagem forte do TypeScript em todas as camadas
- **Dependency Injection**: Injetar dependências ao invés de instanciar diretamente
- **Testes**: Cobrir funcionalidades principais com testes automatizados (Jest)

## Funcionalidades Principais

### Gestão de Propostas e Demandas
- Cadastro e gestão de propostas
- Conversão de propostas em demandas
- Fluxo de status (Pendente, Em Triagem, Em Contratação, Em Execução, Finalizada, etc.)
- Atribuição de profissionais
- Gestão de prazos e datas

### Gestão de Orientações
- Criação e edição de orientações
- Vinculação de orientações a propostas
- Status de orientações (Aguardando Ciência, Ciente)
- Upload de arquivos de orientação

### Gestão de Pendencias
- Criação e resolução de pendencias
- Status de pendencias (sem pendencia, resolvida, a resolver)
- Atribuição de responsáveis
- Sistema Kanban para pendencias

### Gestão de Profissionais e Operadores
- Cadastro de profissionais (usuários)
- Cadastro de operadores
- Gestão de planos e precificações
- Atribuição de profissionais a propostas

### Gestão de Arquivos
- Upload de arquivos de proposta
- Upload de arquivos de orientação
- Armazenamento em AWS S3 ou local
- Gestão de anexos e documentos

### Sistema de Comentários
- Comentários em propostas
- Histórico de interações
- Logs de ações

### Integrações
- Hubspot (CRM)
- AWS S3 (armazenamento de arquivos)
- Brasil API (feriados nacionais)

### Segurança e Auditoria
- Autenticação via JWT
- Tipos de usuário: Admin, Operador, Profissional, Solicitante
- Middlewares de autorização
- Logs de histórico de propostas
- Logs de envio de emails
