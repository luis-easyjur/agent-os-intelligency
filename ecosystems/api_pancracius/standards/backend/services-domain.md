# Services e fluxos de domínio

## Service base por agregado

Cada agregado (ex.: Assinaturas, Pacotes) tem um Service base que injeta apenas o Repository (ex.: `PacoteService` com `PacoteRepository`). Serviços de ação estendem esse base.

## Ações em classes separadas

Operações são classes que estendem o Service base: `Cria`, `Update`, `Delete`, `Paginador`, `Log`. Cada uma expõe um método principal (ex.: `criaPacoteDePeticoes`, `update`, `delete`, `paginate`). Controller injeta a classe de ação e chama o método.

## DTOs de entrada

Serviços de escrita recebem DTOs de dados (ex.: `DadosCriaPacote`, `DadosAtualizaPacote`) construídos no controller com `XxxDto::deRequest($request)` ou `XxxDto::deRequest($request, $model)`.

## Retorno

Serviços de criação/leitura retornam Model (com `load()` quando precisar de relacionamentos). Serviços de update/delete retornam void ou bool. Paginador retorna estrutura de paginação (objeto com items e metadados).

## Sem lógica de apresentação

Service não retorna Resource nem JsonResponse; apenas Models, coleções ou escalares. A transformação para API fica no Controller/Resource.
