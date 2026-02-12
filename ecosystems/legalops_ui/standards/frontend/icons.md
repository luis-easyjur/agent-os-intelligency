# Padrões de Ícones no 123jus-ui

## Visão Geral

O 123jus-ui utiliza duas fontes principais de ícones:
1. **lucide-react** - Biblioteca de ícones moderna
2. **Ícones customizados** - SVG customizados em `src/components/icons/`

## lucide-react

### Uso Básico

```typescript
import { Clock, Play, CheckCircle, Loader2, Trash2 } from 'lucide-react';

<Clock size={16} color="#54666F" />
<Play size={20} />
<CheckCircle size={24} color={theme.colors.interfaceSuccess} />
```

### Ícones Comuns

Ícones frequentemente usados:
- `Clock` - Relógio/tempo
- `Play` - Play/iniciar
- `CheckCircle` - Concluído
- `Loader2` - Loading/spinner
- `Trash2` - Excluir
- `Download` - Download
- `File`, `FileText`, `FileImage` - Tipos de arquivo
- `ExternalLink` - Link externo
- `Paperclip` - Anexo
- `X` - Fechar

### Com Tema

```typescript
import { useTheme } from 'styled-components';
import { Clock } from 'lucide-react';

const Component = () => {
    const theme = useTheme() as any;
    
    return (
        <Clock 
            size={16} 
            color={theme.colors.interfaceText400} 
        />
    );
};
```

## Ícones Customizados

### Estrutura

Ícones customizados em `src/components/icons/`:
- `IconesSistema.tsx` - Ícones do sistema
- `index.tsx` - Exportações de ícones

### Uso

```typescript
import { 
    IconLoading, 
    IconArrowLeft, 
    IconSearch,
    IconListDemand,
    IconProfessional,
    IconOperator
} from '../icons';

<IconLoading />
<IconArrowLeft color="#54666F" />
```

### Ícones Customizados Disponíveis

- `IconLoading` - Spinner de loading
- `IconArrowLeft` - Seta para esquerda
- `IconSearch` - Busca
- `IconListDemand` - Lista de demandas
- `IconProfessional` - Profissional
- `IconOperator` - Operador
- `IconBell` - Notificações
- `IconProfile` - Perfil
- E muitos outros...

## Padrões de Uso

### Tamanhos

```typescript
// lucide-react
<Clock size={16} />  // Pequeno
<Clock size={20} />  // Médio
<Clock size={24} />  // Grande

// Ícones customizados (geralmente tamanho fixo)
<IconLoading />
```

### Cores

```typescript
// Cor específica
<Clock color="#54666F" />

// Cor do tema
import { comum } from '../styles/Themes/light';
<Clock color={comum.colors.interfaceText400} />

// Com useTheme
const theme = useTheme() as any;
<Clock color={theme.colors.primaryRed400} />
```

## Boas Práticas

- **lucide-react para novos ícones**: Preferir lucide-react para novos ícones
- **Ícones customizados existentes**: Usar ícones customizados quando já existem
- **Tamanhos consistentes**: Manter tamanhos consistentes (16, 20, 24)
- **Cores do tema**: Sempre usar cores do tema ao invés de cores hardcoded
- **Acessibilidade**: Incluir aria-label quando ícone não tem texto adjacente

## Exemplo Completo

```typescript
import { Clock, Loader2 } from 'lucide-react';
import { IconLoading } from '../icons';
import { comum } from '../styles/Themes/light';

const Component = () => {
    const [loading, setLoading] = useState(false);

    return (
        <div>
            {/* Ícone com cor do tema */}
            <Clock size={16} color={comum.colors.interfaceText400} />
            
            {/* Ícone de loading */}
            {loading ? (
                <Loader2 size={16} color={comum.colors.primaryRed400} className="spinner" />
            ) : (
                <IconLoading />
            )}
        </div>
    );
};
```
