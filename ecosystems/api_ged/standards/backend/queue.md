# Padrões de Jobs e Filas no APP-GED

## Visão Geral

O APP-GED utiliza o sistema de filas do Laravel para processamento assíncrono de tarefas demoradas ou pesadas. O sistema permite que operações complexas sejam executadas em segundo plano, melhorando a experiência do usuário e a escalabilidade da aplicação.

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
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class ExampleJob implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    protected $data;

    public function __construct($data)
    {
        $this->data = $data;
    }

    public function handle()
    {
        // Lógica de processamento do job
    }
}
```

## Casos de Uso Principais

O APP-GED utiliza jobs para diversos casos de uso:

1. **Processamento em Massa**
   - `MassBilling`: Processamento de faturamento em massa
   - `CancelRevenue`: Cancelamento de receitas
   - `MigrationFromZip`: Migração de arquivos a partir de um ZIP

2. **Operações de Arquivos**
   - `DeleteOfficeFiles`: Exclusão de arquivos de um escritório

3. **Integrações**
   - `PushLawsuitJob`: Envio de processos para outros sistemas
   - `SendLoginLegacy`: Envio de informações de login para sistemas legados

4. **Relatórios**
   - `ReportJob`: Geração de relatórios
   - `ReportManagement`: Gerenciamento de relatórios

5. **Manutenção do Sistema**
   - `DisableOffice`: Desativação de escritórios
   - `FetchOverdueJobs`: Busca de jobs atrasados

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
ExampleJob::dispatch($data);

// Com delay
ExampleJob::dispatch($data)->delay(now()->addMinutes(5));

// Em fila específica
ExampleJob::dispatch($data)->onQueue('high');

// Com tentativas e timeout
ExampleJob::dispatch($data)
    ->onQueue('default')
    ->tries(3)
    ->timeout(300);
```

### Execução e Monitoramento

- Utilizar o comando `php artisan queue:work` para processar jobs
- Em ambiente de produção, usar supervisor ou similar para garantir que o worker esteja sempre rodando
- Monitorar falhas através da tabela `failed_jobs`
- Utilizar o comando `php artisan queue:failed` para listar jobs falhos
- Implementar notificações para jobs críticos que falham

## Exemplos Reais do APP-GED

### MassBilling Job

```php
namespace App\Jobs;

use App\Models\MassBilling as ModelsMassBilling;
use App\Services\MassBillingService;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class MassBilling implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    private $billing;
    
    public function __construct(ModelsMassBilling $billing)
    {
        $this->billing = $billing;
    }

    public function handle(MassBillingService $service)
    {
        $service->massBillingManager($this->billing);
    }
}
```

### Controller que Despacha um Job

```php
namespace App\Http\Controllers;

use App\Http\Requests\MassBillingRequest;
use App\Jobs\MassBilling;
use App\Models\MassBilling as ModelsMassBilling;
use Illuminate\Http\Response;

class MassBillingController extends Controller
{
    public function store(MassBillingRequest $request)
    {
        $massBilling = ModelsMassBilling::create($request->validated());
        
        MassBilling::dispatch($massBilling);
        
        return response()->json($massBilling, Response::HTTP_CREATED);
    }
}
```