# Repositories e acesso a dados

## Interface Repository

`App\Repositories\Interfaces\Repository` define: `findById(int $id)`, `findFirst(array $filters)`, `store(array $data)`, `delete(Model $model)`, `safeDelete(RecursoRestrito $model)`. Repositories concretos implementam essa interface ou estendem base que a implementa.

## ModelRepository base

`App\Repositories\Common\ModelRepository` recebe no construtor o nome da classe do Model (string). Expõe `findById`, `findFirst`, `findLast`, `findOrCreate`, `store`, `update`, `delete`, `safeDelete`. Métodos protegidos: `mudaBanco(BancoDeDados $banco)` para trocar conexão temporariamente, `loadAutocompleteQuery(string $term, array $fields)` para adicionar condições LIKE ao builder. Repositories concretos estendem ModelRepository passando a classe do model ou estendem e definem `$this->model` no construtor.

## Paginação

- **PaginationRepositoryInterface**: `loadPaginationQuery(PaginationDtoInterface $paginationDTO): Builder`. Repositories que paginam implementam essa interface.
- **TemPaginacaoSimples**: trait com `loadPaginationQuery(PaginationDtoInterface $paginationDTO)` usando `$this->model->where($paginationDTO->filters)`.
- **PaginacaoUsaGrupos**: trait com `adicionaQueryParaGrupos(PaginacaoComGruposDtoInterface $dto)` para filtrar por grupos/subgrupos/tags quando o DTO indicar.

## safeDelete

`safeDelete(RecursoRestrito $model)` deve validar que o recurso pertence ao escritório do usuário (ou regra equivalente) antes de excluir. Modelos que podem ser excluídos com essa validação implementam `RecursoRestrito`.

## Organização

Um repository por agregado ou entidade principal (AssinaturaRepository, PacoteRepository, etc.), em `app/Repositories/` por domínio. Repository é injetado no Service; Controller não acessa repository diretamente.
