# Padrões de Design Responsivo

## Breakpoints do Tailwind CSS

O projeto utiliza Tailwind CSS v3.4.17 com breakpoints customizados definidos em `tailwind.config.js`:

- **sm**: `740px` (tablets pequenos)
- **md**: `768px` (tablets)
- **lg**: `1024px` (desktops pequenos)
- **lg**: `1280px` (desktops médios)
- **2xl**: `1536px` (desktops grandes)

### Uso de Breakpoints Tailwind

Os breakpoints são utilizados através de classes utilitárias do Tailwind CSS diretamente no JSX:

```tsx
<div className="grid grid-cols-1 md:grid-cols-2 gap-4">
  <div>Coluna 1</div>
  <div>Coluna 2</div>
</div>

<div className="hidden sm:block">
  Conteúdo visível apenas em telas maiores que 740px
</div>

<div className="block sm:hidden">
  Conteúdo visível apenas em mobile
</div>
```

### Padrões Comuns

- **Grids Responsivos**: `grid-cols-1 md:grid-cols-2` para layouts que mudam de 1 coluna (mobile) para 2 colunas (tablet+)
- **Espaçamento Adaptativo**: `space-y-2 md:space-y-5` para ajustar espaçamentos conforme o tamanho da tela
- **Visibilidade Condicional**: `hidden sm:flow-root` e `block sm:hidden` para mostrar/ocultar elementos conforme breakpoint
- **Text Wrapping**: `text-wrap md:text-nowrap` para controlar quebra de texto

## Media Queries em styled-components

Além do Tailwind CSS, o projeto também utiliza media queries dentro de styled-components para estilos mais complexos:

```tsx
import styled from "styled-components";

export const ExpandedContent = styled.div`
    display: flex;
    flex-direction: column;
    width: 100%;
    
    @media (max-width: 1024px) {
        margin-top: 9px;
    }
    
    @media (max-width: 768px) {
        margin-top: 8px;
    }
    
    @media (max-width: 640px) {
        margin-top: 7px;
    }
    
    @media (max-width: 480px) {
        margin-top: 6px;
    }
`;
```

### Breakpoints Customizados em styled-components

O projeto utiliza breakpoints específicos em styled-components que podem diferir dos breakpoints do Tailwind:
- `1024px` (tablets grandes)
- `850px` (tablets médios)
- `768px` (tablets pequenos)
- `640px` (mobile grande)
- `480px` (mobile médio)
- `414px` (mobile pequeno)
- `375px` (mobile muito pequeno)
- `320px` (mobile mínimo)

## Detecção de Mobile via JavaScript

Alguns componentes utilizam detecção de mobile via JavaScript para lógica condicional:

```tsx
const isMobile = window.innerWidth <= 990;

if (isMobile) {
  // Lógica específica para mobile
}
```

**Nota**: Preferir classes Tailwind CSS ou media queries em styled-components quando possível, reservando detecção JavaScript para casos específicos.

## Boas Práticas

- **Mobile-First**: Começar com layout mobile e aprimorar progressivamente para telas maiores usando breakpoints Tailwind (`sm:`, `md:`, `lg:`)
- **Consistência**: Usar os breakpoints padrão do Tailwind configurados no projeto
- **Unidades Relativas**: Preferir unidades rem/em sobre pixels fixos quando possível
- **Testar em Múltiplos Dispositivos**: Testar mudanças de UI em múltiplos tamanhos de tela
- **Design Touch-Friendly**: Garantir que alvos de toque sejam dimensionados apropriadamente (mínimo 44x44px) para usuários mobile
- **Performance em Mobile**: Otimizar imagens e assets para condições de rede mobile
- **Tipografia Legível**: Manter tamanhos de fonte legíveis em todos os breakpoints
- **Prioridade de Conteúdo**: Mostrar o conteúdo mais importante primeiro em telas menores

## Viewport Dinâmico

Para componentes que ocupam altura total da tela, o projeto utiliza `100dvh` (dynamic viewport height) para melhor compatibilidade com navegadores mobile:

```tsx
const Container = styled.div`
    height: 100dvh; /* Dynamic viewport height for mobile browsers */
`;
```
