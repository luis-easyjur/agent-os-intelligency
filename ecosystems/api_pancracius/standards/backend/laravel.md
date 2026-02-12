# Padrões Laravel no Fênix

## Visão Geral

O Fênix é um sistema jurídico construído com Laravel 11.x, seguindo as melhores práticas e convenções do framework. Este documento descreve os padrões específicos do Laravel que devem ser seguidos no desenvolvimento e manutenção do projeto.

## Estrutura de Diretórios

O projeto segue a estrutura padrão de diretórios do Laravel, com algumas personalizações:

- `app/` - Contém o código principal da aplicação
  - `Http/Controllers/` - Controladores da API organizados por domínio
  - `Http/Middleware/` - Middlewares da aplicação
  - `Http/Requests/` - Form Requests para validação
  - `Http/Resources/` - API Resources para transformação de dados
  - `Models/` - Modelos Eloquent organizados por domínio
  - `Repositories/` - Implementações de repositórios
  - `Services/` - Serviços de negócio organizados por domínio
  - `DTO/` - Data Transfer Objects
  - `Enums/` - Enumeradores
  - `Exceptions/` - Exceções customizadas
  - `Jobs/` - Jobs para processamento em background
- `config/` - Arquivos de configuração
- `database/` - Migrations, seeds e factories
- `routes/` - Definições de rotas
- `tests/` - Testes automatizados

## Routing

### Convenções de Rotas

- Todas as rotas são definidas em `routes/api.php`
- Rotas são agrupadas por funcionalidade ou por middleware
- Usar nomes de recursos no plural para endpoints RESTful

```php
// Exemplo de rotas RESTful
Route::apiResource('assinaturas', AssinaturaController::class);

// Agrupamento por middleware
Route::middleware(['auth:sanctum'])->group(function () {
    Route::prefix('processos')->group(function () {
        Route::get('areas', [AreasProcessoController::class, 'index']);
    });
    // ...
});

// Agrupamento por prefixo
Route::prefix('auth')->group(function () {
    Route::post('login', [AuthController::class, 'login']);
    Route::post('login/google', [AuthController::class, 'loginViaGoogle']);
    Route::get('me', [AuthController::class, 'me'])->middleware('auth:sanctum');
});
```

## Controladores

### Padrões de Controladores

- Controladores são nomeados no singular com sufixo `Controller` (ex: `AssinaturaController`, `AuthController`)
- Seguir o padrão RESTful (index, show, store, update, destroy) quando apropriado
- Manter controladores enxutos, delegando lógica de negócio para serviços

```php
namespace App\Http\Controllers;

use App\Http\Requests\Auth\LoginRequest;
use App\Services\Auth\Login;
use App\Http\Resources\Auth\LoginResource;

class AuthController extends Controller
{
    public function login(
        LoginRequest $request,
        Login $service
    ): JsonResponse
    {
        $loginDto = $request->loadDto();
        $token = $service->login($loginDto);
        return $this->apiResponse('Login realizado com sucesso.', 200, new LoginResource($token));
    }
}
```

## Validação

### Form Requests

- Usar Form Requests para validação complexa
- Cada operação que requer validação deve ter seu próprio Form Request
- Implementar método `rules()` para definir regras de validação
- Implementar `authorize()` quando necessário para verificação de permissões

```php
namespace App\Http\Requests\Auth;

use App\DTO\Auth\Requests\LoginDTO;
use Illuminate\Foundation\Http\FormRequest;

class LoginRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'email' => [
                'string',
                'required',
                'exists:tb_users_advogados'
            ],
            'password' => [
                'string',
                'required'
            ]
        ];
    }

    public function messages(): array
    {
        return [
            'email.required' => "O campo de email é obrigatório",
            'email.exists' => "Email não registrado",
            'password.required' => "O campo de senha é obrigatório"
        ];
    }

    public function loadDto(): LoginDTO
    {
        $data = $this->validated();
        return new LoginDTO(
            $data['email'],
            $data['password']
        );
    }
}
```

## Autenticação e Autorização

- Usar Laravel Sanctum para autenticação de API
- Suporte a login tradicional e login via Google
- Implementar middleware customizado para validação de token quando necessário
- Sistema de permissões customizado através de traits e interfaces

```php
// Exemplo de uso de middleware de autenticação
Route::middleware(['auth:sanctum'])->group(function () {
    // Rotas protegidas
});

// Exemplo de obtenção do usuário atual
$user = User::getCurrent();

// Exemplo de verificação de permissão
if ($user->temPermissao('delete-documentos')) {
    // Ação permitida
}
```

## Serviços

