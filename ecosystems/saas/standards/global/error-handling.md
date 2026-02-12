# Padrões de Tratamento de Erros

## Boas Práticas

- **Mensagens Amigáveis ao Usuário**: Fornecer mensagens de erro claras e acionáveis aos usuários sem expor detalhes técnicos ou informações de segurança
- **Falhar Rápido e Explicitamente**: Validar entrada e verificar pré-condições cedo; falhar com mensagens de erro claras ao invés de permitir estado inválido
- **Tipos de Exceção Específicos**: Usar tipos de exceção/erro específicos ao invés de genéricos para permitir tratamento direcionado
- **Tratamento de Erro Centralizado**: Tratar erros em limites apropriados (controllers, camadas de API) ao invés de espalhar blocos try-catch em todos os lugares
- **Degradação Graciosa**: Projetar sistemas para degradar graciosamente quando serviços não críticos falham ao invés de quebrar completamente
- **Estratégias de Retry**: Implementar exponential backoff para falhas transitórias em chamadas de serviços externos
- **Limpar Recursos**: Sempre limpar recursos (file handles, conexões) em blocos finally ou mecanismos equivalentes
