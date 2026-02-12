# Padrões de Estilização no 123jus-ui

## Visão Geral

O 123jus-ui utiliza duas abordagens principais para estilização:
1. **styled-components** - CSS-in-JS com suporte a temas
2. **Tailwind CSS** - Framework CSS utilitário

## styled-components

### Configuração

O projeto utiliza styled-components v5.3.5 com suporte a temas:

```typescript
import styled from 'styled-components';
import { ThemeProvider } from 'styled-components';

// Componente estilizado
const Container = styled.div`
    display: flex;
    padding: 16px;
    background: ${props => props.theme.colors.background};
    color: ${props => props.theme.colors.interfaceText400};
`;
```

### Temas

Temas estão definidos em `src/styles/Themes/light.ts`:

- **comum** - Tema padrão (cinza)
- **admin** - Tema para administradores (vermelho)
- **operador** - Tema para operadores

**Estrutura de cores:**
- `primaryBlue*` - Tons de azul primário
- `primaryOrange*` - Tons de laranja
- `primaryRed*` - Tons de vermelho (tema admin)
- `secondaryGreen*` - Tons de verde secundário
- `secondaryPool*` - Tons de azul claro
- `interfaceDanger*` - Cores de erro/perigo
- `interfaceSuccess*` - Cores de sucesso
- `interfaceText*` - Cores de texto
- `interfaceGray*` - Tons de cinza
- `interfaceBorder*` - Cores de borda

### Uso de Temas

```typescript
import { ThemeProvider } from 'styled-components';
import { comum, admin, operador } from './styles/Themes/light';

<ThemeProvider theme={user.profile === 'admin' ? admin : comum}>
    {/* Componentes */}
</ThemeProvider>
```

### Estilos Globais

Estilos globais em `src/styles/Global.ts` usando `createGlobalStyle`:

```typescript
import { createGlobalStyle } from "styled-components";

export const GlobalStyles = createGlobalStyle`
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }
    
    body {
        font-family: 'Montserrat', sans-serif;
        -webkit-font-smoothing: antialiased;
    }
`;
```

## Tailwind CSS

### Configuração

Tailwind CSS v3.4.17 está configurado em `tailwind.config.js`:

```javascript
module.exports = {
  content: ["./src/**/*.{js,jsx,ts,tsx}"],
  theme: {
    screens: {
      'sm': '740px',
      'md': '768px',
      'lg': '1024px',
      'xl': '1280px',
      '2xl': '1536px',
    },
  },
}
```

### Uso

Tailwind é usado em conjunto com styled-components:

```typescript
<div className="hidden sm:flow-root">
    {/* Conteúdo */}
</div>

<div className="block sm:hidden">
    {/* Conteúdo mobile */}
</div>
```

## Padrões de Estilização

### Arquivos de Estilo

Componentes podem ter arquivos `styles.ts` separados:

```typescript
// components/Component/styles.ts
import styled from 'styled-components';

export const Container = styled.div`
    display: flex;
    flex-direction: column;
`;

export const Title = styled.h1`
    font-size: 1.5rem;
    color: ${props => props.theme.colors.interfaceText400};
`;
```

### Classes CSS Globais

Algumas classes CSS globais estão definidas em `Global.ts`:

- `.Title` - Título padrão
- `.SubTitle` - Subtítulo
- `.Paragraph` - Parágrafo
- `.w-100` - Largura 100%

### Responsividade

Uso de breakpoints do Tailwind:
- `sm:` - 740px+
- `md:` - 768px+
- `lg:` - 1024px+
- `xl:` - 1280px+
- `2xl:` - 1536px+

Ou media queries em styled-components:

```typescript
const Container = styled.div`
    padding: 16px;
    
    @media (max-width: 720px) {
        padding: 8px;
    }
`;
```

## Boas Práticas

- **styled-components para Componentes**: Usar styled-components para estilização de componentes
- **Tailwind para Utilitários**: Usar Tailwind para classes utilitárias rápidas
- **Temas**: Sempre usar cores do tema ao invés de cores hardcoded
- **Consistência**: Manter consistência visual usando o sistema de design
- **Responsividade**: Sempre considerar responsividade mobile-first
- **Performance**: Evitar criar styled-components dentro de render
- **Organização**: Manter estilos organizados em arquivos `styles.ts` quando apropriado

## Exemplo Completo

```typescript
import styled from 'styled-components';

const Container = styled.div`
    display: flex;
    flex-direction: column;
    padding: 16px;
    background: ${props => props.theme.colors.background};
    border: 1px solid ${props => props.theme.colors.interfaceBorder100};
    
    @media (max-width: 720px) {
        padding: 8px;
    }
`;

const Title = styled.h1`
    font-size: 1.5rem;
    font-weight: 600;
    color: ${props => props.theme.colors.interfaceText400};
    margin-bottom: 16px;
`;

const Component: React.FC = () => {
    return (
        <Container className="sm:flex-row">
            <Title>Título</Title>
            {/* Conteúdo */}
        </Container>
    );
};
```
