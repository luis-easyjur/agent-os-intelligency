# Stack Tecnológico do 123jus-api

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
