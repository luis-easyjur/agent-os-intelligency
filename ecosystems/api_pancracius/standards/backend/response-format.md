# Formato de Resposta da API

## Envelope apiResponse

Trait `ApiResponse` (Controller base) retorna envelope padronizado:

```php
$this->apiResponse(string $message, int $statusCode = 200, $data = []);
```

Resposta: `datetime`, `code`, `error` (true se status >= 300), `message`, `data`. Se `$data` for JsonResource, é convertido com `toArray(request())`.

Usar quando a ação é apenas confirmação com mensagem (ex.: webhook registrado, sincronização solicitada, assistente atualizado). Auth e Workflow usam com Resource em `data`.

## Quando usar cada formato

- **apiResponse**: mensagem de sucesso/erro sem recurso estruturado, ou com dados auxiliares (ex.: Auth com LoginResource em data).
- **Resource direto**: `return new XResource($model)` ou `$resource->response()->setStatusCode(201)` para CRUD que devolve o recurso (show, store, index com collection).
- **response()->json('...')** ou **JsonResponse**: ações que só precisam de mensagem (ex.: habilita/desabilita usuário). Preferir apiResponse para manter envelope quando o front esperar `datetime`/`code`/`message`.

## Status HTTP

- `store` (criação): 201, retornar Resource do recurso criado.
- `destroy`: 204, `return new JsonResponse(null, 204)`.
- Atualizações que não devolvem recurso: 200 e mensagem (apiResponse ou json).

## Resource de listagem

Collections de listagem incluir `tipo`, `data` e, quando fizer sentido, `count` e `permissions` (ex.: UserIndexResourceCollection).

## Show com wrapper

Recursos de show podem envolver em `id`, `tipo`, `data` (ex.: AssinaturaShowResource) para consistência com o front.