- Implementar lógica de negócio em classes de serviço
- Injetar dependências via construtor
- Manter métodos públicos claros e com propósito único

```php
namespace App\Services\Assinaturas;

use App\Models\Assinaturas\Assinatura;
use App\DTO\Assinatura\ParametrosService\CriaAssinaturaDto;
use App\DTO\Assinatura\DadosInsert\DadosCriaAssinatura;
use App\Exceptions\Assinaturas\EscritorioJaPossuiProdutoException;

class Cria extends AssinaturaService
{
    public function criaAssinatura(CriaAssinaturaDto $dto_criacao): Assinatura
    {
        if($dto_criacao->escritorio->produtos->contains($dto_criacao->produto)) {
            throw new EscritorioJaPossuiProdutoException();
        }

        $dados_insert = DadosCriaAssinatura::deCriaAssinaturaDto($dto_criacao);
        $assinatura = $this->repository->criaAssinatura($dados_insert);

        $this->criaPacoteInicialDeAssinatura($assinatura);

        $assinatura->load(['produto']);
        return $assinatura;
    }
}
```

## Jobs e Filas

- Usar filas para processamento assíncrono de tarefas pesadas
- Implementar jobs que são auto-contidos e idempotentes
- Configurar tentativas e manipulação de falhas

```php
namespace App\Jobs\Financeiro\Contratos;

use App\Models\Financeiro\Contratos\Contrato;
use App\Services\Financeiro\Contratos\SincronizaHubspot\SincronizaHubspotService;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class SincronizaHubspot implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    private Contrato $contrato;

    public function __construct(Contrato $contrato)
    {
        $this->contrato = $contrato;
    }

    public function handle(SincronizaHubspotService $service): void
    {
        $service->sincroniza($this->contrato);
    }
}
```

## Eventos e Listeners

- Usar eventos para acoplamento flexível entre componentes
- Implementar listeners para reagir a eventos específicos
- Registrar eventos e listeners no `EventServiceProvider`

```php
// Evento (exemplo genérico)
namespace App\Events;

use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class AssinaturaCriada
{
    use Dispatchable, SerializesModels;

    public $assinatura;

    public function __construct($assinatura)
    {
        $this->assinatura = $assinatura;
    }
}

// Listener
namespace App\Listeners;

use App\Events\AssinaturaCriada;

class ProcessaAssinaturaCriada
{
    public function handle(AssinaturaCriada $event)
    {
        // Processar a assinatura criada
    }
}
```

## Testes

- Implementar testes de feature para endpoints da API
- Implementar testes unitários para lógica de negócio complexa
- Usar factories para criação de dados de teste

```php
use App\Models\Usuarios\User;

describe("mecanismo de login", function () {
    
    beforeEach(function() {
        $this->actor = $this->withHeader('database', 'iturn')
            ->withHeader('accept', 'application/json');
    });

    it("allows for login", function(string $email, string $password) {
        $u = User::factory()->create([
            'email' => $email,
            'password' => md5($password)
        ]);

        $data = compact('password', 'email');
        $response = $this->actor->post("/api/auth/login", $data);
        $body = $response->getContent();
        $response->assertStatus(200);
        expect($body)->toBeJson()
            ->and($body)
            ->json()->token->toBeString();
    })
    ->with([
        ['usertest@mail.com', 'password'],
        ['ransoub@skdgb.con', 'aouiehbfasom']
    ]);
});
```

**Nota**: Os testes usam `$this->actor` que é configurado no `Pest.php` com headers padrão (`database: iturn`, `accept: application/json`). Feature tests usam `DatabaseTransactions` automaticamente. O projeto usa `md5()` para senhas, não `bcrypt()`.

## Logs e Auditoria

- Sistema de logs customizado através de Services de Log
- Logs organizados por domínio (Financeiro, Pessoas, etc.)
- Traits para formatação e preparação de dados de log
- Timeline de logs para visualização histórica

```php
namespace App\Services\Logs\Financeiro\Contratos;

use App\Repositories\Logs\LogRepository;
use App\Services\Logs\Traits\FormataDatas;
use App\Services\Logs\Traits\FormataValoresMoeda;
use App\Services\Logs\Traits\BuscaNomePessoa;
use App\Services\Logs\Traits\BuscaNomeUser;
use App\Services\Logs\Traits\RemoveCamposVaziosEDesnecessarios;

class ContratoLogService
{
    use FormataDatas, FormataValoresMoeda, BuscaNomePessoa, BuscaNomeUser, RemoveCamposVaziosEDesnecessarios;

    public function __construct(
        protected LogRepository $repository
    ) {}

    public function cria(array $dados)
    {
        // Lógica de criação de log usando traits para formatação
    }
}
```