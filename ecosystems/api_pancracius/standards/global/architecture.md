# Arquitetura do Fênix

## Visão Geral

O Fênix é um sistema jurídico Laravel focado em gestão de processos, documentos, assinaturas, financeiro e integrações. A arquitetura é orientada a API e segue o padrão MVC do Laravel.

## Características Principais

- **Sistema Jurídico**: Plataforma completa para gestão de escritórios de advocacia
- **Apenas Backend**: Não possui frontend, apenas APIs para serem consumidas pelo projeto principal
- **Laravel**: Utiliza o framework Laravel completo, incluindo seus padrões e melhores práticas
- **API REST**: Todas as funcionalidades são expostas via API REST

## Arquitetura MVC do Laravel

O projeto segue a arquitetura MVC do Laravel, com algumas personalizações para o contexto de sistema jurídico:

### Controladores (Controllers)
- Localizados em `app/Http/Controllers`
- Cada controlador é responsável por um recurso específico (AuthController, AssinaturaController, ProcessoController, etc.)
- Seguem o padrão REST para endpoints API
- Utilizam Services para lógica de negócio

### Modelos (Models)
- Localizados em `app/Models`
- Organizados por domínio (Processos, Assinaturas, Documentos, Financeiro, etc.)
- Representam as entidades de domínio (Processo, Assinatura, Documento, Contrato, etc.)
- Utilizam Eloquent ORM para interação com o banco de dados
- BaseModel como classe base com configurações comuns

### Repositórios (Repositories)
- Localizados em `app/Repositories`
- Implementam o padrão Repository para abstrair a camada de persistência
- Permitem substituir implementações sem afetar os controladores

### Serviços (Services)
- Localizados em `app/Services`
- Organizados por domínio (Auth, Assinaturas, Processos, Financeiro, etc.)
- Contêm a lógica de negócio principal
- São utilizados pelos controladores para processar as solicitações
- Classes de ação específicas para operações complexas (Cria, Atualiza, Delete, etc.)

### DTOs (Data Transfer Objects)
- Localizados em `app/DTO`
- Objetos de transferência de dados para comunicação entre camadas
- Garantem tipagem forte e estrutura consistente

### Traits
- Localizados em `app/Models/Traits`
- Organizados por contexto (Common, User, Escritorio)
- Reutilização de funcionalidades comuns entre modelos

## Estrutura de Diretórios

### app/
Core do aplicativo Laravel
- `Http/Controllers/` - Controladores API
  - `AuthController.php` - Autenticação e autorização
  - `Assinaturas/` - Controladores de assinaturas e pacotes
  - `Processos/` - Controladores de processos jurídicos
  - `Documentos/` - Controladores de documentos
  - `Financeiro/` - Controladores financeiros
  - `ModelosDeDocumento/` - Controladores de modelos
- `Models/` - Modelos Eloquent organizados por domínio
  - `Processos/` - Processo, AreaDeProcesso, TipoDeAcaoDeProcesso
  - `Assinaturas/` - Assinatura, Pacote, ConsumoDePacote, Produto
  - `Documentos/` - Documento
  - `Financeiro/` - Contrato, Receita, Fatura, NotaFiscal
  - `Usuarios/` - User
  - `Escritorio.php` - Escritório
  - `BaseModel.php` - Classe base para modelos
  - `Traits/` - Traits compartilhadas
- `Services/` - Serviços de negócio organizados por domínio
  - `Auth/` - Serviços de autenticação
  - `Assinaturas/` - Serviços de assinaturas
  - `Processos/` - Serviços de processos
  - `Financeiro/` - Serviços financeiros
  - `Integracoes/` - Serviços de integrações externas
- `Repositories/` - Camada de acesso a dados
- `Jobs/` - Jobs para processamento em background
- `DTO/` - Data Transfer Objects
- `Enums/` - Enumeradores
- `Exceptions/` - Exceções customizadas

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

### Gestão de Processos Jurídicos
- Cadastro e gestão de processos
- Áreas de processo
- Tipos de ação
- Correção monetária e índices

### Gestão de Assinaturas
- Assinaturas de produtos
- Pacotes de petições
- Consumo de pacotes
- Aceite de termos de produtos

### Gestão de Documentos
- Modelos de documentos
- Tipos de modelos
- Insert prévio de documentos
- Preview de modelos

### Gestão Financeira
- Contratos
- Receitas e faturas
- Notas fiscais (integração Asaas)
- Sincronização com Hubspot

### Integrações
- Google (autenticação e serviços)
- Hubspot (CRM e contratos)
- Asaas (financeiro e notas fiscais)
- Discord (notificações e comandos)
- Elasticsearch (busca e indexação)
- Gemini AI (análise de documentos)

### Agendas e Workflows
- Agendas de processos
- Workflows com deslocamento de datas
- Feriados e configurações

### Segurança e Auditoria
- Autenticação via tokens (Sanctum)
- Login via Google
- Sistema de permissões
- Logs de operações
