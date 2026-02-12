# Estrutura de Páginas no 123jus-ui

## Organização

Páginas ficam em `src/pages/` por feature. Cada página é uma pasta com:

- `index.tsx` — componente da página (entry)
- `styles.ts` — styled-components da página (opcional)

```
src/pages/
  Demandas/
    index.tsx
    styles.ts
  Login/
    index.tsx
    recover.tsx
    styles.ts
  Site/
    Contact/
      index.tsx
      styles.ts
```

## Convenções

- Uma pasta por tela; rotas em `src/routes/index.tsx` importam o componente da pasta (ex.: `import Demandas from '../pages/Demandas'`).
- Estilos da página em `styles.ts` na mesma pasta; usar `createGlobalStyle` só em `src/styles/Global.ts`.
- Páginas dentro de `Layout` (rotas privadas) usam Header, Sidebar e conteúdo; rotas públicas não usam Layout.

## Uso de contexto e serviços

- Autenticação: `useAuth()` de `contexts/UserContext` — `user`, `signIn`, `signOut`, `setPageType`, `setPageName`.
- Filtros (lista de demandas): `useSidebarFiltros()` de `contexts/SidebarFiltrosContext`.
- Chamadas à API: `api` de `services/api` ou serviços de domínio (ex.: `comentarioService`).
- Feedback: `toast` (react-hot-toast); opções em `config/toastConfig`.

## Padrão de imports

Ordenar por grupo: React, hooks, componentes, ícones, services, config, styles, assets.

```typescript
import { useEffect, useState } from "react";
import { useHistory } from "react-router-dom";
import { useAuth } from "../../contexts/UserContext";
import ButtonDefault from "../../components/form/ButtonDefault";
import api from "../../services/api";
import toast from "react-hot-toast";
import { Container } from "./styles";
```

## Rotas e páginas

- Rotas públicas: sem `Layout` (Home, Login, Cadastrar, Site/*).
- Rotas privadas: dentro de `<Layout>` em `routes/index.tsx`; usar `PrivateRoutes` com `isPrivate`.
- Páginas que definem título: usar hook `useDocumentTitle` (ex.: Login).

- Nova página: criar pasta em `src/pages/[NomePagina]/` com `index.tsx` (e `styles.ts` se precisar), registrar rota em `src/routes/index.tsx`.
