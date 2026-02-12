# Stack Tecnológico do APP-GED

## Framework & Runtime
- **Linguagem:** PHP 8.1+
- **Framework:** Laravel 8.x
- **Arquitetura:** API REST (apenas backend)

## Backend
- **ORM:** Eloquent
- **API:** RESTful
- **Autenticação:** Laravel Sanctum
- **Jobs/Queue:** Laravel Queue (Redis)
- **Validação:** Laravel Validator
- **Logging:** Monolog
- **Caching:** Laravel Cache (Redis)
- **Eventos:** Laravel Event/Listener

## Storage & File System
- **Banco de Dados Principal:** MySQL/MariaDB
- **Cache e Queues:** Redis
- **Armazenamento de Arquivos:**
  - Local File System
  - Amazon S3
  - Azure Storage
  - Google Cloud Storage
- **Bibliotecas de Arquivo:**
  - Flysystem (Laravel)
  - Intervention Image (processamento de imagens)

## Serviços Externos
- **Filas de Mensagens:** Redis
- **Cache:** Redis
- **Armazenamento em Nuvem:** S3/Azure/GCP

## Testes
- **Testes Unitários:** PHPUnit
- **Testes de Integração:** Laravel Testing
- **Testes de API:** Laravel HTTP Tests

## Deployment & Infraestrutura
- **Containerização:** Docker
- **CI/CD:** GitHub Actions
- **Ambiente:** PHP-FPM + Nginx
- **Monitoramento:** Laravel Telescope (desenvolvimento)

## Convenções de Código
- **Estilo:** PSR-12
- **Tipagem:** Strict typing (declare(strict_types=1))
- **Documentação:** PHPDoc
- **Linting:** PHP_CodeSniffer
