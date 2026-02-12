# Form Requests e validação de entrada

## Organização

Form Requests em `app/Http/Requests/` por domínio (Assinaturas, Users, Documentos, etc.). Um Request por ação ou contexto (CriaXxxRequest, AcessaXxxRequest, PaginaXxxRequest, AtualizaXxxRequest).

## Traits comuns

- **TemBancoVariavel**: `getTargetConnection()` — lê `banco` do request, usa `BancoDeDados::tryFrom()`, retorna `$banco->name` ou string vazia. Usar quando a rota aceita banco variável.
- **VerificaRecursoExiste**: `verificaRecursoExiste(string $alvo, ?string $recurso)` — obtém parâmetro de rota com `request()->route($alvo)`, lança `NotFoundException` se não existir. Usar em authorize ou withValidator quando o recurso da rota deve existir.
- **VeririficaRecursoDoEscritorio**: `verificaRecursoDoEscritorioViaRota(string $alvo)` ou `verificaRecursoDoEscritorioViaModel(Model $alvo)` — valida que o recurso pertence ao escritório do usuário (ou que o usuário é escritório 1). Lança `NotFoundException` se não pertencer. Usar para recursos restritos por escritório.

## Interface PaginationRequest

Requests de paginação podem implementar `PaginationRequest` (métodos `validated()` e `rules()`). DTOs de paginação são construídos no controller a partir do request (ex.: `PacotesPaginationDto::deRequest($request)`).

## Uso no controller

Controller recebe Form Request como type-hint; a validação roda antes do método. DTOs são criados no controller com `XxxDto::deRequest($request)` ou `new XxxDto($request)`, não obrigatoriamente no Request.
