# Convenções Gerais de Desenvolvimento no 123jus-api

## Boas Práticas

- **Fonte única e reutilização**: Manter uma única fonte de verdade por conceito (constantes, formatadores, mapeamentos). Antes de escrever código novo, consultar a base para reutilizar o que já existir; ao identificar lógica repetida, centralizar em módulo compartilhado. Ver **single-source-reuse.md**.
- **Estrutura de Projeto Node.js/TypeScript**: Seguir a estrutura padrão do projeto, mantendo arquivos nos diretórios apropriados
- **Documentação Clara**: Manter arquivos README atualizados com instruções de setup, visão geral da arquitetura e diretrizes de contribuição
- **Boas Práticas de Controle de Versão**: Usar mensagens de commit claras e descritivas
- **Configuração de Ambiente**: Usar arquivo `.env` para configurações de ambiente; nunca commitar secrets ou API keys no controle de versão
- **Respeitar Arquitetura MVC**: Seguir o padrão MVC adaptado para Express.js, mantendo a separação de responsabilidades
- **Padrões de Nomenclatura**:
  - Controllers: PascalCase + Controller (ex: `PropostaController.ts`)
  - Services: PascalCase + Service (ex: `PropostaService.ts`)
  - Repositories: PascalCase + Repositories (ex: `PropostaRepositories.ts`)
  - Interfaces: Prefixo `I` + PascalCase (ex: `IProposta.ts`)
  - Migrations: Timestamp + descrição (ex: `1747679602693_create_table_notificacoes.js`)
  - **Tabelas: Nomes em português, singular ou plural** (ex: `proposta`, `orientacao`, `notificacoes`)
  - Rotas: Substantivos no singular (ex: `/proposta`, `/orientacao`)

## Fluxo de Trabalho

- **Branches**: Criar branches específicos para features/bugs (ex: `feature/upload-file`, `fix/download-issue`)
- **Pull Requests**: Criar Pull Requests com descrição clara das mudanças
- **Code Reviews**: Realizar code reviews antes de mesclar código
- **Testes**: Adicionar testes para novas funcionalidades e garantir que todos os testes passem

## Estrutura de Diretórios

- `src/api/controllers/` - Controladores
- `src/api/services/` - Serviços de negócio
- `src/api/repositories/` - Repositórios de acesso a dados
- `src/api/validators/` - Validadores de entrada
- `src/api/middlewares/` - Middlewares Express
- `src/types/` - Interfaces TypeScript
- `src/routes/` - Definições de rotas
- `src/helpers/` - Funções auxiliares
- `src/config/` - Configurações
- `src/jobs/` - Jobs e tarefas agendadas
- `migrations/` - Migrações de banco de dados

## Padrões de Resposta

Todas as respostas da API seguem formato padronizado:

### Sucesso
```json
{
  "code": 200,
  "status": "success",
  "message": "Ação realizada com sucesso",
  "result": { ... }
}
```

### Erro
```json
{
  "code": 200,
  "status": "error",
  "message": "Ocorreu um erro",
  "result": [
    {
      "field": "campo",
      "message": "Mensagem de erro específica"
    }
  ]
}
```

## Autenticação

- Todas as rotas protegidas requerem header `Authorization: Bearer <token>`
- Token JWT contém informações do usuário (id, tipo)
- Middleware `auth` adiciona informações ao `req`

## Validação

- Validators customizados em `src/api/validators/`
- Validação ocorre antes de processar requisições
- Erros retornados em formato padronizado
