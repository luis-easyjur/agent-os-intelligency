# Arquitetura do 123jus-ui

## Visão Geral

O 123jus-ui é uma aplicação React/TypeScript que consome a API do 123jus-api para fornecer interface de usuário para gestão de propostas, orientações, pendencias, demandas e profissionais jurídicos. A arquitetura segue padrões modernos de React com componentes funcionais, hooks e Context API.

## Características Principais

- **Sistema Jurídico Frontend**: Interface de usuário para gestão de propostas, demandas e profissionais jurídicos
- **React 17 com TypeScript**: Utiliza React 17.0.2 com TypeScript para type safety
- **Single Page Application (SPA)**: Aplicação de página única com roteamento client-side
- **Component-Based Architecture**: Arquitetura baseada em componentes reutilizáveis
- **Context API**: Gerenciamento de estado global usando Context API

## Arquitetura de Componentes React

O projeto segue uma arquitetura baseada em componentes React funcionais, com separação clara de responsabilidades:

### Páginas (Pages)
- Localizadas em `src/pages/`
- Cada página representa uma rota da aplicação
- Exemplos: `Dashboard`, `Demandas`, `Proposals`, `Professionals`, `Operators`, `Login`, etc.
- Organizadas por funcionalidade/domínio

### Componentes (Components)
- Localizados em `src/components/`
- Componentes reutilizáveis organizados por funcionalidade
- Exemplos: `Layout`, `Header`, `Sidebar`, `CardDemanda`, `Pendencias`, `NovaDemanda`, etc.
- Componentes de formulário em `components/form/`
- Componentes de UI em `components/Ui/`

### Contextos (Contexts)
- Localizados em `src/contexts/`
- `UserContext.tsx` - Gerenciamento de autenticação e usuário
- `SidebarFiltrosContext.tsx` - Estado da sidebar de filtros
- Utilizam Context API do React para estado global

### Hooks Customizados
- Localizados em `src/hooks/`
- `useAuth` - Hook para autenticação (exportado do UserContext)
- `useCepLookup` - Hook para busca de CEP via ViaCEP
- `useEdicaoDemanda` - Hook para edição de demandas
- `useDocumentTitle` - Hook para título do documento
- `useMetaDescription` - Hook para meta description

### Serviços (Services)
- Localizados em `src/services/`
- `api.ts` - Configuração do Axios e cliente HTTP
- `comentarioService.ts` - Serviço para comentários
- `toast.ts` - Configuração de toasts/notificações

### Utilitários (Utils)
- Localizados em `src/utils/` e `src/Utils/`
- `validations.ts` - Funções de validação
- `masks.ts` - Máscaras de input (CEP, processo, dinheiro, etc.)
- `dateValidations.ts` - Validações de datas
- `errorSanitizer.ts` - Sanitização de erros
- `partesConverter.ts` - Conversão de partes de processos
- `servicoMapper.ts` - Mapeamento de serviços
- `statusColors.ts` - Cores de status
- `statusMessages.ts` - Mensagens de status
- `timezoneUtils.ts` - Utilitários de timezone

### Rotas (Routes)
- Localizadas em `src/routes/`
- `index.tsx` - Definição de todas as rotas da aplicação
- `PrivateRoutes.tsx` - Componente para rotas protegidas com autenticação

### Estilos (Styles)
- Localizados em `src/styles/`
- `Global.ts` - Estilos globais usando styled-components
- `Themes/light.ts` - Tema claro com cores do sistema
- Suporte a múltiplos temas (comum, admin, operador)

### Tipos (Types)
- Localizados em `src/types/`
- Interfaces TypeScript para entidades
- `edicaoDemanda.ts` - Tipos para edição de demandas
- `index.ts` - Exportações de tipos

## Estrutura de Diretórios

