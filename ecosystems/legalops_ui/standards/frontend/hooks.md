# Padrões de Hooks no 123jus-ui

## Visão Geral

O 123jus-ui utiliza hooks do React (nativos e customizados) para gerenciar estado, efeitos colaterais e lógica reutilizável. Hooks customizados estão localizados em `src/hooks/`.

## Hooks Nativos do React

### useState

Para gerenciar estado local:

```typescript
import { useState } from 'react';

const Component = () => {
    const [count, setCount] = useState(0);
    const [loading, setLoading] = useState(false);
    const [data, setData] = useState<any[]>([]);
};
```

### useEffect

Para efeitos colaterais (chamadas de API, subscriptions, etc.):

```typescript
import { useEffect, useState } from 'react';

const Component = () => {
    const [data, setData] = useState<any[]>([]);

    useEffect(() => {
        async function fetchData() {
            const response = await api.get('/endpoint');
            setData(response.data.result);
        }
        fetchData();
    }, []); // Executa apenas uma vez

    useEffect(() => {
        // Cleanup
        return () => {
            // Limpar recursos
        };
    }, [dependencies]);
};
```

### useCallback

Para memoizar funções:

```typescript
import { useCallback } from 'react';

const Component = ({ onUpdate }: { onUpdate: () => void }) => {
    const handleClick = useCallback(() => {
        onUpdate();
    }, [onUpdate]);
};
```

### useMemo

Para memoizar valores computados:

```typescript
import { useMemo } from 'react';

const Component = ({ items }: { items: any[] }) => {
    const filteredItems = useMemo(() => {
        return items.filter(item => item.active);
    }, [items]);
};
```

### useContext

Para acessar contextos:

```typescript
import { useContext } from 'react';
import { AuthContext } from '../contexts/UserContext';

const Component = () => {
    const { user, signOut } = useContext(AuthContext);
};
```

## Hooks Customizados

### useAuth

Hook para autenticação (exportado do UserContext):

```typescript
import { useAuth } from '../contexts/UserContext';

const Component = () => {
    const { user, signIn, signOut, updateUser } = useAuth();
    
    // user contém: id, nome, email, profile, etc.
};
```

### useSidebarFiltros

Hook para gerenciar sidebar de filtros:

```typescript
import { useSidebarFiltros } from '../contexts/SidebarFiltrosContext';

const Component = () => {
    const { 
        isSidebarFiltrosOpen, 
        setSidebarFiltrosOpen, 
        filtros, 
        setFiltros,
        resetFiltros 
    } = useSidebarFiltros();
};
```

### useCepLookup

Hook para busca de CEP via ViaCEP:

```typescript
import { useCepLookup } from '../hooks/useCepLookup';

const Component = () => {
    const { loadingCep, buscarCep } = useCepLookup();
    
    const handleCepChange = (cep: string) => {
        buscarCep(cep, (data) => {
            // data contém: cep, logradouro, bairro, localidade, uf
            setEndereco(data);
        });
    };
};
```

### useEdicaoDemanda

Hook para gerenciar edição de demandas:

```typescript
import { useEdicaoDemanda } from '../hooks/useEdicaoDemanda';

const Component = ({ demanda }: { demanda: IProposta }) => {
    const {
        isEditing,
        dadosEditados,
        iniciarEdicao,
        cancelarEdicao,
        salvarEdicao,
        hasChanges,
        errors,
        isSaving
    } = useEdicaoDemanda(demanda, handleSaveSuccess);
};
```

### useDocumentTitle

Hook para atualizar título do documento:

```typescript
import { useDocumentTitle } from '../hooks/useDocumentTitle';

const Component = () => {
    useDocumentTitle('Título da Página');
};
```

### useMetaDescription

Hook para atualizar meta description:

```typescript
import { useMetaDescription } from '../hooks/useMetaDescription';

const Component = () => {
    useMetaDescription('Descrição da página');
};
```

## Criando Hooks Customizados

### Estrutura Básica

```typescript
import { useState, useCallback } from 'react';

export const useCustomHook = (param: any) => {
    const [state, setState] = useState(initialValue);

    const action = useCallback(() => {
        // Lógica
    }, [dependencies]);

    return {
        state,
        action
    };
};
```

### Exemplo: useCepLookup

```typescript
import { useState, useCallback } from 'react';
import toast from 'react-hot-toast';

export const useCepLookup = () => {
    const [loadingCep, setLoadingCep] = useState(false);

    const buscarCep = useCallback(async (cep: string, onSuccess?: (data: any) => void) => {
        const cepLimpo = cep.replace(/\D/g, '');
        
        if (cepLimpo.length !== 8) {
            return;
        }

        setLoadingCep(true);
        try {
            const response = await fetch(`https://viacep.com.br/ws/${cepLimpo}/json/`);
            const data = await response.json();

            if (data.erro) {
                toast.error('CEP não encontrado');
                return;
            }

            if (onSuccess) {
                onSuccess(data);
            }
        } catch (error) {
            console.error('Erro ao buscar CEP:', error);
            toast.error('Erro ao buscar CEP');
        } finally {
            setLoadingCep(false);
        }
    }, []);

    return {
        loadingCep,
        buscarCep
    };
};
```

## Boas Práticas

- **Prefixo use**: Todos os hooks customizados devem começar com `use`
- **Reutilização**: Criar hooks quando lógica é reutilizada em múltiplos componentes
- **Separação de Responsabilidades**: Um hook deve ter uma responsabilidade clara
- **TypeScript**: Tipar parâmetros e retornos de hooks
- **Cleanup**: Limpar recursos (timeouts, subscriptions) em useEffect cleanup
- **Dependências**: Incluir todas as dependências nos arrays de dependências
- **Performance**: Usar useCallback e useMemo quando apropriado para otimização

## Exemplo Completo

```typescript
import { useState, useEffect, useCallback } from 'react';
import api from '../services/api';
import toast from 'react-hot-toast';

interface UseDataFetchReturn {
    data: any[];
    loading: boolean;
    error: string | null;
    refetch: () => void;
}

export const useDataFetch = (endpoint: string): UseDataFetchReturn => {
    const [data, setData] = useState<any[]>([]);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState<string | null>(null);

    const fetchData = useCallback(async () => {
        setLoading(true);
        setError(null);
        try {
            const response = await api.get(endpoint);
            
            if (response.data.status === 'error') {
                throw new Error(response.data.message);
            }
            
            setData(response.data.result || []);
        } catch (err: any) {
            setError(err.message || 'Erro ao buscar dados');
            toast.error(err.message || 'Erro ao buscar dados');
        } finally {
            setLoading(false);
        }
    }, [endpoint]);

    useEffect(() => {
        fetchData();
    }, [fetchData]);

    return {
        data,
        loading,
        error,
        refetch: fetchData
    };
};
```
