# Tech Stack do 123jus-api

## Framework & Runtime
- **Linguagem:** TypeScript
- **Runtime:** Node.js
- **Framework:** Express.js 4.x
- **Arquitetura:** API REST (apenas backend)

## Backend
- **Query Builder:** Knex.js
- **API:** RESTful
- **Autenticação:** JWT (jsonwebtoken)
- **Jobs/Cron:** node-cron
- **Validação:** Validators customizados (classes TypeScript)
- **Logging:** Console.log (pode ser estendido com Winston ou similar)
- **Upload de Arquivos:** Multer, Busboy, Formidable
- **Email:** Nodemailer
- **HTTP Client:** Axios

## Storage & File System
- **Banco de Dados Principal:** MySQL/MariaDB
- **ORM/Query Builder:** Knex.js
- **Armazenamento de Arquivos:** AWS S3 (configurável para local)
- **Estratégia de Storage:** Pattern Strategy (S3StorageStrategy, LocalStorageStrategy)

## Serviços Externos e Integrações
- **AWS S3:** Armazenamento de arquivos na nuvem
- **Hubspot:** Integração para CRM (@hubspot/api-client)
- **Brasil API:** Consulta de feriados nacionais

## Testes
- **Framework de Testes:** Jest
- **Testes Unitários:** Jest
- **Testes de Integração:** Jest com Supertest
- **Configuração:** ts-jest para TypeScript

## Deployment & Infraestrutura
- **Containerização:** Docker
- **Process Manager:** PM2 (ecosystem.config.js)
- **Web Server:** Nginx (quando necessário)
- **Build:** TypeScript Compiler (tsc)
- **Configuração:** Docker Compose

## Convenções de Código
- **Estilo:** TypeScript strict mode
- **Tipagem:** TypeScript strict typing
- **Documentação:** JSDoc/TypeScript comments
- **Linting:** ESLint (quando configurado)
- **Formatação:** Prettier (quando configurado)

## Arquitetura
- **Padrão:** MVC adaptado para Express.js
- **Separação de Responsabilidades:**
  - Controllers: Lógica de requisições/respostas HTTP
  - Services: Lógica de negócio
  - Repositories: Acesso a dados
  - Validators: Validação de entrada
  - Middlewares: Autenticação, autorização e processamento de requisições

## Banco de Dados
- **Sistema:** MySQL/MariaDB
- **Migrações:** Sistema customizado com timestamps
- **Isolamento de Dados:** Por tipo de usuário (Admin, Operador, Profissional, Solicitante)
- **Convenções:** Tabelas e colunas em português, snake_case

## Segurança
- **Autenticação:** JWT (JSON Web Tokens)
- **Autorização:** Middlewares de controle de acesso por tipo de usuário
- **Proteção de Dados:** Conformidade com LGPD
- **Validação:** Validators customizados para entrada de dados

## Integrações
- **CRM:** Hubspot API para sincronização de dados
- **Storage:** AWS S3 para armazenamento de arquivos
- **APIs Externas:** Brasil API para consulta de feriados

## Monitoramento e Logs
- **Logging:** Console.log (extensível)
- **Histórico:** Sistema de logs de histórico de propostas
- **Auditoria:** Logs de envio de emails e ações de usuários
- **Rastreamento:** Logs de mudanças de status e dispositivos
