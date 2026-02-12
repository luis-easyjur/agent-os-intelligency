# HTTP Resources (API Resources)

## Organização

Resources em `app/Http/Resources/` por domínio (Assinatura, User, Documento, etc.). Um Resource por contexto de resposta (Show, Index, Collection). Nomenclatura: XxxResource (item), XxxResourceCollection ou XxxIndexResourceCollection (listagem), XxxShowResource quando envolve item em wrapper.

## Show com wrapper

Recursos de show podem envolver o item em `id`, `tipo` (nome do recurso) e `data` (outro Resource). Ex.: AssinaturaShowResource retorna `['id' => $this->id, 'tipo' => 'Assinatura', 'data' => new AssinaturaResource($this)]`. Mantém consistência com o front.

## Collection de listagem

Collections de listagem incluem `tipo`, `data` (array ou toArray da collection) e, quando fizer sentido, `count` e `permissions` (ex.: UserIndexResourceCollection com has_permissao_modulo, has_permissao_apenas_ver_modulo). O front usa para exibir lista e habilitar/desabilitar ações.

## AutocompleteResource

Listas de autocomplete usam `App\Http\Resources\Common\AutocompleteResource::collection($result)`. Cada item deve expor `id` e `autocomplete_label` (ou equivalente no toArray do Resource). O model que alimenta o Resource deve implementar Autocompletable e expor o Attribute correspondente.

## whenLoaded

Usar `$this->whenLoaded('relacionamento')` para incluir relacionamentos apenas quando carregados (evitar N+1 e controlar payload). Ex.: AssinaturaResource usa `whenLoaded('escritorio')`, `whenLoaded('produto')`.

## Uso no controller

Controller retorna `new XxxResource($model)` ou `new XxxResourceCollection($collection)` (ou `XxxResource::collection($collection)`). Para store com 201: `$resource->response()->setStatusCode(201)`.
