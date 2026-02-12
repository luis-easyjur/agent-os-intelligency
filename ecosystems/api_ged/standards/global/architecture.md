# Arquitetura do APP-GED

## Visão Geral

O APP-GED é um microsserviço Laravel focado no gerenciamento de arquivos e filas de processamento para o projeto principal (APP). A arquitetura é orientada a API e segue o padrão MVC do Laravel.

## Características Principais

- **Microsserviço**: Funciona como um serviço independente que se integra ao sistema principal
- **Apenas Backend**: Não possui frontend, apenas APIs para serem consumidas pelo projeto principal
- **Laravel**: Utiliza o framework Laravel completo, incluindo seus padrões e melhores práticas
- **API REST**: Todas as funcionalidades são expostas via API REST

## Arquitetura MVC do Laravel

O projeto segue a arquitetura MVC do Laravel, com algumas personalizações para o contexto de microsserviço:

### Controladores (Controllers)
- Localizados em `app/Http/Controllers`
- Cada controlador é responsável por um recurso específico (FileController, QueueController, etc.)
- Seguem o padrão REST para endpoints API

### Modelos (Models)
- Localizados em `app/Models`
- Representam as entidades de domínio (File, Queue, QueueItem, etc.)
- Utilizam Eloquent ORM para interação com o banco de dados

### Repositórios (Repositories)
- Localizados em `app/Repositories`
- Implementam o padrão Repository para abstrair a camada de persistência
- Permitem substituir implementações sem afetar os controladores

### Serviços (Services)
- Localizados em `app/Services`
- Contêm a lógica de negócio principal
- São utilizados pelos controladores para processar as solicitações

## Estrutura de Diretórios

### app/
Core do aplicativo Laravel
- `Http/Controllers/` - Controladores API
  - `FileController.php` - Gerencia operações de arquivos
  - `QueueController.php` - Gerencia filas de processamento
  - `AuthController.php` - Autenticação e autorização
- `Models/` - Modelos Eloquent
  - `File.php` - Modelo de arquivos
  - `Queue.php` - Modelo de filas
  - `QueueItem.php` - Itens em filas
- `Services/` - Serviços de negócio
  - `FileService.php` - Lógica de manipulação de arquivos
  - `QueueService.php` - Lógica de gerenciamento de filas
- `Repositories/` - Camada de acesso a dados
- `Jobs/` - Jobs para processamento em background

### routes/
Definições de rotas
- `api.php` - Rotas da API REST
- `channels.php` - Canais de broadcast
- `console.php` - Comandos personalizados

### database/
Migrações e seeders
- `migrations/` - Migrações de banco de dados
- `seeders/` - Seeders para dados iniciais
- `factories/` - Factories para testes

### config/
Arquivos de configuração
- `filesystems.php` - Configurações de armazenamento
- `queue.php` - Configurações de filas

## Padrões de Código

- **PSR-12**: Seguir padrão de codificação PSR-12
- **SOLID**: Aplicar princípios SOLID
- **Clean Code**: Nomes descritivos, funções pequenas, responsabilidade única
- **Type Hinting**: Usar tipagem forte (PHP 8+)
- **Dependency Injection**: Injetar dependências ao invés de instanciar diretamente
- **Testes**: Cobrir funcionalidades principais com testes automatizados

## Funcionalidades Principais

### Gerenciamento de Arquivos
- Upload de arquivos
- Download de arquivos
- Armazenamento em diferentes drivers (local, S3, Azure)
- Controle de versões
- Compartilhamento

### Gerenciamento de Filas
- Criação de filas de processamento
- Monitoramento de status
- Processamento assíncrono
- Priorização de itens

### Segurança e Auditoria
- Autenticação via tokens (Sanctum)
- Auditoria de todas as operações
- Controle de acesso baseado em usuário
