# Padrões de Código no 123jus-ui

## Princípios Gerais
- Priorizar código simples, explícito e legível
- Evitar abstrações desnecessárias
- Preferir clareza a "elegância"
- Código deve ser fácil de manter por outro desenvolvedor

## Qualidade de Código: Obrigatória

**Todo código novo ou alterado DEVE seguir boas práticas de clean code e os padrões do TypeScript/React.**

### Regras Obrigatórias:

- **Componentes pequenos e focados**: Máximo de responsabilidade única por componente
- **Hooks customizados**: Extrair lógica reutilizável para hooks customizados
- **Nomes descritivos**: Variáveis, funções e componentes devem revelar intenção claramente
- **Código legível**: Priorizar clareza sobre "elegância" ou otimizações prematuras
- **Sem duplicação desnecessária**: Aplicar princípio DRY quando apropriado
- **Comentários quando necessário**: Explicar o *porquê*, não o *o quê*
- **Type Safety**: Usar tipagem forte do TypeScript para props, estados e retornos
- **TypeScript Strict Mode**: Seguir modo strict do TypeScript
- **Componentes Funcionais**: Sempre usar componentes funcionais com hooks

### Melhorias e Manutenção:

- **Melhorar código existente**: Ao alterar código, melhorar sua qualidade
- **Refatorações focadas**: Permitidas e encorajadas quando melhoram legibilidade ou manutenibilidade
- **Respeitar arquitetura de componentes**: Seguir a arquitetura baseada em componentes React

### O que NÃO é permitido:

- **Violar convenções do TypeScript/React**: Não divergir das convenções e práticas recomendadas
- **Mudanças estruturais amplas**: Não reestruturar grandes partes do código sem solicitação explícita
- **Misturar padrões**: Não misturar diferentes padrões arquiteturais no mesmo módulo

## Convenções
- Nomes de variáveis, funções e hooks em camelCase
- Componentes React em PascalCase
- Interfaces TypeScript prefixadas com `I` (ex: `IProposta`)
- Hooks customizados com prefixo `use` (ex: `useAuth`, `useCepLookup`)
- Evitar abreviações obscuras
- Comentários devem explicar o *porquê*, não o *o quê*

## Organização do Código
- Páginas para rotas principais
- Componentes reutilizáveis em `components/`
- Hooks customizados em `hooks/`
- Services para chamadas de API em `services/`
- Utilitários em `utils/` e `Utils/`
- Contextos para estado global em `contexts/`
- Tipos TypeScript em `types/`
- Estilos com styled-components ou arquivos `styles.ts`
- Preferir composição sobre herança

## Decisões da IA
A IA deve:
- Seguir os padrões do TypeScript/React
- Seguir os padrões existentes no código
- Perguntar antes de introduzir novos padrões
- Justificar decisões não triviais
- **Sempre aplicar clean code** em qualquer código novo ou alterado
- **Usar hooks e Context API** para gerenciamento de estado

A IA NÃO deve:
- Reestruturar código sem solicitação explícita
- Alterar estilo apenas por preferência
- Misturar padrões diferentes no mesmo módulo
- **Introduzir novas bibliotecas ou mudanças de stack sem solicitação explícita**

## Idioma
- Código e comentários em inglês (nomes de componentes, funções, variáveis)
- Mensagens de erro e conteúdo visível ao usuário em português
- Props e interfaces podem usar português quando apropriado
