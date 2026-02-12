# Tech Stack

## Framework & Runtime
- **Linguagem:** PHP 7.4+
- **Arquitetura:** Monolítica (frontend e backend no mesmo código-base)
- **Servidor Web:** Apache/Nginx
- **Runtime:** PHP-FPM ou mod_php

## Frontend
- **Framework CSS:** Bootstrap 5.3.7 (com patches customizados)
- **Biblioteca JavaScript:** jQuery 3.x
- **Bibliotecas Auxiliares:**
  - jQuery UI (componentes interativos)
  - CKEditor (editor de texto rico)
  - Quill.js (editor de texto alternativo)
  - Flatpickr (seleção de datas)
  - Tom Select (selects avançados)
  - Toastr (notificações)
  - Gridstack (layouts arrastáveis)
  - FontAwesome (ícones)

## Database & Storage
- **Banco de Dados:** MySQL/MariaDB
- **Driver:** mysqli (extensão nativa do PHP)
- **Ferramentas de Acesso:**
  - QueryBuilder (`database/QueryBuilder.php`)
  - Functions utilitárias (`database/functions.php`)
- **Padrões de Banco:**
  - Uso de prepared statements para segurança
  - Pool de conexões para performance
  - Backup automático e estratégias de recuperação

## Estrutura de Projeto
- **Organização:** Por funcionalidade (monolítica)
- **Padrões de Arquivos:**
  - `ajax_*.php` para scripts AJAX
  - `JANELA_*.php` para modais
  - `sgr/` para sistema de gestão principal
  - `sgr/advogados/` para módulo principal de advogados

## Integrações e APIs
- **Inteligência Artificial:**
  - Integração com modelos de linguagem (LLM) para JurisAI
  - APIs de processamento de linguagem natural
  - Sistemas de análise preditiva
- **Serviços Financeiros:**
  - Integração com APIs de bancos brasileiros para Internet Banking
  - APIs de pagamento (Pix, Boleto, Cartão)
  - Processamento de transações financeiras
- **Serviços Externos:**
  - APIs de jurisprudência (STF, STJ, TST)
  - Serviços de assinatura digital
  - Integrações com sistemas de correspondentes

## Segurança e Compliance
- **Autenticação:** Sistema de autenticação customizado com sessões seguras
- **Autorização:** Controle de acesso baseado em perfis e permissões
- **Criptografia:**
  - Dados em trânsito (HTTPS/TLS)
  - Dados em repouso (encriptação de banco de dados)
- **LGPD Compliance:**
  - Política de zero retenção de dados para IA
  - Controles de privacidade e consentimento
  - Auditoria e logs de acesso

## Testing
- **Testes E2E:** Cypress (testes end-to-end automatizados)
- **Testes Unitários:** Não há framework de testes unitários PHP ativo no projeto principal
- **Testes de Segurança:** Testes periódicos de segurança e adequação LGPD

## DevOps e Infraestrutura
- **Containerização:** Docker (docker-compose para ambiente local)
- **Versionamento:** Git (Bitbucket)
- **Deploy:** Processo de deploy customizado
- **Monitoramento:** Logs e monitoramento de aplicação

## Ferramentas de Desenvolvimento
- **Agente-OS:** Sistema de desenvolvimento agêntico orientado por especificações
- **MCPs (Model Context Protocols):**
  - MySQL MCP para consultas ao banco de dados
  - Jira MCP para gestão de tarefas
  - Bitbucket MCP para gestão de repositórios
  - Confluence MCP para documentação

## Padrões e Convenções
- **Arquitetura Legada:** Respeitar estrutura legada existente, sem introduzir padrões formais (DDD, Clean Architecture) sem solicitação explícita
- **Qualidade de Código:** Seguir boas práticas de clean code (funções pequenas, nomes claros, responsabilidade única)
- **Melhorias Incrementais:** Permitir melhorias locais e refatorações incrementais dentro do contexto existente

## Observações Importantes
- Este projeto utiliza uma arquitetura legada e não está em processo de migração tecnológica
- A estrutura legada deve ser respeitada: não reestruturar organização de diretórios sem solicitação explícita
- Não introduzir novas camadas arquiteturais (serviços, repositórios abstratos, DTOs) sem solicitação explícita
- Não sugerir ou implementar mudanças de stack tecnológica (ex: migrar para framework moderno, introduzir ORM) sem solicitação explícita
