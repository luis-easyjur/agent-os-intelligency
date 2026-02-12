# Padrões de Tratamento de Erros no 123jus-ui

## Boas Práticas

- **Mensagens Amigáveis ao Usuário**: Fornecer mensagens de erro claras e acionáveis aos usuários sem expor detalhes técnicos ou informações de segurança
- **Falhar Rápido e Explicitamente**: Validar entrada e verificar pré-condições cedo; falhar com mensagens de erro claras ao invés de permitir estado inválido
- **Tipos de Erro Específicos**: Usar objetos de erro específicos ao invés de genéricos para permitir tratamento direcionado
- **Tratamento de Erro Centralizado**: Tratar erros em componentes ou hooks apropriados ao invés de espalhar blocos try-catch em todos os lugares
- **Degradação Graciosa**: Projetar componentes para degradar graciosamente quando serviços não críticos falham ao invés de quebrar completamente
- **Feedback Visual**: Usar toasts/notificações para informar erros ao usuário
- **Limpar Recursos**: Sempre limpar recursos (timeouts, subscriptions) em useEffect cleanup
- **Sanitização de mensagens**: Para mensagens de erro exibidas ao usuário (toast, etc.), usar `sanitizeErrorMessage(error?.message)` de `utils/errorSanitizer`; nunca exibir `error.message` cru (evita vazamento de SQL, detalhes de banco ou rede)

## Padrão de Tratamento no 123jus-ui

### Componentes

Componentes devem usar try-catch para tratamento de erros em operações assíncronas:

```typescript
import React, { useState } from 'react';
import api from '../../services/api';
import toast from 'react-hot-toast';
import { sanitizeErrorMessage } from '../../utils/errorSanitizer';
import { toastDefsError } from '../../config/toastConfig';

const Component: React.FC = () => {
    const [loading, setLoading] = useState(false);

    const handleAction = async () => {
        setLoading(true);
        try {
            const response = await api.post('/endpoint', data);
            
            if (response.data.status === 'error') {
                throw new Error(response.data.message);
            }
            
            toast.success('Ação realizada com sucesso');
        } catch (error: any) {
            console.error('Erro ao realizar ação:', error);
            toast.error(sanitizeErrorMessage(error?.message) || 'Erro ao realizar ação', toastDefsError);
        } finally {
            setLoading(false);
        }
    };

    return (
        <button onClick={handleAction} disabled={loading}>
            {loading ? 'Carregando...' : 'Ação'}
        </button>
    );
};
```

### Services

Services devem tratar erros e lançar exceções apropriadas:

```typescript
import api from './api';

export const comentarioService = {
    async getComentarios(id_proposta: number) {
        try {
            const response = await api.get(`/comentario/${id_proposta}`);
            
            if (response.data.status === 'success') {
                return response.data.result || [];
            }
            
            throw new Error(response.data.message || 'Erro ao buscar comentários');
        } catch (error: any) {
            console.error('Erro ao buscar comentários:', error);
            throw error;
        }
    }
};
```

### Hooks Customizados

Hooks devem tratar erros apropriadamente:

```typescript
import { useState, useCallback } from 'react';
import toast from 'react-hot-toast';

export const useCepLookup = () => {
    const [loadingCep, setLoadingCep] = useState(false);

    const buscarCep = useCallback(async (cep: string, onSuccess?: (data: any) => void) => {
        setLoadingCep(true);
        try {
            const response = await fetch(`https://viacep.com.br/ws/${cep}/json/`);
            const data = await response.json();

            if (data.erro) {
                toast.error('CEP não encontrado. Verifique o CEP digitado.');
                return;
            }

            if (onSuccess) {
                onSuccess(data);
            }
        } catch (error) {
            console.error('Erro ao buscar CEP:', error);
            toast.error('Erro ao buscar CEP. Tente novamente.');
        } finally {
            setLoadingCep(false);
        }
    }, []);

    return { loadingCep, buscarCep };
};
```

## Formato de Erro da API

A API retorna erros no formato:

```json
{
  "code": 200,
  "status": "error",
  "message": "Erro na validação",
  "result": [
    {
      "field": "id_comarca",
      "message": "Comarca não encontrada"
    }
  ]
}
```

## Tratamento de Erros de API

Sempre verificar `response.data.status`:

```typescript
const response = await api.post('/endpoint', data);

if (response.data.status === 'error') {
    // Tratar erro
    const errors = response.data.result || [];
    errors.forEach((error: any) => {
        toast.error(sanitizeErrorMessage(error?.message) || 'Erro ao processar', toastDefsError);
    });
    return;
}

// Processar sucesso
const result = response.data.result;
```

## Notificações de Erro

Usar `react-hot-toast` com opções de `config/toastConfig`. Sempre sanitizar mensagens de erro antes de exibir:

```typescript
import toast from 'react-hot-toast';
import { toastDefs, toastDefsError } from '../config/toastConfig';
import { sanitizeErrorMessage } from '../utils/errorSanitizer';

toast.success('Ação realizada com sucesso', toastDefs);
toast.error(sanitizeErrorMessage(error?.message) || 'Erro ao realizar ação', toastDefsError);
```

## Tratamento de Erros Assíncronos

Para operações assíncronas, sempre usar try-catch:

```typescript
useEffect(() => {
    let cancelled = false;

    async function fetchData() {
        try {
            const response = await api.get('/endpoint');
            if (!cancelled && response.data.status === 'success') {
                setData(response.data.result);
            }
        } catch (error: any) {
            if (!cancelled) {
                console.error('Erro ao buscar dados:', error);
                toast.error(sanitizeErrorMessage(error?.message) || 'Erro ao buscar dados', toastDefsError);
            }
        }
    }

    fetchData();

    return () => {
        cancelled = true;
    };
}, []);
```

## Logging de Erros

Erros devem ser logados no console para debugging:

```typescript
catch (error: any) {
    console.error('Erro em Component.handleAction:', error);
    toast.error(sanitizeErrorMessage(error?.message) || 'Erro ao realizar ação', toastDefsError);
}
```

## Estados de Loading e Error

Componentes devem gerenciar estados de loading e error:

```typescript
const [loading, setLoading] = useState(false);
const [error, setError] = useState<string | null>(null);

const handleAction = async () => {
    setLoading(true);
    setError(null);
    try {
        // Operação
    } catch (error: any) {
        setError(error.message);
    } finally {
        setLoading(false);
    }
};
```
