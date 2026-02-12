# Interfaces de modelos

## Autocompletable

Modelos que entram em autocomplete implementam `App\ModelInterfaces\Autocompletable`: método `autocompleteLabel(): Attribute`. O Attribute deve retornar o valor exibido no autocomplete (acessor no model). O `AutocompleteResource` espera `id` e `autocomplete_label` no resource; o model pode expor `autocomplete_label` como acessor apontando para o Attribute. Services de autocomplete implementam `AutocompletableService` e são usados pelo trait `HasAutocomplete` no controller.

## RecursoRestrito

Modelos que pertencem a um escritório e devem ser filtrados ou validados por escritório implementam `App\ModelInterfaces\RecursoRestrito`: método `escritorio(): BelongsTo` e scope `scopeDaEmpresa(Builder $query)`. Usado por: Form Requests (traits que verificam recurso do escritório), `Repository::safeDelete(RecursoRestrito $model)`, e Services que precisam garantir que o recurso é do escritório do usuário. Modelos como User, Grupo, Documento, Feedback, ModeloDeDocumento implementam essa interface.

## Quando usar

- **Autocompletable**: qualquer entidade exposta em endpoint de autocomplete (usuários, grupos, etc.).
- **RecursoRestrito**: qualquer entidade que tenha `id_empresa` (ou equivalente) e cujo acesso deva ser restrito ao escritório do usuário logado.
