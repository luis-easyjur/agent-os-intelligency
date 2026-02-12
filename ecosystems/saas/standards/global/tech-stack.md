# Stack Tecnológico

## Framework & Runtime
- **Linguagem:** PHP 7.4+
- **Arquitetura:** Monolítica (frontend e backend no mesmo código-base)

## Frontend
- **Framework CSS:** Bootstrap 5.3.7 (com patches customizados)
- **Biblioteca JavaScript:** jQuery 3.x
- **Bibliotecas Auxiliares:**
  - jQuery UI
  - CKEditor
  - Quill.js
  - Flatpickr
  - Tom Select
  - Toastr
  - Gridstack
  - FontAwesome

## Database & Storage
- **Banco de Dados:** MySQL/MariaDB
- **Driver:** mysqli (extensão nativa do PHP)
- **Ferramentas de Acesso:**
  - QueryBuilder (`database/QueryBuilder.php`)
  - Functions utilitárias (`database/functions.php`)

## Estrutura
- **Organização:** Por funcionalidade (monolítica)
- **Padrões de Arquivos:**
  - `ajax_*.php` para scripts AJAX
  - `JANELA_*.php` para modais

## Testing
- **Testes E2E:** Cypress (testes end-to-end automatizados)
- **Testes Unitários:** Não há framework de testes unitários PHP ativo no projeto principal
