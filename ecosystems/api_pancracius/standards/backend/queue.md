# Padrões de Jobs e Filas no Fênix

## Visão Geral

O Fênix utiliza o sistema de filas do Laravel para processamento assíncrono de tarefas demoradas ou pesadas. O sistema permite que operações complexas sejam executadas em segundo plano, melhorando a experiência do usuário e a escalabilidade da aplicação.

## Configuração de Filas

O projeto utiliza o arquivo `config/queue.php` para configuração das filas. A conexão padrão é definida pela variável de ambiente `QUEUE_CONNECTION`.

Opções de drivers disponíveis:
- `sync`: Executa jobs de forma síncrona (útil para desenvolvimento e testes)
- `database`: Armazena jobs em uma tabela do banco de dados
- `redis`: Armazena jobs no Redis (recomendado para produção)
- `sqs`: Utiliza Amazon SQS para processamento
- `beanstalkd`: Utiliza Beanstalkd como fila

## Estrutura de Jobs

Os jobs são armazenados no diretório `app/Jobs/` e seguem a estrutura padrão do Laravel:

```php
namespace App\Jobs\Financeiro\Contratos;

use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Queue\Queueable;

class SincronizaHubspot implements ShouldQueue
{
    use Queueable;

    public $tries = 3;
    public $backoff = 10;
    
    public function __construct(public int $id_contrato)
    {
        $this->onQueue('sincroniza_contratos_hubspot');
    }

    public function handle(SincronizaHubspotService $service): void
    {
        // Lógica de processamento do job
    }
}
```

## Casos de Uso Principais

O Fênix utiliza jobs para diversos casos de uso:

1. **Integrações Financeiras**
   - `SincronizaHubspot`: Sincronização de contratos com Hubspot
   - `DeletaHubspot`: Exclusão de contratos no Hubspot

2. **Rotinas de Processamento**
   - Jobs para processamento de publicações e intimações
   - Jobs para análise de IA de documentos
   - Jobs para processamento de publicações e vinculação

3. **Integrações Externas**
   - Processamento de webhooks (Hubspot, Asaas)
   - Sincronização de dados com sistemas externos

4. **Comandos e Rotinas**
   - Atualização de permissões
   - Atualização de tabelas auxiliares

## Boas Práticas

### Criação de Jobs

- **Idempotência**: Jobs devem ser idempotentes - executar o mesmo job múltiplas vezes deve produzir o mesmo resultado
- **Atomicidade**: Cada job deve realizar uma operação atômica e bem definida
- **Injeção de Dependências**: Utilizar injeção de dependências no método `handle()` para serviços e repositórios
- **Dados Serializáveis**: Garantir que os dados armazenados no job possam ser serializados
- **Tratamento de Erros**: Implementar tratamento adequado de erros para evitar falhas silenciosas

### Despacho de Jobs

```php
// Despacho básico
SincronizaHubspot::dispatch($contratoId);

// Com delay
SincronizaHubspot::dispatch($contratoId)->delay(now()->addMinutes(5));

// Em fila específica (definida no construtor do job)
SincronizaHubspot::dispatch($contratoId);

// Com tentativas e timeout (definidos no job)
// $tries e $backoff são propriedades do job
```

### Execução e Monitoramento

- Utilizar o comando `php artisan queue:work` para processar jobs
- Em ambiente de produção, usar supervisor ou similar para garantir que o worker esteja sempre rodando
- Monitorar falhas através da tabela `failed_jobs`
- Utilizar o comando `php artisan queue:failed` para listar jobs falhos
- Implementar notificações para jobs críticos que falham

## Exemplos Reais do Fênix

### SincronizaHubspot Job

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
    use Queueable;

    public $tries = 3;
    public $backoff = 10;
    private Contrato $contrato;
    
    public function __construct(public int $id_contrato)
    {
        $this->onQueue('sincroniza_contratos_hubspot');
    }

    public function handle(SincronizaHubspotService $service): void
    {
        $this->contrato = Contrato::findOrFail($this->id_contrato);
        $service->gerenciarSincronizacao($this->contrato);
        $this->logSucesso();
    }

    public function failed(Throwable $e): void
    {
        // Lógica de tratamento de falha
        $this->logErro($e);
    }
}
```

### Controller que Despacha um Job

```php
namespace App\Http\Controllers\Financeiro\Contratos;

use App\Jobs\Financeiro\Contratos\SincronizaHubspot;
use App\Models\Financeiro\Contratos\Contrato;
use App\Http\Controllers\Controller;
use Illuminate\Http\JsonResponse;

class SincronizarHubspotController extends Controller
{
    public function sincronizar(Contrato $contrato): JsonResponse
    {
        $usuario = User::getCurrent();
        
        // Validações de permissão
        if ($usuario->id_empresa != 1) {
            throw new SincronizaContratoHubspotInvalidoException([
                "O usuário [ID: {$usuario->id}] não pertence ao escritório Easyjur e não é permitido sincronizar contratos com o Hubspot."
            ]);
        }
        
        if ($contrato->id_empresa != 1) {
            throw new SincronizaContratoHubspotInvalidoException([
                "O contrato [ID: {$contrato->id}] não pertence ao escritório Easyjur e não é permitido sincronizar o contrato com o Hubspot."
            ]);
        }
        
        SincronizaHubspot::dispatch($contrato->id);
        
        return $this->apiResponse('Solicitação de sincronização realizada com sucesso.', 200);
    }
}
```