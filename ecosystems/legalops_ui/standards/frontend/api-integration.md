# Padrões de Integração com API no 123jus-ui

## Visão Geral

O 123jus-ui utiliza Axios para fazer requisições HTTP à API do 123jus-api. O cliente Axios é configurado centralmente e inclui interceptors para autenticação automática.

## Cliente API

### Configuração

Cliente Axios configurado em `src/services/api.ts`:

```typescript
import axios, { AxiosInstance } from 'axios';

interface ApiInstance extends AxiosInstance {
    download: (path: string, filename?: string) => Promise<any>;
}

const api = axios.create({
    baseURL: process.env.REACT_APP_API_URL ?? "https://production.backend.juridicoja.com"
}) as ApiInstance;
```

**Configuração:**
- Base URL via variável de ambiente `REACT_APP_API_URL`
- Fallback para URL de produção se não configurado
- Token JWT adicionado automaticamente via interceptor

### Interceptor de Autenticação

Token JWT é adicionado automaticamente nas requisições:

```typescript
// Em UserContext.tsx
api.defaults.headers.common['Authorization'] = `Bearer ${token}`;
```

O token é lido de `localStorage` e adicionado automaticamente quando o usuário faz login.

### Método Download

Cliente possui método customizado para download de arquivos:

```typescript
api.download = async (path: string, filename?: string) => {
    const response = await api.get(path, {
        responseType: 'blob'
    });
    
    // Cria blob e trigger download
    const blob = new Blob([response.data]);
    const url = window.URL.createObjectURL(blob);
    const link = document.createElement('a');
    link.href = url;
    link.download = filename || 'download';
    link.click();
    window.URL.revokeObjectURL(url);
};
```

## Padrões de Uso

### GET Request

```typescript
import api from '../services/api';

const fetchData = async () => {
    try {
        const response = await api.get('/proposta');
        
        if (response.data.status === 'success') {
            return response.data.result;
        }
        
        throw new Error(response.data.message || 'Erro ao buscar dados');
    } catch (error: any) {
        console.error('Erro:', error);
        throw error;
    }
};
```

### POST Request

```typescript
const createData = async (data: IProposta) => {
    try {
        const response = await api.post('/proposta', data);
        
        if (response.data.status === 'error') {
            throw new Error(response.data.message);
        }
        
        return response.data.result;
    } catch (error: any) {
        console.error('Erro:', error);
        throw error;
    }
};
```

### PUT Request

```typescript
const updateData = async (id: number, data: IProposta) => {
    try {
        const response = await api.put(`/proposta/${id}`, data);
        
        if (response.data.status === 'error') {
            throw new Error(response.data.message);
        }
        
        return response.data.result;
    } catch (error: any) {
        console.error('Erro:', error);
        throw error;
    }
};
```

### DELETE Request

```typescript
const deleteData = async (id: number) => {
    try {
        const response = await api.delete(`/proposta/${id}`);
        
        if (response.data.status === 'error') {
            throw new Error(response.data.message);
        }
        
        return response.data.result;
    } catch (error: any) {
        console.error('Erro:', error);
        throw error;
    }
};
```

### Download de Arquivo

```typescript
const downloadFile = async (fileId: number, filename: string) => {
    try {
        await api.download(`/arquivo/download/${fileId}`, filename);
    } catch (error: any) {
        console.error('Erro ao baixar arquivo:', error);
        toast.error('Erro ao baixar arquivo', toastDefsError);
    }
};
```

## Formato de Resposta da API

A API retorna sempre no formato padronizado:

### Sucesso

```json
{
  "code": 200,
  "status": "success",
  "message": "Ação realizada com sucesso",
  "result": { ... }
}
```

### Erro

```json
{
  "code": 200,
  "status": "error",
  "message": "Ocorreu um erro",
  "result": [
    {
      "field": "campo",
      "message": "Mensagem de erro específica"
    }
  ]
}
```

## Tratamento de Respostas

### Verificação de Status

Sempre verificar `response.data.status`:

