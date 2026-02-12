# Fonte única e reutilização de código

Este standard define práticas obrigatórias para evitar duplicação e manter uma única fonte de verdade por conceito. Aplica-se a qualquer linguagem, framework ou projeto.

## Regra fundamental

- **Consultar antes de escrever**: Antes de implementar qualquer função, constante, mapeamento ou utilitário, procurar na base de código se já existe implementação equivalente ou reutilizável. Só criar código novo quando não houver fonte adequada.

## Fonte única por conceito

- **Um conceito, um lugar**: Cada noção de negócio ou técnica (status, labels, formatadores, mapeamentos, constantes de domínio) deve ter uma única definição no projeto. Múltiplos pontos que dependem do mesmo conceito devem importar ou referenciar essa definição, nunca copiá-la.
- **Constantes e enums**: Valores fixos (status, códigos, chaves) devem ficar em módulo de constantes ou equivalente. Nunca espalhar literais idênticos em vários arquivos; usar referência à constante.
- **Formatadores e helpers**: Funções que formatam dados (moeda, data, tamanho de arquivo, etc.) ou que aplicam regras repetidas devem ficar em utilitários compartilhados. Componentes ou serviços importam e usam; não reimplementam a lógica.
- **Mapeamentos valor–label**: Listas, dicionários ou mapas que convertem valor interno em texto de exibição (ex.: status → label) devem existir em um único módulo. Telas, relatórios e APIs consomem essa fonte.

## Quando identificar necessidade de centralizar

- **Centralizar ao invés de duplicar**: Ao perceber que vai escrever lógica igual ou muito parecida à que já existe em outro arquivo, parar e centralizar: extrair para módulo/utilitário compartilhado e substituir as ocorrências por import ou chamada. Não copiar trechos entre arquivos.
- **Refatorar duplicação existente**: Se ao revisar ou estender o código encontrar lógica duplicada (mesma função, mesmo mapeamento, mesmas constantes em vários lugares), tratar como dívida técnica: criar fonte única e migrar os usos. Priorizar quando o conceito for crítico (ex.: status, regras de negócio).

## Fluxo de trabalho recomendado

- **Antes de implementar**: (1) Buscar na base por nomes de função, constantes ou padrões relacionados ao que se vai implementar. (2) Se existir, reutilizar ou estender com cuidado para não quebrar usos atuais. (3) Se não existir e o conceito for reutilizável, criar em local compartilhado (ex.: `utils/`, `constants/`, `lib/`) e usar a partir daí.
- **Ao adicionar comportamento**: Verificar se o novo comportamento pode ser coberto por utilitário ou constante já existente (ex.: novo status → adicionar à lista de constantes e ao mapeamento de labels, não criar nova string em um único componente).

## Benefícios esperados

- Manutenção previsível: alterar regra, label ou formato em um único ponto.
- Menos inconsistência e bugs por esquecer um dos locais duplicados.
- Código mais enxuto e legível; menos repetição.

## Relação com outros standards

- **coding-style.md**: O princípio DRY e a preferência por funções pequenas e reutilizáveis complementam este standard.
- **conventions.md**: A estrutura de projeto deve prever pastas ou módulos para constantes e utilitários compartilhados, de forma que a “fonte única” tenha local óbvio.
