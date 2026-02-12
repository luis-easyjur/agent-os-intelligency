# Padrões de Código

## Princípios Gerais
- Priorizar código simples, explícito e legível
- Evitar abstrações desnecessárias
- Preferir clareza a "elegância"
- Código deve ser fácil de manter por outro desenvolvedor

## Qualidade de Código: Obrigatória

**Todo código novo ou alterado DEVE seguir boas práticas de clean code, independentemente da arquitetura legada do projeto.**

### Regras Obrigatórias:

- **Funções pequenas e focadas**: Máximo de responsabilidade única por função
- **Nomes descritivos**: Variáveis, funções e classes devem revelar intenção claramente
- **Código legível**: Priorizar clareza sobre "elegância" ou otimizações prematuras
- **Sem duplicação desnecessária**: Aplicar princípio DRY quando apropriado
- **Comentários quando necessário**: Explicar o *porquê*, não o *o quê*

### Melhorias Locais e Incrementais:

- **Melhorar código existente**: Ao alterar código legado, melhorar sua qualidade de forma local
- **Refatorações pequenas**: Permitidas e encorajadas quando melhoram legibilidade ou manutenibilidade
- **Sem mudanças arquiteturais**: Melhorias devem ser dentro do contexto existente, sem introduzir novas camadas ou padrões

### O que NÃO é permitido:

- **Usar arquitetura legada como desculpa**: Não é permitido escrever código de baixa qualidade sob o pretexto de "respeitar o legado"
- **Introduzir padrões arquiteturais**: Não aplicar DDD, Clean Architecture ou outros padrões formais sem solicitação explícita
- **Mudanças estruturais amplas**: Não reestruturar grandes partes do código sem solicitação

## Convenções
- Nomes de variáveis, métodos e classes devem ser claros e descritivos
- Evitar abreviações obscuras
- Comentários devem explicar o *porquê*, não o *o quê*

## Organização
- Funções pequenas e com responsabilidade única
- Evitar funções longas ou com múltiplas responsabilidades
- Preferir composição a herança quando possível

## Decisões da IA
A IA deve:
- seguir os padrões existentes no código
- perguntar antes de introduzir novos padrões
- justificar decisões não triviais
- **sempre aplicar clean code** em qualquer código novo ou alterado
- **melhorar qualidade** de código legado quando fizer alterações nele

A IA NÃO deve:
- reestruturar código sem solicitação explícita
- alterar estilo apenas por preferência
- misturar padrões diferentes no mesmo módulo
- **usar arquitetura legada como justificativa para código de baixa qualidade**
- **introduzir novas camadas arquiteturais, padrões formais (DDD, Clean Architecture) ou mudanças de stack sem solicitação explícita**

## Idioma
- Código e comentários devem ser escritos em português, quando aplicável