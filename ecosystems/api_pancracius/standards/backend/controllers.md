# Padrão de Controllers

## Controller fino

Controller não contém lógica de negócio. Recebe FormRequest (ou Request), injeta Service (e eventualmente Model por route binding), converte request em DTO quando necessário (`XxxDto::deRequest($request)`), chama o service e retorna Resource, apiResponse ou JsonResponse.

## Autocomplete

Usar trait `HasAutocomplete`. O método do controller chama `$this->executeAutocomplete($request, $service)`. Query string: `term`. Retorno: `AutocompleteResource::collection($result)`.

O Service deve implementar `AutocompletableService`. O model (ou objeto retornado) deve expor `id` e `autocomplete_label` (atributo ou acessor) para o AutocompleteResource.

## Endpoint indexBehavior

Para listagens que dependem de permissões do usuário, expor além de `index` um endpoint `behavior` (ex.: `GET /users/behavior`) que retorna Resource com permissões e comportamento da tela (ex.: UserIndexBehaviorResource). O front usa para saber o que exibir/habilitar.

## Convenções de método e retorno

- `index`: FormRequest + Service (Paginador), retorno ResourceCollection (tipo, data, count, permissions quando aplicável).
- `show`: Request de acesso + Model (binding) + eventualmente Service, retorno ShowResource.
- `store`: CriaRequest + Service, DTO quando necessário, retorno Resource com status 201.
- `update`: UpdateRequest + Model + Service, retorno Resource ou mensagem (200).
- `destroy`: Request + Model + Service, retorno 204 (JsonResponse null).