```typescript
const response = await api.get('/endpoint');

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

### Tratamento de Erros

```typescript
try {
    const response = await api.post('/endpoint', data);
    
    if (response.data.status === 'error') {
        throw new Error(response.data.message || 'Erro ao processar');
    }
    
    // Sucesso
    toast.success('Operação realizada com sucesso', toastDefs);
} catch (error: any) {
    console.error('Erro:', error);
    toast.error(sanitizeErrorMessage(error?.message) || 'Erro ao realizar operação', toastDefsError);
}
```

## Services

### Estrutura de Services

Services organizam chamadas de API por domínio:

```typescript
// src/services/comentarioService.ts
import api from './api';

export const comentarioService = {
    async getComentarios(id_proposta: number) {
        const response = await api.get(`/comentario/${id_proposta}`);
        
        if (response.data.status === 'success') {
            return response.data.result || [];
        }
        
        throw new Error(response.data.message || 'Erro ao buscar comentários');
    },
    
    async createComentario(data: ICreateComentario) {
        const response = await api.post('/comentario', data);
        
        if (response.data.status === 'success') {
            return response.data.result;
        }
        
        throw new Error(response.data.message || 'Erro ao criar comentário');
    }
};
```

## Feedback (Toast)

Usar opções de `config/toastConfig` para manter estilo consistente:

- **Sucesso**: `toast.success(msg, toastDefs)` — importar `toastDefs` de `config/toastConfig`
- **Erro**: `toast.error(msg, toastDefsError)` — importar `toastDefsError` de `config/toastConfig`
- Para mensagens de erro vindas da API ou de exceções: usar `sanitizeErrorMessage(error?.message)` de `utils/errorSanitizer` antes de passar para `toast.error`

```typescript
import toast from 'react-hot-toast';
import { toastDefs, toastDefsError } from '../config/toastConfig';
import { sanitizeErrorMessage } from '../utils/errorSanitizer';

toast.success('Operação realizada com sucesso', toastDefs);
toast.error(sanitizeErrorMessage(error?.message) || 'Erro ao processar', toastDefsError);
```

## Boas Práticas

- **Sempre verificar status**: Verificar `response.data.status` antes de processar resultado
- **Tratamento de erros**: Sempre usar try-catch para operações assíncronas
- **Feedback ao usuário**: Usar toasts com `toastDefs` (sucesso) e `toastDefsError` (erro)
- **Services organizados**: Organizar chamadas de API em services por domínio
- **TypeScript**: Tipar respostas e parâmetros quando possível
- **Download de arquivos**: Usar método `api.download` para downloads
- **Sanitização de erros**: Sanitizar mensagens de erro com `sanitizeErrorMessage` antes de exibir ao usuário

## Exemplo Completo

```typescript
import { useState, useEffect } from 'react';
import api from '../services/api';
import toast from 'react-hot-toast';
import { toastDefs, toastDefsError } from '../config/toastConfig';
import { sanitizeErrorMessage } from '../utils/errorSanitizer';

const Component = () => {
    const [loading, setLoading] = useState(false);
    const [data, setData] = useState<any[]>([]);

    useEffect(() => {
        async function fetchData() {
            setLoading(true);
            try {
                const response = await api.get('/proposta');
                
                if (response.data.status === 'error') {
                    throw new Error(response.data.message);
                }
                
                setData(response.data.result || []);
            } catch (error: any) {
                console.error('Erro ao buscar dados:', error);
                toast.error(sanitizeErrorMessage(error?.message) || 'Erro ao buscar dados', toastDefsError);
            } finally {
                setLoading(false);
            }
        }

        fetchData();
    }, []);

    const handleCreate = async (formData: any) => {
        setLoading(true);
        try {
            const response = await api.post('/proposta', formData);
            
            if (response.data.status === 'error') {
                const errors = response.data.result || [];
                errors.forEach((error: any) => {
                    toast.error(sanitizeErrorMessage(error?.message) || 'Erro', toastDefsError);
                });
                return;
            }
            
            toast.success('Proposta criada com sucesso', toastDefs);
            // Atualizar lista
        } catch (error: any) {
            console.error('Erro:', error);
            toast.error(sanitizeErrorMessage(error?.message) || 'Erro ao criar proposta', toastDefsError);
        } finally {
            setLoading(false);
        }
    };

    return (
        <div>
            {loading ? 'Carregando...' : (
                <div>
                    {data.map(item => <div key={item.id}>{item.nome}</div>)}
                    <button onClick={() => handleCreate({ nome: 'Teste' })}>
                        Criar
                    </button>
                </div>
            )}
        </div>
    );
};
```
