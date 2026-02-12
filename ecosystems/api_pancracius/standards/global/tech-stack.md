# Stack Tecnológico do Fênix

## Framework & Runtime
- **Linguagem:** PHP 8.3+
- **Framework:** Laravel 11.x
- **Arquitetura:** API REST (apenas backend)

## Backend
- **ORM:** Eloquent
- **API:** RESTful
- **Autenticação:** Laravel Sanctum
- **Jobs/Queue:** Laravel Queue
- **Validação:** Laravel Validator
- **Logging:** Monolog
- **Caching:** Laravel Cache
- **Eventos:** Laravel Event/Listener
- **Documentação de API:** Scramble (dedoc/scramble)

## Storage & File System
- **Banco de Dados Principal:** MySQL/MariaDB
- **Conexões Múltiplas:** Suporte a múltiplas conexões de banco de dados
- **Cache e Queues:** Redis (quando configurado)

## Serviços Externos e Integrações
- **Google APIs:** Google Client Library (autenticação e serviços Google)
- **Elasticsearch:** Integração para busca e indexação
- **Hubspot:** Integração para CRM e gestão de contratos
- **Asaas:** Integração para gestão financeira e notas fiscais
- **Discord:** Integração para notificações e comandos
- **Gemini AI:** Integração para análise de documentos e IA

## Testes
- **Framework de Testes:** Pest PHP
- **Testes Unitários:** Pest
- **Testes de Integração:** Pest com Laravel Plugin
- **Testes de API:** Laravel HTTP Tests via Pest
- **Snapshots:** Spatie Pest Plugin Snapshots

## Deployment & Infraestrutura
- **Containerização:** Docker
- **Web Server:** Nginx
- **Ambiente:** PHP-FPM
- **Configuração:** Docker Compose

## Convenções de Código
- **Estilo:** PSR-12
- **Tipagem:** Strict typing (declare(strict_types=1))
- **Documentação:** PHPDoc
- **Linting:** Laravel Pint
