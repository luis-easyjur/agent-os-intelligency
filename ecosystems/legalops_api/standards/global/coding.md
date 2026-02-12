# Padrões de Código no 123jus-api

## Princípios Gerais
- Priorizar código simples, explícito e legível
- Evitar abstrações desnecessárias
- Preferir clareza a "elegância"
- Código deve ser fácil de manter por outro desenvolvedor

## Qualidade de Código: Obrigatória

**Todo código novo ou alterado DEVE seguir boas práticas de clean code e os padrões do TypeScript/Node.js.**

### Regras Obrigatórias:

- **Funções pequenas e focadas**: Máximo de responsabilidade única por função
- **Nomes descritivos**: Variáveis, funções e classes devem revelar intenção claramente
- **Código legível**: Priorizar clareza sobre "elegância" ou otimizações prematuras
- **Sem duplicação desnecessária**: Aplicar princípio DRY quando apropriado
- **Comentários quando necessário**: Explicar o *porquê*, não o *o quê*
- **Type Safety**: Usar tipagem forte do TypeScript para parâmetros e retornos
- **TypeScript Strict Mode**: Seguir modo strict do TypeScript

### Melhorias e Manutenção:

- **Melhorar código existente**: Ao alterar código, melhorar sua qualidade
- **Refatorações focadas**: Permitidas e encorajadas quando melhoram legibilidade ou manutenibilidade
- **Respeitar arquitetura MVC**: Seguir a arquitetura MVC adaptada para Express.js

### O que NÃO é permitido:

- **Violar convenções do TypeScript**: Não divergir das convenções e práticas recomendadas do TypeScript
- **Mudanças estruturais amplas**: Não reestruturar grandes partes do código sem solicitação explícita
- **Misturar padrões**: Não misturar diferentes padrões arquiteturais no mesmo módulo

## Convenções
- Nomes de variáveis, métodos e classes devem ser claros e descritivos
- Classes em PascalCase, métodos e variáveis em camelCase
- Interfaces TypeScript prefixadas com `I` (ex: `IProposta`)
- Nomes de tabelas e colunas em snake_case
- Evitar abreviações obscuras
- Comentários devem explicar o *porquê*, não o *o quê*

## Organização do Código
- Controllers enxutos, delegando lógica para Services
- Services para lógica de negócio complexa
- Repositories para acesso a dados
- Interfaces TypeScript para definição de estruturas de dados
- Validators para validação de entrada
- Jobs para processamento agendado
- Preferir composição a herança quando possível

## Decisões da IA
A IA deve:
- Seguir os padrões do TypeScript/Node.js
- Seguir os padrões existentes no código
- Perguntar antes de introduzir novos padrões
- Justificar decisões não triviais
- **Sempre aplicar clean code** em qualquer código novo ou alterado
- **Usar injeção de dependência** ao invés de instanciar classes diretamente

A IA NÃO deve:
- Reestruturar código sem solicitação explícita
- Alterar estilo apenas por preferência
- Misturar padrões diferentes no mesmo módulo
- **Introduzir novas camadas arquiteturais ou mudanças de stack sem solicitação explícita**

## Idioma
- Código e comentários em inglês (nomes de classes, métodos, variáveis)
- Mensagens de erro e conteúdo visível ao usuário em português
- Nomes de tabelas e colunas do banco de dados em português
