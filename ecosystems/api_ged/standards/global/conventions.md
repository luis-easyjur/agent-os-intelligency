# Convenções Gerais de Desenvolvimento no APP-GED

## Boas Práticas

- **Estrutura de Projeto Laravel**: Seguir a estrutura padrão do Laravel, mantendo arquivos nos diretórios apropriados
- **Documentação Clara**: Manter arquivos README atualizados com instruções de setup, visão geral da arquitetura e diretrizes de contribuição
- **Boas Práticas de Controle de Versão**: Usar mensagens de commit claras e descritivas
- **Configuração de Ambiente**: Usar arquivo .env para configurações de ambiente; nunca commitar secrets ou API keys no controle de versão
- **Respeitar Arquitetura MVC**: Seguir o padrão MVC do Laravel, mantendo a separação de responsabilidades
- **Padrões de Nomenclatura**:
  - Controllers: Singular + Controller (ex: FileController)
  - Models: Singular (ex: File)
  - Migrations: Data + descrição (ex: 2026_01_19_create_files_table)
  - Tabelas: Plural (ex: files)
  - Rotas: Substantivos no plural (ex: /api/files)

## Fluxo de Trabalho

- **Branches**: Criar branches específicos para features/bugs (ex: feature/upload-file, fix/download-issue)
- **Pull Requests**: Criar Pull Requests com descrição clara das mudanças
- **Code Reviews**: Realizar code reviews antes de mesclar código
- **Testes**: Adicionar testes para novas funcionalidades e garantir que todos os testes passem

## Documentação de API

- **Swagger/OpenAPI**: Documentar endpoints de API usando anotações Swagger
- **Exemplos de Uso**: Fornecer exemplos de request/response para cada endpoint
- **Parâmetros**: Documentar todos os parâmetros, incluindo tipo e se são obrigatórios
