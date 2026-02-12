# Clientes de APIs externas

## Base HttpClient

Clientes estendem `App\ExternalServices\ApiClients\HttpClient`. O base oferece `get`, `post`, `put`, `patch`, `delete` com retry (configurável), timeout e log de erro genérico (Elastic). Construtor recebe `Log` (Elastic).

## URL base

Cliente específico implementa `getBaseUrl(): string` (ex.: AsaasClient). A URL pode depender de ambiente ou de escritório (sandbox vs produção). Montar URL final no método de ação: `$this->getBaseUrl() . $path`.

## Tratamento de erro da API

Após chamar `$this->post(...)` (ou outro), verificar resposta (ex.: `$httpClientDTO->failed`). Se a API retornou erro de negócio, chamar método próprio do cliente (ex.: `processAsaasClientError($httpClientDTO, $path)`): logar no Elastic com índice e dados adequados e lançar exceção específica do cliente (ex.: `AsaasClientException`).

## Ações em classes específicas

Operações contra a API ficam em classes que estendem o cliente (ex.: `CancelarNF extends AsaasClient`). Método público (ex.: `cancelarNF($token, $id, $data)`) monta path, headers, chama get/post e trata erro com o método de erro do cliente.

## Exceções

Cada integração pode ter exceção própria (ex.: `AsaasClientException`) com status e payload para o controller tratar.
