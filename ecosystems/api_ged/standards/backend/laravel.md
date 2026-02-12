# Padrões Laravel no APP-GED

## Visão Geral

O APP-GED é um microsserviço construído com Laravel 8.x, seguindo as melhores práticas e convenções do framework. Este documento descreve os padrões específicos do Laravel que devem ser seguidos no desenvolvimento e manutenção do projeto.

## Estrutura de Diretórios

O projeto segue a estrutura padrão de diretórios do Laravel, com algumas personalizações:

- `app/` - Contém o código principal da aplicação
  - `Http/Controllers/` - Controladores da API
  - `Http/Middleware/` - Middlewares da aplicação
  - `Http/Requests/` - Form Requests para validação
  - `Models/` - Modelos Eloquent
  - `Repositories/` - Implementações de repositórios
  - `Services/` - Serviços de negócio
  - `Traits/` - Traits compartilhadas
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
Route::apiResource('files', 'FileController');

// Agrupamento por middleware
Route::middleware(['auth:sanctum'])->group(function () {
    Route::get('audits', [AuditController::class, 'index']);
    // ...
});

// Agrupamento por prefixo
Route::prefix('static')->group(function () {
    Route::post('', [StaticFilesController::class, 'store']);
    Route::delete('', [StaticFilesController::class, 'destroy']);
    // ...
});
```

## Controladores

### Padrões de Controladores

- Controladores são nomeados no singular com sufixo `Controller` (ex: `FileController`)
- Seguir o padrão RESTful (index, show, store, update, destroy) quando apropriado
- Manter controladores enxutos, delegando lógica de negócio para serviços

```php
namespace App\Http\Controllers;

use App\Http\Requests\CreateFileRequest;
use App\Models\File;
use App\Services\FileService;

class FileController extends Controller
{
    protected $fileService;

    public function __construct(FileService $fileService)
    {
        $this->fileService = $fileService;
    }

    public function index()
    {
        return $this->fileService->getAllFiles();
    }

    public function store(CreateFileRequest $request)
    {
        return $this->fileService->createFile($request->validated());
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
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class CreateFileRequest extends FormRequest
{
    public function authorize()
    {
        return true; // Ou verificação específica de permissão
    }

    public function rules()
    {
        return [
            'company_id' => 'required|integer|exists:companies,id',
            'file' => 'required|file|max:10240',
            'model_type' => 'sometimes|string',
            'model_id' => 'sometimes|integer',
        ];
    }
}
```

## Autenticação e Autorização

- Usar Laravel Sanctum para autenticação de API
- Implementar middleware customizado para validação de token quando necessário
- Utilizar o pacote Spatie Permission para gerenciamento de papéis e permissões

```php
// Exemplo de uso de middleware de autenticação
Route::middleware(['auth:sanctum'])->group(function () {
    // Rotas protegidas
});

// Exemplo de verificação de permissão
if ($user->hasPermissionTo('delete-files')) {
    // Ação permitida
}
```

## Serviços

- Implementar lógica de negócio em classes de serviço
- Injetar dependências via construtor
- Manter métodos públicos claros e com propósito único

```php
namespace App\Services;

use App\Models\File;
use App\Repositories\FileRepository;
use Illuminate\Support\Facades\Storage;

class FileService
{
    protected $fileRepository;

    public function __construct(FileRepository $fileRepository)
    {
        $this->fileRepository = $fileRepository;
    }

    public function uploadFile($data, $file)
    {
        $path = Storage::putFile('files', $file);
        $data['path'] = $path;
        $data['size'] = $file->getSize();
        $data['mime_type'] = $file->getMimeType();
        
        return $this->fileRepository->create($data);
    }
}
```

## Jobs e Filas

- Usar filas para processamento assíncrono de tarefas pesadas
- Implementar jobs que são auto-contidos e idempotentes
- Configurar tentativas e manipulação de falhas

```php
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class ProcessFile implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    protected $file;
    protected $options;

    public function __construct($file, $options = [])
    {
        $this->file = $file;
        $this->options = $options;
    }

    public function handle()
    {
        // Lógica de processamento
    }
}
```

## Eventos e Listeners

- Usar eventos para acoplamento flexível entre componentes
- Implementar listeners para reagir a eventos específicos
- Registrar eventos e listeners no `EventServiceProvider`

```php
// Evento
namespace App\Events;

use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class FileUploaded
{
    use Dispatchable, SerializesModels;

    public $file;

    public function __construct($file)
    {
        $this->file = $file;
    }
}

// Listener
namespace App\Listeners;

use App\Events\FileUploaded;

class ProcessUploadedFile
{
    public function handle(FileUploaded $event)
    {
        // Processar o arquivo
    }
}
```

## Testes

- Implementar testes de feature para endpoints da API
- Implementar testes unitários para lógica de negócio complexa
- Usar factories para criação de dados de teste

```php
namespace Tests\Feature;

use App\Models\File;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Laravel\Sanctum\Sanctum;
use Tests\TestCase;

class FileApiTest extends TestCase
{
    use RefreshDatabase;

    /** @test */
    public function user_can_upload_a_file()
    {
        Sanctum::actingAs(User::factory()->create());
        $file = UploadedFile::fake()->create('document.pdf', 100);

        $response = $this->postJson('/api/files', [
            'file' => $file,
            'company_id' => 1
        ]);

        $response->assertStatus(201);
        $this->assertDatabaseHas('files', [
            'filename' => 'document.pdf'
        ]);
    }
}
```

## Auditoria

- Usar o pacote `owen-it/laravel-auditing` para auditoria de modelos
- Implementar a interface `Auditable` e usar o trait `Auditable` nos modelos que precisam ser auditados

```php
use OwenIt\Auditing\Contracts\Auditable as InterfaceAuditable;
use OwenIt\Auditing\Auditable;

class File extends Model implements InterfaceAuditable
{
    use Auditable;
    // ...
}
```