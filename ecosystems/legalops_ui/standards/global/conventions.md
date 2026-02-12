# Convenções Gerais de Desenvolvimento no 123jus-ui

## Boas Práticas

- **Fonte única e reutilização**: Manter uma única fonte de verdade por conceito (constantes, formatadores, mapeamentos). Antes de escrever código novo, consultar a base para reutilizar o que já existir; ao identificar lógica repetida, centralizar em módulo compartilhado. Ver **single-source-reuse.md**.
- **Estrutura de Projeto React/TypeScript**: Seguir a estrutura padrão do projeto, mantendo arquivos nos diretórios apropriados
- **Documentação Clara**: Manter arquivos README atualizados com instruções de setup, visão geral da arquitetura e diretrizes de contribuição
- **Boas Práticas de Controle de Versão**: Usar mensagens de commit claras e descritivas
- **Configuração de Ambiente**: Usar arquivo `.env` para configurações de ambiente (ex: `REACT_APP_API_URL`); nunca commitar secrets ou API keys no controle de versão
- **Respeitar Arquitetura de Componentes**: Seguir a arquitetura baseada em componentes React, mantendo a separação de responsabilidades
- **Padrões de Nomenclatura**:
  - Componentes: PascalCase (ex: `ButtonDefault.tsx`, `InputDefault.tsx`)
  - Hooks: camelCase com prefixo `use` (ex: `useAuth.ts`, `useCepLookup.ts`)
  - Services: camelCase (ex: `api.ts`, `comentarioService.ts`)
  - Utilitários: camelCase (ex: `validations.ts`, `masks.ts`)
  - Interfaces: Prefixo `I` + PascalCase (ex: `IProposta.ts`)
  - Páginas: PascalCase (ex: `Dashboard/`, `Demandas/`)
  - Rotas: Substantivos no singular (ex: `/demanda`, `/proposta`)

## Fluxo de Trabalho

- **Branches**: Criar branches específicos para features/bugs (ex: `feature/new-component`, `fix/button-styling`)
- **Pull Requests**: Criar Pull Requests com descrição clara das mudanças
- **Code Reviews**: Realizar code reviews antes de mesclar código
- **Testes**: Adicionar testes para novas funcionalidades e garantir que todos os testes passem

## Estrutura de Diretórios

- `src/pages/` - Páginas da aplicação (rotas)
- `src/components/` - Componentes reutilizáveis
- `src/hooks/` - Hooks customizados
- `src/services/` - Serviços de API
- `src/utils/` - Funções utilitárias genéricas (validação, máscaras, datas, sanitização de erros, etc.)
- `src/utility/` - Tipos e helpers de domínio (interfaces de proposta/anexo, status, timezone, mapeadores, etc.)
- `src/contexts/` - Contextos React para estado global
- `src/types/` - Interfaces TypeScript compartilhadas pela aplicação (ex.: filtros, tipos de listagem); interfaces de domínio/API podem ficar em `utility/` ou no próprio service
- `src/routes/` - Configuração de rotas
- `src/styles/` - Estilos globais e temas
- `src/config/` - Configurações (ex.: toastConfig)
- `src/helpers/` - Funções auxiliares

## Padrões de Resposta da API

A aplicação consome a API do 123jus-api que retorna formato padronizado:

### Sucesso
```json
{
  "code": 200,
  "status": "success",
  "message": "Ação realizada com sucesso",
  "result": { ... }
}
```

### Erro
```json
{
  "code": 200,
  "status": "error",
  "message": "Ocorreu um erro",
  "result": [
    {
      "field": "campo",
      "message": "Mensagem de erro específica"
    }
  ]
}
```

## Autenticação

- Token JWT armazenado em `localStorage` com chave `@Jj:token`
- Dados do usuário armazenados em `localStorage` com chave `@Jj:user`
- Token adicionado automaticamente no header `Authorization: Bearer <token>` via Axios
- Context API (`UserContext`) gerencia estado de autenticação
- Rotas protegidas usando componente `PrivateRoutes`

## Validação

- Validação de formulários com `react-hook-form` e `yup`
- Validações utilitárias em `src/utils/validations.ts`
- Validação ocorre no cliente para UX, mas servidor também valida
- Erros de validação exibidos em formato padronizado
