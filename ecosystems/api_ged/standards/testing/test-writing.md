# Padrões de Escrita de Testes para APP-GED

## Contexto do Projeto

O APP-GED utiliza **PHPUnit** para testes unitários e de integração e **Laravel Testing** para testes de API. Como se trata de um microsserviço sem frontend, os testes são focados na validação das APIs e da lógica de negócio.

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
namespace Tests\Feature;

use Tests\TestCase;
use App\Models\File;
use App\Models\User;
use Laravel\Sanctum\Sanctum;
use Illuminate\Foundation\Testing\RefreshDatabase;

class FileApiTest extends TestCase
{
    use RefreshDatabase;
    
    /** @test */
    public function user_can_upload_a_file()
    {
        // Arrange
        Sanctum::actingAs(User::factory()->create());
        $file = UploadedFile::fake()->create('document.pdf', 100);
        
        // Act
        $response = $this->postJson('/api/files', [
            'file' => $file,
            'description' => 'Test document'
        ]);
        
        // Assert
        $response->assertStatus(201)
                 ->assertJsonStructure(['id', 'name', 'path', 'size']);
                 
        $this->assertDatabaseHas('files', [
            'name' => 'document.pdf',
            'status' => 'active'
        ]);
    }
}
```

### Testes Unitários

```php
namespace Tests\Unit;

use Tests\TestCase;
use App\Services\FileService;
use App\Repositories\FileRepository;
use Mockery;
use Illuminate\Foundation\Testing\RefreshDatabase;

class FileServiceTest extends TestCase
{
    /** @test */
    public function it_can_process_a_file()
    {
        // Arrange
        $repository = Mockery::mock(FileRepository::class);
        $repository->shouldReceive('save')->once()->andReturn(true);
        
        $service = new FileService($repository);
        $fileData = ['name' => 'test.pdf', 'size' => 1000];
        
        // Act
        $result = $service->processFile($fileData);
        
        // Assert
        $this->assertTrue($result);
    }
}
```
