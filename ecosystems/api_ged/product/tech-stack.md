# Tech Stack do APP-GED

## Framework & Runtime
- **Linguagem:** PHP 8.1+
- **Framework:** Laravel 8.x
- **Arquitetura:** API REST (apenas backend, microsserviço)
- **Tipagem:** Strict typing (declare(strict_types=1))

## Backend
- **ORM:** Eloquent (Laravel)
- **API:** RESTful
- **Autenticação:** Laravel Sanctum (tokens)
- **Jobs/Queue:** Laravel Queue (Redis como driver)
- **Validação:** Laravel Validator com Form Requests
- **Logging:** Monolog (via Laravel)
- **Caching:** Laravel Cache (Redis)
- **Eventos:** Laravel Event/Listener
- **Auditoria:** Laravel Auditing (OwenIt)
- **Permissões:** Spatie Laravel Permission

## Storage & File System
- **Banco de Dados Principal:** MySQL/MariaDB
- **Cache e Queues:** Redis
- **Armazenamento de Arquivos:**
  - Local File System
  - Amazon S3 (via league/flysystem-aws-s3-v3)
  - Azure Storage (via matthewbdaly/laravel-azure-storage)
  - Google Cloud Storage
- **Bibliotecas de Arquivo:**
  - Flysystem (Laravel)
  - Intervention Image (processamento de imagens, quando necessário)

## Serviços Externos
- **Filas de Mensagens:** Redis
- **Cache:** Redis
- **Armazenamento em Nuvem:** S3/Azure/GCP (configurável por ambiente)
- **Integrações:**
  - Webhooks (Asaas)
  - Solucionare (sistemas legados)

## Testes
- **Testes Unitários:** PHPUnit
- **Testes de Integração:** Laravel Testing
- **Testes de API:** Laravel HTTP Tests
- **Factories:** Laravel Model Factories
- **Mocks:** Mockery

## Deployment & Infraestrutura
- **Containerização:** Docker
- **CI/CD:** GitHub Actions
- **Ambiente:** PHP-FPM + Nginx
- **Monitoramento:** Laravel Telescope (desenvolvimento)
- **Documentação API:** Swagger/OpenAPI (zircote/swagger-php)

## Convenções de Código
- **Estilo:** PSR-12
- **Tipagem:** Strict typing obrigatório
- **Documentação:** PHPDoc
- **Linting:** PHP_CodeSniffer
- **Idioma do Código:** Inglês (classes, métodos, variáveis)
- **Idioma de Mensagens:** Português (erros, validações, conteúdo ao usuário)

## Padrões Arquiteturais
- **MVC:** Seguir arquitetura MVC do Laravel
- **Repository Pattern:** Abstração de acesso a dados via Repositories
- **Service Layer:** Lógica de negócio em Services
- **Dependency Injection:** Injeção de dependências via construtor
- **Form Requests:** Validação de entrada via Form Requests
- **API Resources:** Transformação de dados para resposta da API

## Bibliotecas e Pacotes Principais
- **doctrine/dbal:** Para operações avançadas de banco de dados
- **fruitcake/laravel-cors:** CORS para APIs
- **guzzlehttp/guzzle:** Cliente HTTP para integrações
- **laravel/sanctum:** Autenticação via tokens
- **laravellegends/pt-br-validator:** Validações em português brasileiro
- **owen-it/laravel-auditing:** Sistema de auditoria
- **prettus/l5-repository:** Padrão Repository
- **spatie/laravel-permission:** Gerenciamento de permissões
- **zircote/swagger-php:** Documentação Swagger

## Multi-tenancy
- **Isolamento:** Todos os dados isolados por `company_id`
- **Filtros Obrigatórios:** Sempre incluir filtro por `company_id` em consultas quando aplicável
- **Segurança:** Garantir que usuários só acessem dados de sua empresa

## Processamento Assíncrono
- **Driver de Fila:** Redis (produção), database/sync (desenvolvimento)
- **Retry:** Configurável por job (padrão: 3 tentativas)
- **Timeout:** Configurável por job
- **Monitoramento:** Tabela `failed_jobs` para rastreamento de falhas
- **Jobs Principais:**
  - MassBilling
  - MassTimesheet
  - MassCancelRevenue
  - PushLawsuitJob
  - DeleteOfficeFiles
  - MigrationFromZip
  - ReportJob
  - DisableOffice

## Segurança
- **Autenticação:** Tokens via Laravel Sanctum
- **Autorização:** Roles e Permissões (Spatie)
- **Auditoria:** Rastreamento completo de operações
- **Validação:** Validação rigorosa de entrada via Form Requests
- **Secrets:** Configuração via variáveis de ambiente (.env)

## Performance
- **Cache:** Redis para cache de dados frequentes
- **Queue:** Processamento assíncrono para operações pesadas
- **Otimizações:** Eager loading, paginação, índices de banco de dados
