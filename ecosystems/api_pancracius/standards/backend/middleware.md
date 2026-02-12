# Padrões de Middleware no Fênix

## Visão Geral

O Fênix utiliza middleware customizado para gerenciar aspectos críticos do sistema, especialmente o isolamento multi-tenant através de múltiplos bancos de dados.

## Middleware Principal

### DatabaseMiddleware

O `DatabaseMiddleware` é **crítico** para o funcionamento do sistema multi-tenant. Ele valida e configura a conexão de banco de dados baseado no header HTTP `database`.

```php
namespace App\Http\Middleware;

use App\Exceptions\Database\InvalidDatabaseSelectedException;
use App\Exceptions\Database\NoDatabaseSelectedException;
use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;

class DatabaseMiddleware
{
    public function handle(Request $request, Closure $next): Response
    {
        $database = $request->header('database');

        if(!$database) {
            throw new NoDatabaseSelectedException();
        }
        
        if(!in_array($database, ['iturn', 'oabes', 'oabmg'])) {
            throw new InvalidDatabaseSelectedException();
        }

        DB::setDefaultConnection($database);
        return $next($request);
    }
}
```

**Funcionalidades:**
- Valida presença do header `database`
- Valida se o banco especificado é válido (`iturn`, `oabes`, `oabmg`)
- Configura a conexão padrão do Laravel para o banco especificado
- Lança exceções específicas se validação falhar

**Uso:**
Todas as requisições devem incluir o header:
```
database: iturn
```

## Middleware do Laravel

### Autenticação (Sanctum)

O middleware `auth:sanctum` é usado para proteger rotas que requerem autenticação:

```php
Route::middleware(['auth:sanctum'])->group(function () {
    Route::get('/api/auth/me', [AuthController::class, 'me']);
    // Outras rotas protegidas
});
```

### Validação de Banco de Dados

O `DatabaseMiddleware` deve ser aplicado globalmente ou em grupos de rotas:

```php
// Em bootstrap/app.php ou routes
Route::middleware(['database'])->group(function () {
    // Rotas que requerem seleção de banco
});
```

## Configuração de Middleware

O middleware é registrado em `bootstrap/app.php` ou `app/Http/Kernel.php`:

```php
// Exemplo de registro
$middleware->append(\App\Http\Middleware\DatabaseMiddleware::class);
```

## Boas Práticas

- **Sempre incluir header database**: Todas as requisições devem incluir o header `database`
- **Validação consistente**: Validar banco de dados em todas as rotas que acessam dados
- **Exceções apropriadas**: Usar exceções específicas para erros de middleware
- **Documentação**: Documentar middleware customizado com propósito e uso
- **Testes**: Testar middleware isoladamente e em integração

## Tratamento de Erros

O middleware lança exceções específicas:

- `NoDatabaseSelectedException`: Quando header `database` não é fornecido
- `InvalidDatabaseSelectedException`: Quando banco especificado não é válido

Essas exceções são automaticamente renderizadas como respostas JSON pelo `BaseException`.

## Exemplo de Uso em Testes

```php
describe("requisições com middleware", function() {
    
    beforeEach(function() {
        $this->actor = $this->withHeader('database', 'iturn')
            ->withHeader('accept', 'application/json');
    });

    it('falha sem header database', function() {
        $response = $this->post('/api/assinaturas', []);
        $response->assertStatus(422);
    });
    
    it('falha com banco inválido', function() {
        $response = $this->withHeader('database', 'banco_invalido')
            ->post('/api/assinaturas', []);
        $response->assertStatus(422);
    });
});
```

## Integração com Outros Middleware

O `DatabaseMiddleware` deve ser executado **antes** de qualquer middleware que acesse o banco de dados, garantindo que a conexão correta esteja configurada.

Ordem recomendada:
1. `DatabaseMiddleware` - Configura conexão
2. `auth:sanctum` - Autentica usuário
3. Outros middlewares específicos
