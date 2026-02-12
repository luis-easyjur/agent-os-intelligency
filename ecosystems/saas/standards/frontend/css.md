# Padrões CSS

## Framework Principal

O projeto utiliza **dois sistemas Bootstrap**:
- **Bootstrap antigo** (legado): Usado em partes antigas do sistema
- **Bootstrap 5.3.7** (novo): Usado em partes modernizadas do sistema

## Encapsulamento com Classe `.rebrand`

Para evitar conflitos entre o Bootstrap antigo e o novo, **todo código HTML que utiliza Bootstrap 5.3.7 deve ser encapsulado dentro de um elemento com a classe `rebrand`**.

### Como Usar

```html
<!-- Código com Bootstrap 5.3.7 -->
<div class="rebrand">
    <button class="btn btn-primary">Botão Bootstrap 5</button>
    <div class="modal">...</div>
</div>

<!-- Código legado com Bootstrap antigo (fora do .rebrand) -->
<button class="btn btn-primary">Botão Bootstrap Antigo</button>
```

### Por que é Necessário

- O patch `rebrand-bootstrap-patch.js` gerencia componentes Bootstrap 5 (Tooltip, Popover, Modal, Toast, Offcanvas) dentro do escopo `.rebrand`
- O CSS `rebrand-bootstrap-reset.css` isola visualmente o novo Bootstrap do legado
- Isso garante que componentes Bootstrap 5 não interfiram com o código legado e vice-versa

### Boas Práticas

- **Sempre encapsular**: Todo HTML que usa Bootstrap 5.3.7 deve estar dentro de `.rebrand`
- **Priorizar Classes do Bootstrap**: Usar classes utilitárias do Bootstrap ao invés de criar CSS customizado sempre que possível
- **Evitar Sobrescrever Estilos do Framework**: Trabalhar com os padrões do Bootstrap ao invés de lutar contra eles com excesso de overrides
- **CSS Customizado Apenas Quando Necessário**: Criar classes CSS personalizadas apenas quando não for possível atingir o resultado desejado com as classes do Bootstrap
- **Manter Consistência Visual**: Utilizar o sistema de design do Bootstrap para manter consistência em todo o projeto
- **Organização de Arquivos CSS**: Manter CSS customizado organizado em arquivos separados por módulo/funcionalidade quando necessário
