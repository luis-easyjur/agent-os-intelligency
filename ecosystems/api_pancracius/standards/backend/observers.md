# Observers e efeitos colaterais

## Observer abstrato

`App\Observers\Observer` é abstrato. Implementa `created`, `updated`, `deleted` que: chamam `$this->dtoService->constroiDto($model, $mensagem)` e em seguida `$this->logService->criaLog($model, $dto)`. Não declara propriedades do dtoService no base; cada observer concreto injeta.

## Observer concreto

Cada observer concreto (ex.: `AssinaturaObserver`, `PacoteObserver`) define `protected string $entidade` e no construtor injeta `Cria $logService` (Logs) e um serviço de log do domínio (ex.: `Assinatura\Log`) como `dtoService`. O dtoService deve implementar `constroiDto(Model $model, string $mensagem)` retornando o DTO usado por `criaLog`.

## Uso

Observers são para efeitos colaterais de auditoria/log ao criar, atualizar ou excluir modelo. Lógica de negócio que altera outros modelos ou chama serviços deve ficar em Service ou em outro Observer específico, não no Observer de log.

## Traits

Alguns observers usam traits (ex.: `TrataBancoDeDados`, `TrataDatas`) para normalizar atributos do modelo antes de montar o DTO. O trait atua sobre `$this->alvo` (array de atributos).
