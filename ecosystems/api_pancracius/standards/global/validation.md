# Padrões de Validação

## Boas Práticas

- **Validar no Servidor**: Sempre validar no servidor; nunca confiar apenas em validação do lado do cliente para segurança ou integridade de dados
- **Lado do Cliente para UX**: Usar validação do lado do cliente para fornecer feedback imediato ao usuário, mas duplicar verificações no servidor
- **Falhar Cedo**: Validar entrada o mais cedo possível e rejeitar dados inválidos antes do processamento
- **Mensagens de Erro Específicas**: Fornecer mensagens de erro claras e específicas por campo que ajudem usuários a corrigir sua entrada
- **Allowlists ao Invés de Blocklists**: Quando possível, definir o que é permitido ao invés de tentar bloquear tudo que não é
- **Validação de Tipo e Formato**: Verificar tipos de dados, formatos, intervalos e campos obrigatórios sistematicamente
- **Sanitizar Entrada**: Sanitizar entrada do usuário para prevenir ataques de injeção (SQL, XSS, command injection)
- **Validação de Regras de Negócio**: Validar regras de negócio (ex: saldo suficiente, datas válidas) na camada apropriada da aplicação
- **Validação Consistente**: Aplicar validação consistentemente em todos os pontos de entrada (formulários web, endpoints de API, jobs em background)
