# Padrões de Estados de Loading no 123jus-ui

## Visão Geral

O 123jus-ui utiliza múltiplas abordagens para indicar estados de carregamento, incluindo skeletons, spinners e estados de loading em botões.

## Skeleton Loading

### react-loading-skeleton

Biblioteca `react-loading-skeleton` para skeletons de loading:

```typescript
import Skeleton from 'react-loading-skeleton';
import 'react-loading-skeleton/dist/skeleton.css';

{loading ? (
    <>
        <Skeleton width={80} height={36} borderRadius={6} />
        <Skeleton width={120} height={12} />
        <Skeleton circle width={16} height={16} />
        <Skeleton width={180} height={18} />
    </>
) : (
    <ConteudoReal />
)}
```

### Componentes Skeleton Customizados

Componentes skeleton customizados para listagens:

```typescript
import SkeletonListaDemandas from '../ListaDemandas/SkeletonListaDemandas';

{loading ? (
    <SkeletonListaDemandas />
) : (
    <ListaDemandas data={data} />
)}
```

## Spinners

### lucide-react Loader2

Ícone de loading do lucide-react:

```typescript
import { Loader2 } from 'lucide-react';

<Loader2 size={16} color={comum.colors.primaryRed400} className="spinner" />
```

### IconLoading

Ícone de loading customizado:

```typescript
import { IconLoading } from '../icons';

<ButtonDefault loading={true}>
    {/* IconLoading é exibido automaticamente quando loading=true */}
</ButtonDefault>
```

## Estados de Loading em Componentes

### Loading em Botões

```typescript
<ButtonDefault
    loading={isSubmitting}
    disabled={isSubmitting}
>
    Salvar
</ButtonDefault>
```

Quando `loading={true}`, o botão exibe `IconLoading` ao invés do texto.

### Loading em Operações Assíncronas

```typescript
const [downloading, setDownloading] = useState(false);

const handleDownload = async () => {
    setDownloading(true);
    try {
        await api.download('/arquivo/download', 'arquivo.pdf');
    } finally {
        setDownloading(false);
    }
};

<button disabled={downloading}>
    {downloading ? <Loader2 size={16} /> : <Download size={16} />}
    {downloading ? 'Baixando...' : 'Baixar'}
</button>
```

## Padrões de Implementação

### Loading em Listagens

```typescript
const [loading, setLoading] = useState(false);
const [data, setData] = useState<any[]>([]);

useEffect(() => {
    async function fetchData() {
        setLoading(true);
        try {
            const response = await api.get('/endpoint');
            setData(response.data.result || []);
        } finally {
            setLoading(false);
        }
    }
    fetchData();
}, []);

return (
    <>
        {loading ? (
            <SkeletonListaDemandas />
        ) : (
            data.map(item => <Card key={item.id} data={item} />)
        )}
    </>
);
```

### Loading em Formulários

```typescript
const [loading, setLoading] = useState(false);

const handleSubmit = async (data: any) => {
    setLoading(true);
    try {
        await api.post('/endpoint', data);
        toast.success('Salvo com sucesso');
    } catch (error) {
        toast.error('Erro ao salvar');
    } finally {
        setLoading(false);
    }
};

<ButtonDefault
    type="submit"
    loading={loading}
    disabled={loading}
>
    Salvar
</ButtonDefault>
```

### Loading em Operações de Arquivo

```typescript
const [downloading, setDownloading] = useState(false);
const [excluindo, setExcluindo] = useState(false);

const handleDownload = async () => {
    setDownloading(true);
    try {
        await api.download(`/arquivo/${id}`, filename);
    } finally {
        setDownloading(false);
    }
};

<button disabled={downloading || excluindo}>
    {downloading ? (
        <Loader2 size={16} />
    ) : (
        <Download size={16} />
    )}
</button>
```

## Boas Práticas

- **Skeletons para Listagens**: Usar skeletons para listagens e cards
- **Spinners para Botões**: Usar spinners em botões durante operações
- **Estados Desabilitados**: Desabilitar controles durante loading
- **Feedback Visual**: Sempre fornecer feedback visual durante operações
- **Cleanup**: Limpar estados de loading em finally blocks
- **Performance**: Evitar re-renders desnecessários durante loading

## Exemplo Completo

```typescript
import { useState, useEffect } from 'react';
import SkeletonListaDemandas from '../ListaDemandas/SkeletonListaDemandas';
import { Loader2 } from 'lucide-react';
import ButtonDefault from '../form/ButtonDefault';
import api from '../services/api';

const Component = () => {
    const [loading, setLoading] = useState(false);
    const [saving, setSaving] = useState(false);
    const [data, setData] = useState<any[]>([]);

    useEffect(() => {
        setLoading(true);
        api.get('/endpoint')
            .then(response => {
                if (response.data.status === 'success') {
                    setData(response.data.result || []);
                }
            })
            .finally(() => setLoading(false));
    }, []);

    const handleSave = async () => {
        setSaving(true);
        try {
            await api.post('/endpoint', data);
        } finally {
            setSaving(false);
        }
    };

    return (
        <div>
            {loading ? (
                <SkeletonListaDemandas />
            ) : (
                <>
                    {data.map(item => <div key={item.id}>{item.nome}</div>)}
                    <ButtonDefault
                        onClick={handleSave}
                        loading={saving}
                        disabled={saving}
                    >
                        Salvar
                    </ButtonDefault>
                </>
            )}
        </div>
    );
};
```
