# Padrões de Escrita de Testes para Fênix

## Contexto do Projeto

O Fênix utiliza **Pest PHP** para testes unitários e de integração e **Laravel Testing** via Pest para testes de API. Como se trata de um sistema backend sem frontend, os testes são focados na validação das APIs e da lógica de negócio.

## Estrutura e bindings (Pest.php)

- **Feature** e **ModelBehavior**: usam `Tests\TestCase` e `DatabaseTransactions`; em `beforeEach` ficam `$this->actor` (request com headers `database: iturn` e `accept: application/json`) e `$this->admin` (usuário admin criado). Testes de API e de comportamento de modelo rodam em transação (rollback ao final).
- **Unit**: usam apenas `Tests\TestCase`; não usam `DatabaseTransactions`. Usar para lógica pura, mocks e serviços isolados. Não dispõem de `$this->actor` nem `$this->admin` por padrão; criar quando necessário.

Organização: pastas em `tests/` espelham domínios (Assinaturas, Users, Documentos, etc.). Arquivos em `test_*.php` ou `*Test.php`; dentro deles `describe()` e `it()`.

## Boas Práticas

- **Cobertura de API Completa**: Escrever testes para todos os endpoints da API, validando respostas, códigos de status HTTP e formatos de dados
- **Testes Unitários para Lógica de Negócio**: Implementar testes unitários para serviços e outras classes com lógica de negócio complexa
- **Testes de Integração**: Criar testes de integração que validem a interação entre componentes, incluindo banco de dados e serviços externos
- **Mocks e Stubs para Dependências Externas**: Utilizar mocks para simular serviços externos como armazenamento em nuvem, evitando chamadas reais durante testes
- **Database Seeding**: Preparar dados de teste consistentes usando seeders e factories do Laravel
- **Testar Cenários de Sucesso e Falha**: Incluir testes para validar comportamento em cenários de sucesso e falha (erros de validação, exceções, etc.)
- **Testes de Queue e Jobs**: Testar processamento assíncrono de filas e jobs
- **Testar Comportamento, Não Implementação**: Focar testes no que o código faz, não em como faz, para reduzir fragilidade
- **Nomes de Teste Claros**: Usar nomes descritivos que expliquem o que está sendo testado e o resultado esperado

## Exemplos de Estrutura de Testes

### Testes de Feature (API)

```php
use App\Models\Assinaturas\Assinatura;
use App\Models\Assinaturas\Produtos\Produto;
use App\Models\Escritorio;

describe('cria assinaturas', function() {
    
    beforeEach(function() {
        $this->admin->id_empresa = 1;
        $this->admin->addPermission(Module::ADMIN);
        $this->admin->save();
    });

    it('funciona sem data de expiração', function() {
        $produto = Produto::factory()->create();
        $escritorio = Escritorio::factory()->create();

        $data = [
            'banco' => $escritorio->getConnection()->getDatabaseName(),
            'id_produto_ia' => $produto->id,
            'id_empresa' => $escritorio->id
        ];

        $response = $this->actor->actingAs($this->admin)
            ->post('/api/assinaturas', $data);

        $response->assertCreated();

        $id = $response->json()['id'];
        $data['id'] = $id;
        $this->assertDatabaseHas(Assinatura::class, $data);

        $assinatura = Assinatura::find($id);
        $this->assertModelExists($assinatura);
    });
});
```

**Nota**: Os testes usam `$this->actor` (configurado no `Pest.php` com headers `database: iturn` e `accept: application/json`) e `$this->admin` (usuário admin criado automaticamente). Feature tests usam `DatabaseTransactions` automaticamente.

### Testes Unitários

```php
use App\Services\Assinaturas\Cria;
use App\Repositories\Assinaturas\AssinaturaRepository;
use App\DTO\Assinatura\ParametrosService\CriaAssinaturaDto;
use App\Models\Assinaturas\Assinatura;
use App\Models\Assinaturas\Produtos\Produto;
use App\Models\Escritorio;
use App\Enums\BancoDeDados;

test('it can create an assinatura', function () {
    // Arrange
    $produto = Produto::factory()->create();
    $escritorio = Escritorio::factory()->create();
    $banco = BancoDeDados::iturn;
    
    $dto = new CriaAssinaturaDto(
        escritorio: $escritorio,
        produto: $produto,
        banco: $banco
    );
    
    $service = new Cria(new AssinaturaRepository());
    
    // Act
    $result = $service->criaAssinatura($dto);
    
    // Assert
    expect($result)->toBeInstanceOf(Assinatura::class)
        ->and($result->id_produto_ia)->toBe($produto->id)
        ->and($result->id_empresa)->toBe($escritorio->id);
});
```
