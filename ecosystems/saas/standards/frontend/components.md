# Padrões de Componentes e Elementos de UI

## Contexto do Projeto

Este é um projeto PHP monolítico que utiliza Bootstrap 5 e jQuery. Não há componentes React/Vue ou sistema de componentes moderno.

## Boas Práticas

- **Reutilização de Código HTML/PHP**: Criar includes e partials PHP para elementos de UI reutilizáveis (modais, formulários, tabelas)
- **Padrão de Modais**: Usar arquivos `JANELA_*.php` para modais reutilizáveis
- **Classes Bootstrap Consistentes**: Usar classes do Bootstrap de forma consistente em elementos similares
- **JavaScript Modular**: Organizar código jQuery em funções reutilizáveis quando apropriado
- **Nomenclatura Clara**: Usar nomes descritivos para IDs, classes CSS e funções JavaScript
- **Separação de Responsabilidades**: Manter HTML, CSS e JavaScript organizados e separados quando possível
- **Documentação de Includes**: Documentar parâmetros e uso de includes PHP reutilizáveis
