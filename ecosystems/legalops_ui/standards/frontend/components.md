# Padrões de Componentes React no 123jus-ui

## Visão Geral

O 123jus-ui utiliza React 17 com TypeScript e segue uma arquitetura baseada em componentes funcionais. Os componentes são organizados por funcionalidade e reutilizáveis quando apropriado.

## Estrutura de Componentes

### Estrutura de pasta

Cada componente é uma pasta com:

- `index.tsx` — componente (entry)
- `styles.ts` — styled-components (opcional; usar quando houver estilos)

Subcomponentes em subpastas com o mesmo padrão (ex.: `CardDemanda/CabecalhoCardDemanda/index.tsx`, `styles.ts`).

### Organização

Componentes estão organizados em `src/components/` por domínio funcional:

- `Layout/` - Layout principal da aplicação
- `Header/` - Cabeçalho da aplicação
- `Sidebar/` - Barra lateral de navegação
- `Demanda/` - Componentes relacionados a demandas
- `Pendencias/` - Componentes de pendencias (incluindo Kanban)
- `NovaDemanda/` - Componentes para criação de demandas
- `form/` - Componentes de formulário reutilizáveis
- `Ui/` - Componentes de UI genéricos
- `CardDemanda/` - Cards de demanda
- `ListaDemandas/` - Componentes de listagem de demandas
- E outros...

## Padrões de Implementação

### Componentes Funcionais

Todos os componentes são funcionais usando hooks:

```typescript
import React, { useState, useEffect } from 'react';

interface ComponentProps {
    title: string;
    onAction: () => void;
}

const Component: React.FC<ComponentProps> = ({ title, onAction }) => {
    const [state, setState] = useState<string>('');

    useEffect(() => {
        // Lógica de efeito
    }, []);

    return (
        <div>
            <h1>{title}</h1>
            <button onClick={onAction}>Ação</button>
        </div>
    );
};

export default Component;
```

### Componentes com Estilos

Componentes utilizam styled-components para estilização:

```typescript
import styled from 'styled-components';

const Container = styled.div`
    display: flex;
    flex-direction: column;
    padding: 16px;
`;

const Component: React.FC<ComponentProps> = ({ title }) => {
    return (
        <Container>
            <h1>{title}</h1>
        </Container>
    );
};
```

### Componentes de Formulário

Componentes de formulário em `components/form/`:

- `InputDefault` - Input padrão com máscaras
- `InputCheckbox` - Checkbox
- `InputMoney` - Input para valores monetários
- `SelectEditavel` - Select editável
- `ButtonDefault` - Botão padrão
- `ButtonIcon` - Botão com ícone
- `DateTimePickerEditavel` - Seletor de data/hora
- E outros...

**Características:**
- Suporte a máscaras (react-input-mask)
- Validação integrada
- Estados de erro
- Campos obrigatórios

## Componentes Principais

### Layout

Componente principal que envolve a aplicação:
- Sidebar de navegação
- Header
- Menu mobile
- Sistema de temas (comum, admin, operador)
- Toaster para notificações

### Demanda

Componentes relacionados a visualização e edição de demandas:
- `AbaPrincipal` - Aba principal com informações
- `AbaAnexos` - Aba de anexos
- `AbaComentarios` - Aba de comentários
- `AbaHistorico` - Aba de histórico
- `AbaOrientacoes` - Aba de orientações
- `CabecalhoDemanda` - Cabeçalho da demanda
- Modais para ações (Finalizar, Concluir, Interromper, etc.)

### Pendencias

Sistema Kanban para gestão de pendencias:
- `KanbanPendencias` - Componente principal do Kanban
- `ColunaPendencias` - Coluna do Kanban
- `CardPendencia` - Card de pendencia
- `ModalCriarPendencia` - Modal para criar pendencia
- `ModalConcluirPendencia` - Modal para concluir pendencia
- `ModalVisualizarPendencia` - Modal para visualizar pendencia

### NovaDemanda

Componentes para criação de demandas:
- Formulários multi-step
- Validação de dados
- Upload de arquivos
- Seleção de partes do processo

### ListaDemandas

Componentes para listagem de demandas:
- Filtros avançados
- Paginação
- Agrupamento por data
- Skeleton loading

## Boas Práticas

- **Componentes Funcionais**: Sempre usar componentes funcionais com hooks
- **TypeScript**: Tipar todas as props e estados
- **Reutilização**: Criar componentes reutilizáveis quando apropriado
- **Separação de Estilos**: Usar styled-components ou arquivos `styles.ts` separados
- **Props Descritivas**: Usar nomes descritivos para props
- **Composição**: Preferir composição sobre herança
- **Hooks Customizados**: Extrair lógica reutilizável para hooks customizados
- **Performance**: Usar React.memo, useMemo, useCallback quando apropriado
- **Acessibilidade**: Incluir atributos ARIA e semântica HTML apropriada

## Exemplo Completo

```typescript
import React, { useState, useEffect } from 'react';
import styled from 'styled-components';
import api from '../../services/api';
import toast from 'react-hot-toast';

const Container = styled.div`
    display: flex;
    flex-direction: column;
    gap: 16px;
`;

interface CardProps {
    title: string;
    id: number;
    onUpdate?: () => void;
}

const Card: React.FC<CardProps> = ({ title, id, onUpdate }) => {
    const [loading, setLoading] = useState(false);

    const handleAction = async () => {
        setLoading(true);
        try {
            await api.post(`/endpoint/${id}`);
            toast.success('Ação realizada com sucesso');
            onUpdate?.();
        } catch (error: any) {
            toast.error(error.message || 'Erro ao realizar ação');
        } finally {
            setLoading(false);
        }
    };

    return (
        <Container>
            <h2>{title}</h2>
            <button onClick={handleAction} disabled={loading}>
                {loading ? 'Carregando...' : 'Ação'}
            </button>
        </Container>
    );
};

export default Card;
```
