# Padrões de Escrita de Testes

## Contexto do Projeto

O projeto utiliza **Cypress** para testes E2E (end-to-end). Não há framework de testes unitários PHP ativo no projeto principal.

## Boas Práticas

- **Escrever Testes Mínimos Durante Desenvolvimento**: NÃO escrever testes para cada mudança ou passo intermediário. Focar em completar a implementação da feature primeiro, depois adicionar testes estratégicos apenas em pontos de conclusão lógicos
- **Testar Apenas Fluxos de Usuário Principais**: Escrever testes E2E exclusivamente para caminhos críticos e fluxos de usuário primários. Pular escrita de testes para utilitários não críticos e fluxos secundários até que seja instruído a fazê-lo
- **Adiar Teste de Edge Cases**: NÃO testar edge cases, estados de erro ou lógica de validação a menos que sejam críticos para o negócio. Estes podem ser abordados em fases dedicadas de teste, não durante desenvolvimento de features
- **Testar Comportamento, Não Implementação**: Focar testes no que o código faz, não em como faz, para reduzir fragilidade
- **Nomes de Teste Claros**: Usar nomes descritivos que expliquem o que está sendo testado e o resultado esperado
- **Testes E2E com Cypress**: Usar Cypress para testar fluxos completos do usuário, simulando interações reais no navegador