### src/
Core do aplicativo React/TypeScript
- `pages/` - Páginas da aplicação (rotas)
  - `Dashboard/` - Dashboard principal
  - `Demandas/` - Listagem de demandas
  - `Demanda/` - Detalhes de uma demanda
  - `Proposals/` - Listagem de propostas
  - `Professionals/` - Listagem de profissionais
  - `Operators/` - Listagem de operadores
  - `Login/` - Página de login
  - `Register/` - Página de registro
  - `Site/` - Páginas públicas do site
- `components/` - Componentes reutilizáveis
  - `Layout/` - Layout principal da aplicação
  - `Header/` - Cabeçalho
  - `Sidebar/` - Barra lateral de navegação
  - `Demanda/` - Componentes relacionados a demandas
  - `Pendencias/` - Componentes de pendencias (incluindo Kanban)
  - `NovaDemanda/` - Componentes para criação de demandas
  - `form/` - Componentes de formulário
  - `Ui/` - Componentes de UI reutilizáveis
- `contexts/` - Contextos React
  - `UserContext.tsx` - Contexto de autenticação e usuário
  - `SidebarFiltrosContext.tsx` - Contexto de filtros
- `hooks/` - Hooks customizados
  - `useAuth.ts` - Hook de autenticação
  - `useCepLookup.ts` - Hook para busca de CEP
  - `useEdicaoDemanda.ts` - Hook para edição de demandas
- `services/` - Serviços de API
  - `api.ts` - Cliente Axios configurado
  - `comentarioService.ts` - Serviço de comentários
- `routes/` - Configuração de rotas
  - `index.tsx` - Rotas principais
  - `PrivateRoutes.tsx` - Componente de rotas protegidas
- `styles/` - Estilos globais e temas
  - `Global.ts` - Estilos globais
  - `Themes/light.ts` - Tema claro
- `utils/` e `Utils/` - Funções utilitárias
- `types/` - Interfaces e tipos TypeScript
- `helpers/` - Funções auxiliares
  - `permissoesUsuario.ts` - Helpers de permissões
- `config/` - Configurações
  - `toastConfig.ts` - Configuração de toasts
- `App.tsx` - Componente raiz da aplicação
- `index.tsx` - Ponto de entrada da aplicação

## Padrões de Código

- **TypeScript Strict Mode**: Usar modo strict do TypeScript
- **Componentes Funcionais**: Preferir componentes funcionais com hooks
- **Hooks Customizados**: Criar hooks reutilizáveis quando apropriado
- **Type Safety**: Usar tipagem forte do TypeScript em todos os componentes
- **Separação de Responsabilidades**: Separar lógica de negócio, apresentação e estado
- **Testes**: Cobrir funcionalidades principais com testes (React Testing Library)

## Funcionalidades Principais

### Gestão de Demandas
- Listagem de demandas com filtros
- Visualização detalhada de demandas
- Criação de novas demandas
- Edição de demandas
- Histórico de alterações
- Sistema de abas (Principal, Anexos, Comentários, Histórico, Orientações)

### Gestão de Pendencias
- Sistema Kanban para pendencias
- Criação e resolução de pendencias
- Filtros e busca de pendencias
- Contador de pendencias

### Gestão de Profissionais e Operadores
- Listagem de profissionais
- Perfil de profissional
- Listagem de operadores
- Perfil de operador
- Gestão de permissões

### Autenticação e Autorização
- Login com JWT
- Rotas protegidas
- Diferentes perfis de usuário (Admin, Operador, Profissional, Solicitante)
- Context API para gerenciamento de autenticação

### Formulários
- Formulários com react-hook-form
- Validação com yup
- Máscaras de input (CEP, processo, dinheiro, telefone)
- Componentes de formulário reutilizáveis

### Notificações
- Sistema de notificações
- Toasts com react-hot-toast
- Contadores de notificações

### Integrações
- Integração com API do backend (123jus-api)
- ViaCEP para busca de endereços
- Microsoft Clarity para analytics
