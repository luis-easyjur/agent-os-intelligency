# Padrões de Estilo de Código

## Boas Práticas

- **Convenções de Nomenclatura Consistentes**: Estabelecer e seguir convenções de nomenclatura para variáveis, funções, classes e arquivos em todo o código
- **Formatação Automatizada**: Manter estilo de código consistente (indentação, quebras de linha, etc.) usando ferramentas como Prettier quando configurado
- **Nomes Significativos**: Escolher nomes descritivos que revelem a intenção; evitar abreviações e variáveis de uma letra, exceto em contextos estreitos
- **Funções Pequenas e Focadas**: Manter funções pequenas e focadas em uma única tarefa para melhor legibilidade e testabilidade
- **Indentação Consistente**: Usar indentação consistente (espaços ou tabs) e configurar editor/linter para aplicá-la
- **Remover Código Morto**: Deletar código não utilizado, blocos comentados e imports ao invés de deixá-los como lixo
- **Princípio DRY**: Evitar duplicação extraindo lógica comum em funções ou módulos reutilizáveis. Ver também **single-source-reuse.md** para regras de fonte única e consulta à base antes de escrever código novo.

## Convenções TypeScript/React

- **CamelCase**: Variáveis, funções, métodos e hooks em camelCase
- **PascalCase**: Componentes React, classes e interfaces em PascalCase
- **UPPER_CASE**: Constantes em UPPER_CASE
- **Interfaces com prefixo I**: Interfaces TypeScript prefixadas com `I` (ex: `IProposta`, `IOrientacao`)
- **Arquivos em PascalCase**: Componentes React em PascalCase (ex: `ButtonDefault.tsx`, `InputDefault.tsx`)
- **Arquivos em camelCase**: Utilitários, hooks e services em camelCase (ex: `useCepLookup.ts`, `api.ts`)
- **Hooks com prefixo use**: Hooks customizados começam com `use` (ex: `useAuth`, `useCepLookup`)

## Exemplos

```typescript
// Variáveis e funções em camelCase
const idProposta = 123;
function criarProposta(data: IProposta) { }

// Componentes React em PascalCase
const ButtonDefault: React.FC<ButtonProps> = ({ children }) => { }

// Hooks customizados com prefixo use
const useCepLookup = () => { }

// Interfaces com prefixo I
interface IProposta { }

// Constantes em UPPER_CASE
const MAX_FILE_SIZE = 1024 * 1024; // 1MB

// Arquivos de componentes
// ButtonDefault/index.tsx
// InputDefault/index.tsx
```
