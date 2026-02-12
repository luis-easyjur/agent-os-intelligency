# Padrões de Acessibilidade de UI

## Atributos ARIA em React

O projeto utiliza atributos ARIA para melhorar a acessibilidade de componentes React, especialmente em elementos interativos e formulários.

### aria-label

Usado para fornecer labels descritivos para elementos interativos que não possuem texto visível:

```tsx
<button aria-label="Menu de ações">
  <IconMenu />
</button>

<button aria-label="Selecionar destinatário">
  <Select />
</button>

<button aria-label={isTextoExpandido ? "Recolher texto" : "Expandir texto"}>
  <IconExpand />
</button>
```

### aria-expanded

Indica o estado expandido/colapsado de elementos interativos:

```tsx
<button 
  aria-label="Selecionar destinatário"
  aria-expanded={selectOpen}
>
  <Select />
</button>
```

### aria-invalid e aria-describedby

Usados em campos de formulário para indicar erros de validação:

```tsx
<textarea
  aria-label="Campo de edição do comentário"
  aria-invalid={!!erroTexto}
  aria-describedby={erroTexto ? "erro-edicao" : undefined}
  style={{
    borderColor: erroTexto ? '#EF4444' : undefined
  }}
/>
{erroTexto && (
  <div id="erro-edicao" role="alert">
    {erroTexto}
  </div>
)}
```

### role

Usado para definir o papel semântico de elementos quando HTML semântico não é suficiente:

```tsx
<div role="button" onClick={handleClick}>
  Elemento clicável sem ser um botão nativo
</div>

<div role="listbox">
  <div role="option">Opção 1</div>
  <div role="option">Opção 2</div>
</div>

<div role="alert" aria-live="polite">
  Mensagem de erro importante
</div>
```

### aria-live

Usado para indicar que uma região deve ser anunciada por screen readers quando seu conteúdo muda:

```tsx
<Container role="alert" aria-live="polite">
  <p>Mensagem de erro que será anunciada automaticamente</p>
</Container>
```

### aria-hidden

Usado para ocultar elementos decorativos de tecnologias assistivas:

```tsx
<Icon aria-hidden="true" role="img" />
```

## HTML Semântico

O projeto prioriza o uso de elementos HTML semânticos apropriados:

- `<nav>` para navegação
- `<main>` para conteúdo principal
- `<button>` para ações interativas
- `<form>` para formulários
- `<label>` associado a inputs via `htmlFor`
- `<header>`, `<footer>`, `<section>` quando apropriado

## Navegação por Teclado

- Todos os elementos interativos devem ser acessíveis via teclado
- Indicadores de foco visíveis devem ser mantidos (não remover `outline` sem substituir)
- Ordem de tabulação lógica deve ser respeitada

## Contraste de Cor

- Manter proporções de contraste suficientes (4.5:1 para texto normal, 3:1 para texto grande)
- Não depender apenas de cor para transmitir informação
- Usar ícones, texto ou padrões visuais adicionais

## Texto Alternativo

- Fornecer `alt` descritivo para imagens significativas
- Usar `aria-label` para elementos interativos sem texto visível
- Associar labels a inputs de formulário via `htmlFor` e `id`

## Estrutura Lógica de Cabeçalhos

- Usar níveis de cabeçalho (h1-h6) na ordem apropriada
- Criar um esboço de documento claro e hierárquico
- Não pular níveis (ex: h1 → h3)

## Gerenciamento de Foco

- Gerenciar foco apropriadamente em conteúdo dinâmico
- Restaurar foco após fechar modais
- Manter foco lógico em aplicações single-page durante navegação

## Teste de Screen Reader

- Testar componentes com screen readers (NVDA, JAWS, VoiceOver)
- Verificar que todas as views são acessíveis
- Garantir que informações importantes sejam anunciadas corretamente

## Padrões Específicos do Projeto

### Componentes de Formulário

Formulários utilizam `aria-invalid` e `aria-describedby` para indicar erros:

```tsx
<input
  aria-label="Campo de texto da resposta"
  aria-invalid={!!erroTexto}
/>
{erroTexto && (
  <div id="erro-texto" role="alert">
    {erroTexto}
  </div>
)}
```

### Componentes de Seleção

Componentes de seleção customizados utilizam `role="listbox"` e `role="option"`:

```tsx
<SelectDropdown role="listbox">
  <div role="option">Opção 1</div>
  <div role="option">Opção 2</div>
</SelectDropdown>
```

### Mensagens de Erro

Mensagens de erro utilizam `role="alert"` para serem anunciadas automaticamente:

```tsx
<div role="alert" aria-live="polite">
  Erro ao carregar dados
</div>
```
