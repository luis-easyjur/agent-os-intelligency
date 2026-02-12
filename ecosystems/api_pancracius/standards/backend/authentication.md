# Padrões de Autenticação e Autorização no Fênix

## Visão Geral

O Fênix utiliza **Laravel Sanctum** para autenticação de API, com suporte a login tradicional (email/senha) e login via Google OAuth. O sistema possui um mecanismo customizado de permissões baseado em módulos do sistema.

## Autenticação

### Login Tradicional

O sistema suporta login tradicional usando email e senha. O processo de autenticação:

1. Validação das credenciais através de `LoginRequest`
2. Verificação de senha (suporta MD5 legado e bcrypt)
3. Geração de token Sanctum
4. Retorno do token e dados do usuário

```php
namespace App\Services\Auth;

use App\DTO\Auth\Requests\LoginDTO;
use App\Exceptions\Auth\AuthFailedException;
use App\Models\Usuarios\User;

class Login extends AuthService
{
    public function login(LoginDTO $loginDTO)
    {
        $user = $this->userRepository->findUserByEmail($loginDTO->email);
        $this->validaLogin($loginDTO, $user);
        return $this->criaTokenParaUsuario($user);
    }
    
    private function validaLogin(LoginDTO $loginDTO, User $user): void
    {
        $senha_atualizada = $user->senha_atualizada;
        $password = $user->password;

        if($senha_atualizada == 'Sim') {
            $valid = password_verify($loginDTO->password, $password);
        } else {
            $hashed = md5($loginDTO->password);
            $valid = $password == $hashed;
        }

        if(!$valid)
            throw new AuthFailedException("Usuário ou senha incorreto");
    }
}
```

### Login via Google

O sistema suporta autenticação via Google OAuth usando credenciais JWT:

```php
namespace App\Services\Auth;

use App\Services\ApiClients\GoogleClient;

class LoginViaGoogle extends AuthService
{
    public function loginViaGoogle(string $credencial)
    {
        $client = new GoogleClient();
        $email = $client->buscaEmailParaCredencial($credencial);
        $user = $this->userRepository->findUserByEmail($email);
        return $this->criaTokenParaUsuario($user);
    }
}
```

### Endpoints de Autenticação

- **POST /api/auth/login** - Login tradicional (email/senha)
- **POST /api/auth/login/google** - Login via Google OAuth
- **GET /api/auth/me** - Obter dados do usuário atual (requer autenticação)

### Middleware de Autenticação

As rotas protegidas devem usar o middleware `auth:sanctum`:

```php
Route::middleware(['auth:sanctum'])->group(function () {
    // Rotas protegidas
});
```

## Obtenção do Usuário Atual

O modelo `User` possui um método estático para obter o usuário autenticado:

```php
use App\Models\Usuarios\User;

$user = User::getCurrent();
```

## Autorização e Permissões

### Sistema de Módulos

O Fênix utiliza um sistema de permissões baseado em **módulos** definidos no enum `Module`. Cada módulo representa uma área funcional do sistema (Processos, Assinaturas, Financeiro, etc.).

### Estrutura de Permissões

As permissões são armazenadas em dois campos do modelo `User`:
- `modulos`: String contendo módulos com permissão de escrita (formato: `d{id}d|`)
- `modulos_ver`: String contendo módulos com permissão apenas de leitura (formato: `d{id}d|`)

### Trait TemPermissoes

O modelo `User` utiliza o trait `TemPermissoes` que fornece métodos para gerenciar permissões:

```php
namespace App\Models\Traits\User;

use App\Enums\Module;

trait TemPermissoes
{
    // Adicionar permissão a um usuário
    public function addPermission(Module $modulo, bool $apenas_ver = false): void
    {
        if($apenas_ver)
            $this->modulos_ver .= "d".$modulo->value."d|";

        $this->modulos .= "d".$modulo->value."d|";
        $this->save();
    }

    // Verificar se usuário pode ler um módulo
    public function canRead(Module $module): bool
    {
        return $this->read_permissions->contains($module);
    }

    // Verificar se usuário pode escrever em um módulo
    public function canWrite(Module $module): bool
    {
        return $this->write_permissions->contains($module);
    }
}
```

### Verificação de Permissões

```php
use App\Enums\Module;
use App\Models\Usuarios\User;

$user = User::getCurrent();

// Verificar permissão de leitura
if ($user->canRead(Module::PROCESSOS)) {
    // Usuário pode visualizar processos
}

// Verificar permissão de escrita
if ($user->canWrite(Module::PROCESSOS)) {
    // Usuário pode criar/editar processos
}
```

### Módulos Principais

Os principais módulos do sistema incluem:
- `Module::PROCESSOS` - Gestão de processos jurídicos
- `Module::ASSINATURAS` - Gestão de assinaturas
- `Module::FINANCEIRO` - Módulo financeiro
- `Module::ADMIN` - Acesso administrativo
- E muitos outros (ver `app/Enums/Module.php`)

## Boas Práticas

- **Sempre verificar autenticação**: Usar middleware `auth:sanctum` em rotas que requerem autenticação
- **Verificar permissões antes de ações**: Sempre verificar `canRead()` ou `canWrite()` antes de operações sensíveis
- **Usar User::getCurrent()**: Sempre usar o método estático para obter o usuário atual
- **Tratar exceções de autenticação**: Capturar `AuthFailedException` e outras exceções relacionadas
- **Não expor detalhes de segurança**: Mensagens de erro não devem revelar se email existe ou não

## Segurança

- **Tokens Sanctum**: Tokens são armazenados em `personal_access_tokens` e têm expiração configurável
- **Validação de senha**: Sistema suporta migração gradual de MD5 para bcrypt
- **Headers obrigatórios**: Middleware `DatabaseMiddleware` valida header `database` para multi-tenancy
- **Isolamento por escritório**: Dados são isolados por `id_empresa` do usuário autenticado
