# Arquitetura do Sistema

## Visão Geral

Este é um sistema PHP legado, organizado como um monólito. Frontend e backend estão misturados no mesmo código-base.

A arquitetura atual NÃO segue um padrão formal como DDD ou Clean Architecture, e isso deve ser respeitado.

## Arquitetura Legada: Respeito e Limites

**Este projeto utiliza uma arquitetura legada e não está em processo de migração tecnológica.**

### O que isso significa:

- **A estrutura legada deve ser respeitada**: Não reestruturar organização de diretórios, separação de responsabilidades ou padrões arquiteturais existentes
- **Não introduzir novas camadas**: Proibido criar camadas de serviço, repositórios abstratos, DTOs ou qualquer padrão arquitetural moderno sem solicitação explícita
- **Não mudar stack**: Proibido sugerir ou implementar mudanças de stack tecnológica (ex: migrar para framework moderno, introduzir ORM, etc)
- **Não aplicar padrões formais**: Não aplicar DDD, Clean Architecture, CQRS ou qualquer padrão arquitetural formal

### O que isso NÃO significa:

- **Qualidade de código é obrigatória**: Todo código novo ou alterado DEVE seguir boas práticas de clean code (funções pequenas, nomes claros, responsabilidade única, etc)
- **Melhorias locais são permitidas**: Melhorar qualidade de código dentro do contexto existente é sempre bem-vindo
- **Refatorações incrementais são permitidas**: Melhorar código existente de forma local e incremental é permitido e encorajado

## Estrutura de Diretórios

### sgr/
Sistema de gestão principal
- `advogados/` - Módulo principal de advogados
  - `scripts/` - Scripts PHP principais do módulo
    - `proposta/` - Funcionalidades de propostas
    - `processos/` - Gestão de processos
    - `agenda/` - Sistema de agenda
    - `faturamento/` - Módulo de faturamento
    - `jurisai/` - Integração com JurisAI
  - `ajax_*.php` - Scripts AJAX específicos do módulo
- `ajax_*.php` - Scripts AJAX gerais
- `index.php` - Página principal do sistema

### database/
Classes e funções de acesso ao banco
- `QueryBuilder.php` - Classe para consultas complexas e estruturadas
- `functions.php` - Funções utilitárias de banco (db_get_by_id, db_create, db_update, etc)
- `logging.php` - Sistema de logs
- `BaseRepository.php` - Classe base para repositórios

### api/
Endpoints da API REST
- `user/` - Endpoints de usuários
- `processo/` - Endpoints de processos
- `agenda/` - Endpoints de agenda
- `andamento/` - Endpoints de andamentos

### ajax/
Scripts AJAX para requisições assíncronas
- Scripts que processam requisições AJAX do frontend

### includes/
Arquivos de inclusão e utilitários
- Funções auxiliares e helpers

### config/
Arquivos de configuração
- `globals_setup/` - Configurações globais
- `definitions.php` - Definições e constantes

### acesso/
Sistema de autenticação e acesso
- `utils/` - Utilitários de acesso
- `api/` - APIs de cadastro e autenticação

## Organização do Código

- O código é organizado principalmente por funcionalidade
- Existem responsabilidades misturadas em alguns pontos
- Refatorações estruturais só devem ser sugeridas quando solicitadas

## Padrões de Arquivos

- Arquivos PHP seguem padrão `ajax_*.php` para scripts AJAX
- Arquivos PHP seguem padrão `JANELA_*.php` para modais
- Estrutura monolítica (frontend e backend juntos)

## Regras Importantes

A IA deve:
- Respeitar a estrutura existente
- Seguir o padrão já utilizado no diretório onde está atuando
- Propor melhorias incrementais e seguras
- **Sempre aplicar clean code** em código novo ou alterado, mesmo dentro da arquitetura legada

A IA NÃO deve:
- Sugerir reescritas completas
- Introduzir camadas novas sem solicitação explícita
- Impor padrões arquiteturais modernos sem contexto
- **Usar arquitetura legada como desculpa para código de baixa qualidade**

## Evolução

Melhorias arquiteturais devem:
- Ser pequenas
- Ter baixo risco
- Ser justificadas com impacto real

**Importante**: Melhorias de qualidade de código (clean code) são sempre permitidas e encorajadas, desde que sejam locais e incrementais, sem alterar a estrutura arquitetural existente.
