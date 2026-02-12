# Convenções Gerais de Desenvolvimento no Fênix

## Boas Práticas

- **Fonte única e reutilização**: Manter uma única fonte de verdade por conceito (constantes, formatadores, mapeamentos). Antes de escrever código novo, consultar a base para reutilizar o que já existir; ao identificar lógica repetida, centralizar em módulo compartilhado. Ver **single-source-reuse.md**.
- **Estrutura de Projeto Laravel**: Seguir a estrutura padrão do Laravel, mantendo arquivos nos diretórios apropriados
- **Documentação Clara**: Manter arquivos README atualizados com instruções de setup, visão geral da arquitetura e diretrizes de contribuição
- **Boas Práticas de Controle de Versão**: Usar mensagens de commit claras e descritivas
- **Configuração de Ambiente**: Usar arquivo .env para configurações de ambiente; nunca commitar secrets ou API keys no controle de versão
- **Respeitar Arquitetura MVC**: Seguir o padrão MVC do Laravel, mantendo a separação de responsabilidades
- **Padrões de Nomenclatura**:
  - Controllers: Singular + Controller (ex: AssinaturaController)
  - Models: Singular, organizados por domínio (ex: Processo, Assinatura)
  - Migrations: Data + descrição (ex: 2026_01_19_create_processos_table)
  - **Tabelas: Todas têm prefixo `tb_` + plural** (ex: `tb_processos`, `tb_assinaturas_produtos_ia`)
  - Rotas: Substantivos no plural (ex: /api/assinaturas, /api/processos/areas)

## Fluxo de Trabalho

- **Branches**: Criar branches específicos para features/bugs (ex: feature/upload-file, fix/download-issue)
- **Pull Requests**: Criar Pull Requests com descrição clara das mudanças
- **Code Reviews**: Realizar code reviews antes de mesclar código
- **Testes**: Adicionar testes para novas funcionalidades e garantir que todos os testes passem

## Documentação de API

- **Scramble**: Documentar endpoints de API usando Scramble (dedoc/scramble)
- **Anotações PHPDoc**: Usar anotações PHPDoc nos controllers para documentar endpoints
- **Exemplos de Uso**: Fornecer exemplos de request/response para cada endpoint através de anotações
- **Parâmetros**: Documentar todos os parâmetros, incluindo tipo e se são obrigatórios
