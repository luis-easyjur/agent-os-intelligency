# Padrões de Alterações no Banco de Dados

## Contexto do Projeto

As migrações de banco de dados são criadas em um projeto separado chamado **fenix**. Sempre que for necessário criar ou editar tabelas, as migrações devem ser criadas no projeto fenix, não no projeto atual.

## Boas Práticas

- **Scripts SQL Documentados**: Documentar claramente o propósito de cada script SQL de alteração
- **Scripts Reversíveis**: Quando possível, incluir script de rollback junto com alterações
- **Mudanças Pequenas e Focadas**: Manter cada alteração focada em uma única mudança lógica
- **Backup Antes de Alterações**: Sempre fazer backup antes de executar alterações em produção
- **Testar em Desenvolvimento**: Testar todas as alterações em ambiente de desenvolvimento primeiro
- **Gerenciamento de Índices**: Criar índices em tabelas grandes cuidadosamente, considerando impacto em performance durante criação
- **Nomenclatura de Scripts**: Usar nomes descritivos que indiquem data e propósito (ex: `20250112_adiciona_campo_status.sql`)
- **Controle de Versão**: Sempre commitar scripts SQL no controle de versão
- **Documentação de Impacto**: Documentar impacto esperado de alterações em outras partes do sistema
