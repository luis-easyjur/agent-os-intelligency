# Tech Stack do Fênix

## Framework & Runtime
- **Linguagem:** PHP 8.3+
- **Framework:** Laravel 11.x
- **Arquitetura:** API REST (apenas backend, microserviço)
- **Padrão:** Microserviços complementando sistema principal (APP)

## Backend
- **ORM:** Eloquent ORM
- **API:** RESTful API
- **Autenticação:** Laravel Sanctum (tokens)
- **OAuth:** Google OAuth 2.0
- **Jobs/Queue:** Laravel Queue (processamento assíncrono)
- **Validação:** Laravel Validator
- **Logging:** Monolog
- **Caching:** Laravel Cache
- **Eventos:** Laravel Event/Listener
- **Documentação de API:** Scramble (dedoc/scramble)

## Storage & File System
- **Banco de Dados Principal:** MySQL/MariaDB
- **Multi-tenancy:** Suporte a múltiplas conexões de banco de dados (iturn, oabes, oabmg)
- **Cache e Queues:** Redis (quando configurado)
- **Convenções de Tabelas:** Prefixo `tb_` para todas as tabelas (ex: `tb_processos`, `tb_assinaturas_produtos_ia`)

## Serviços Externos e Integrações
- **Google APIs:** Google Client Library (autenticação OAuth e serviços Google)
- **Elasticsearch:** Integração para busca avançada e indexação de documentos
- **Hubspot:** Integração para CRM, gestão de contratos e Health Score
- **Asaas:** Integração para gestão financeira e notas fiscais
- **Discord:** Integração para notificações e comandos via Discord
- **Gemini AI:** Integração para análise de documentos e processamento com IA
- **EasyJur (APP):** Integração com sistema principal via APIs
- **Solucionare:** Integração para captura de publicações processuais
- **IBGE:** Integração para dados de cidades e localidades

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
- **Porta:** 9000 (desenvolvimento local)

## Convenções de Código
- **Estilo:** PSR-12
- **Tipagem:** Strict typing (declare(strict_types=1))
- **Documentação:** PHPDoc
- **Linting:** Laravel Pint
- **Idioma do Código:** Inglês (classes, métodos, variáveis)
- **Idioma de Mensagens:** Português (mensagens de erro e conteúdo visível ao usuário)

## Arquitetura de Código
- **Padrão:** MVC (Model-View-Controller) do Laravel
- **Controllers:** Localizados em `app/Http/Controllers`, organizados por domínio
- **Models:** Localizados em `app/Models`, organizados por domínio, estendem `BaseModel`
- **Services:** Localizados em `app/Services`, contêm lógica de negócio
- **Repositories:** Localizados em `app/Repositories`, abstraem acesso a dados
- **DTOs:** Localizados em `app/DTO`, objetos de transferência de dados
- **Jobs:** Localizados em `app/Jobs`, processamento assíncrono
- **Middleware:** Custom middleware para multi-tenancy e autenticação

## Padrões de Desenvolvimento
- **Injeção de Dependência:** Sempre usar injeção de dependência ao invés de instanciar classes diretamente
- **Clean Code:** Funções pequenas, nomes descritivos, responsabilidade única
- **SOLID:** Aplicar princípios SOLID
- **Type Hinting:** Usar tipagem forte (PHP 8+)
- **Testes:** Cobrir funcionalidades principais com testes automatizados

## Segurança
- **Autenticação:** Laravel Sanctum (tokens)
- **Autorização:** Middleware de autenticação em todas as rotas protegidas
- **Validação:** Validação de entrada em todos os endpoints
- **Logs:** Sistema de logs para auditoria e rastreamento

## Performance
- **Cache:** Laravel Cache para otimização de queries frequentes
- **Queue:** Processamento assíncrono para operações pesadas
- **Otimização:** Eloquent queries otimizadas, eager loading quando apropriado

## Monitoramento e Observabilidade
- **Logs:** Monolog para logging estruturado
- **Notificações:** Discord para notificações de rotinas e erros
- **Rastreamento:** Logs detalhados de todas as operações e integrações
