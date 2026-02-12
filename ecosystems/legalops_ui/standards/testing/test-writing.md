## Melhores práticas de cobertura de testes

- **Escrever Testes Mínimos Durante Desenvolvimento**: NÃO escrever testes para cada mudança ou etapa intermediária. Focar em completar a implementação do recurso primeiro, depois adicionar testes estratégicos apenas em pontos de conclusão lógicos
- **Testar Apenas Fluxos Principais do Usuário**: Escrever testes exclusivamente para caminhos críticos e fluxos de trabalho principais do usuário. Pular escrita de testes para utilitários não críticos e fluxos de trabalho secundários até se/quando você for instruído a fazê-lo.
- **Adiar Teste de Casos Extremos**: NÃO testar casos extremos, estados de erro ou lógica de validação a menos que sejam críticos para o negócio. Estes podem ser abordados em fases dedicadas de testes, não durante desenvolvimento de recursos.
- **Testar Comportamento, Não Implementação**: Focar testes no que o código faz, não em como faz, para reduzir fragilidade
- **Nomes de Teste Claros**: Usar nomes descritivos que expliquem o que está sendo testado e o resultado esperado
- **Mockar Dependências Externas**: Isolar unidades mockando bancos de dados, APIs, sistemas de arquivos e outros serviços externos
- **Execução Rápida**: Manter testes unitários rápidos (milissegundos) para que desenvolvedores os executem frequentemente durante desenvolvimento
