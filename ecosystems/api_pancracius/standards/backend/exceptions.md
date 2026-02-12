# Padrões de Exceções no Fênix

## Visão Geral

O Fênix utiliza um sistema de exceções customizado baseado em `BaseException` que fornece tratamento padronizado de erros com respostas JSON consistentes para APIs.

## BaseException

Todas as exceções customizadas do Fênix estendem `BaseException`, que fornece:

- Resposta JSON padronizada
- Código de status HTTP configurável
- Array de erros detalhados
- Formatação consistente de respostas de erro

```php
namespace App\Exceptions;

use Exception;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Request;

class BaseException extends Exception
{
    protected int $responseCode = 422;
    protected array $errors = [];

    public function render(Request $request): JsonResponse
    {
        return response()->json([
            'datetime' => date('Y-m-d H:i:s'),
            "error" => true,
            "code" => $this->code,
            "message" => $this->message,
            "errors" => $this->errors
        ], $this->responseCode);
    }

    public function getErros(): array
    {
        return $this->errors;
    }

    public function getJsonErrors(): string
    {
        return json_encode($this->errors);
    }
}
```

## Estrutura de Exceções

As exceções são organizadas por domínio em `app/Exceptions/`:

```
app/Exceptions/
├── Auth/
│   └── AuthFailedException.php
├── Database/
│   ├── InvalidDatabaseSelectedException.php
│   └── NoDatabaseSelectedException.php
├── Assinaturas/
│   └── EscritorioJaPossuiProdutoException.php
├── Financeiro/
│   └── Contratos/
│       └── SincronizaContratoHubspotInvalidoException.php
└── ...
```

## Padrões de Criação

### Exceção Simples

```php
namespace App\Exceptions\Auth;

use App\Exceptions\BaseException;

class AuthFailedException extends BaseException
{
    public function __construct(string $message = "Falha na autenticação")
    {
        parent::__construct($message);
        $this->code = 401;
        $this->responseCode = 401;
    }
}
```

### Exceção com Erros Detalhados

```php
namespace App\Exceptions\Assinaturas;

use App\Exceptions\BaseException;

class EscritorioJaPossuiProdutoException extends BaseException
{
    public function __construct()
    {
        parent::__construct("Escritório já possui este produto");
        $this->code = 422;
        $this->errors = [
            'id_empresa' => ['Escritório já possui uma assinatura para este produto']
        ];
    }
}
```

### Exceção com Array de Mensagens

```php
namespace App\Exceptions\Financeiro\Contratos;

use App\Exceptions\BaseException;

class SincronizaContratoHubspotInvalidoException extends BaseException
{
    public function __construct(array $errors = [])
    {
        parent::__construct("Não é permitido sincronizar este contrato com o Hubspot");
        $this->code = 422;
        $this->errors = $errors;
    }
}
```

## Uso de Exceções

### Lançamento de Exceções

```php
namespace App\Services\Assinaturas;

use App\Exceptions\Assinaturas\EscritorioJaPossuiProdutoException;

class Cria extends AssinaturaService
{
    public function criaAssinatura(CriaAssinaturaDto $dto_criacao): Assinatura
    {
        if($dto_criacao->escritorio->produtos->contains($dto_criacao->produto)) {
            throw new EscritorioJaPossuiProdutoException();
        }
        
        // Continuação da lógica...
    }
}
```

### Tratamento de Exceções

O Laravel automaticamente captura exceções que estendem `BaseException` e renderiza a resposta JSON através do método `render()`.

Para tratamento customizado em controllers:

```php
namespace App\Http\Controllers\Assinaturas;

use App\Exceptions\Assinaturas\EscritorioJaPossuiProdutoException;

class AssinaturaController extends Controller
{
    public function store(CriaAssinaturaRequest $request, Cria $service)
    {
        try {
            $assinatura = $service->criaAssinatura($request->loadDto());
            return new AssinaturaShowResource($assinatura);
        } catch (EscritorioJaPossuiProdutoException $e) {
            return response()->json([
                'error' => true,
                'message' => $e->getMessage(),
                'errors' => $e->getErros()
            ], 422);
        }
    }
}
```

### Em Testes

```php
use App\Exceptions\Assinaturas\EscritorioJaPossuiProdutoException;
use Illuminate\Support\Facades\Exceptions;

it('falha caso escritorio já tenha o produto', function() {
    Exceptions::fake();
    
    $response = $this->actor->actingAs($this->admin)
        ->post('/api/assinaturas', $data);
    
    $response->assertUnprocessable();
    Exceptions::assertReported(EscritorioJaPossuiProdutoException::class);
});
```

## Códigos de Status HTTP

As exceções devem usar códigos de status HTTP apropriados:

- **400 Bad Request**: Requisição malformada
- **401 Unauthorized**: Falha na autenticação
- **403 Forbidden**: Acesso negado (autorização)
- **404 Not Found**: Recurso não encontrado
- **422 Unprocessable Entity**: Erros de validação ou regras de negócio
- **500 Internal Server Error**: Erros inesperados do servidor

## Boas Práticas

- **Nomes descritivos**: Nomes de exceções devem descrever claramente o erro
- **Mensagens claras**: Mensagens devem ser compreensíveis para desenvolvedores e usuários
- **Organização por domínio**: Agrupar exceções por domínio funcional
- **Códigos de status apropriados**: Usar códigos HTTP corretos para cada tipo de erro
- **Erros detalhados**: Fornecer array de erros quando houver múltiplos problemas
- **Não expor detalhes sensíveis**: Evitar expor informações de segurança ou estrutura interna
- **Documentação**: Documentar exceções customizadas com PHPDoc

## Exceções Comuns do Fênix

### Autenticação
- `AuthFailedException`: Falha na autenticação (401)

### Banco de Dados
- `NoDatabaseSelectedException`: Header `database` não fornecido
- `InvalidDatabaseSelectedException`: Banco de dados inválido no header

### Assinaturas
- `EscritorioJaPossuiProdutoException`: Escritório já possui produto (422)
- `ObjetoMalFormadoException`: Dados inválidos para criação (422)

### Financeiro
- `SincronizaContratoHubspotInvalidoException`: Contrato não pode ser sincronizado (422)
- `CancelarNFAsaasException`: Erro ao cancelar nota fiscal (422)

### Serviços Externos
- `GeminiClientException`: Erro na comunicação com Gemini AI
- `RegistrarPublicacaoException`: Erro ao registrar publicação

## Formato de Resposta

Todas as exceções retornam o seguinte formato JSON:

```json
{
    "datetime": "2024-01-15 10:30:00",
    "error": true,
    "code": 422,
    "message": "Mensagem de erro descritiva",
    "errors": {
        "campo": ["Erro específico do campo"]
    }
}
```
