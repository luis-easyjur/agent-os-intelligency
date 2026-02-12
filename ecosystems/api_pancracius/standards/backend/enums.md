# Enums no Fênix

## Organização

Enums em `app/Enums/`. Um arquivo por enum. Nomenclatura PascalCase (BancoDeDados, Module, StatusAtividade, TipoGrupo).

## Quando usar

Valores fixos e fechados: status (StatusUsuario, StatusAtividade), tipos (TipoGrupo, TipoPacote, TipoUsuario), módulos e permissões (Module), bancos (BancoDeDados), categorias (CategoriaTipoAcaoProcesso). Evitar strings ou constantes soltas para esses casos.

## Uso no código

- **value**: valor persistido (ex.: `BancoDeDados::iturn->value` para string 'advogados_iturn').
- **name**: nome do case (ex.: `BancoDeDados::iturn->name` para 'iturn').
- **tryFrom**: obter enum a partir de valor ou null (ex.: `BancoDeDados::tryFrom($request->get('banco'))`).
- **from**: obter enum a partir de valor ou lançar exceção.
- **Casts no Model**: `protected $casts = ['status' => StatusAtividade::class];` para serialização automática.

## Configuração e multi-tenancy

BancoDeDados é usado no header `database` (valores name: iturn, oabes, oabmg) e em validação de request (value pode ser usado em regras). Module é usado para permissões (canRead, canWrite). Manter enums alinhados com o que o front e as rotas esperam.
