# Padrões de Gerenciamento de Estado no 123jus-ui

## Visão Geral

O 123jus-ui utiliza Context API do React para gerenciamento de estado global. O projeto não utiliza Redux ou outras bibliotecas de gerenciamento de estado, preferindo Context API nativo do React.

## Context API

### UserContext

Contexto principal para autenticação e dados do usuário em `src/contexts/UserContext.tsx`:

```typescript
import { createContext, useContext, useState, useCallback } from 'react';
import api from '../services/api';

interface User {
    id: string;
    nome: string;
    photo: string;
    email: string;
    profile: string;
    id_escritorio_solicitante?: number;
}

interface AuthContextData {
    user: User;
    signIn(credentials: SignInCredentials): Promise<void>;
    signOut(): void;
    updateUser(user: User): void;
    pageName: string;
    setPageName(page: any): void;
    pageType: any;
    setPageType(type: any): void;
}

const AuthContext = createContext<AuthContextData>({} as AuthContextData);

export function AuthProvider({ children }: { children: ReactNode }) {
    const [data, setData] = useState<AuthState>(() => {
        const token = localStorage.getItem('@Jj:token');
        const user = localStorage.getItem('@Jj:user');

        if (token && user) {
            api.defaults.headers.common['Authorization'] = `Bearer ${token}`;
            return { token, user: JSON.parse(user) };
        }

        return {} as AuthState;
    });

    const signIn = useCallback(async ({ email, senha }) => {
        const response = await api.post('login/geral', { email, senha });
        
        if (response.data.status === 'error') {
            throw new Error(response.data.message);
        }

        const { user, token } = response.data.result;
        localStorage.setItem('@Jj:token', token);
        localStorage.setItem('@Jj:user', JSON.stringify(user));
        api.defaults.headers.common['Authorization'] = `Bearer ${token}`;
        setData({ user, token });
    }, []);

    const signOut = useCallback(() => {
        localStorage.removeItem('@Jj:token');
        localStorage.removeItem('@Jj:user');
        setData({} as AuthState);
    }, []);

    return (
        <AuthContext.Provider value={{ user: data.user, signIn, signOut, ... }}>
            {children}
        </AuthContext.Provider>
    );
}

export function useAuth(): AuthContextData {
    const context = useContext(AuthContext);
    if (!context) {
        throw new Error('useAuth must be used within an AuthProvider');
    }
    return context;
}
```

**Características:**
- Gerencia autenticação e estado do usuário
- Persiste token e usuário em `localStorage`
- Adiciona token automaticamente nas requisições Axios
- Fornece hook `useAuth` para acesso ao contexto
- Gerencia nome e tipo da página atual

### SidebarFiltrosContext

Contexto para estado da sidebar de filtros em `src/contexts/SidebarFiltrosContext.tsx`:

```typescript
import { createContext, useContext, useState } from 'react';

interface SidebarFiltrosContextType {
    isSidebarFiltrosOpen: boolean;
    setSidebarFiltrosOpen: (open: boolean) => void;
    toggleSidebarFiltros: () => void;
    filtros: IFiltroListaDemandas;
    setFiltros: (filtros: IFiltroListaDemandas) => void;
    resetFiltros: () => void;
}

export const SidebarFiltrosProvider: React.FC = ({ children }) => {
    const [isSidebarFiltrosOpen, setSidebarFiltrosOpen] = useState(false);
    const [filtros, setFiltros] = useState<IFiltroListaDemandas>({});

    return (
        <SidebarFiltrosContext.Provider value={{ ... }}>
            {children}
        </SidebarFiltrosContext.Provider>
    );
};

export const useSidebarFiltros = () => {
    const context = useContext(SidebarFiltrosContext);
    if (!context) {
        throw new Error('useSidebarFiltros must be used within a SidebarFiltrosProvider');
    }
    return context;
};
```

## Estado Local

### useState

Para estado local de componentes:

```typescript
import { useState } from 'react';

const Component = () => {
    const [loading, setLoading] = useState(false);
    const [data, setData] = useState<any[]>([]);
    const [error, setError] = useState<string | null>(null);
};
```

### useReducer (quando apropriado)

Para estado complexo com múltiplas ações:

```typescript
import { useReducer } from 'react';

const reducer = (state: any, action: any) => {
    switch (action.type) {
        case 'SET_LOADING':
            return { ...state, loading: action.payload };
        case 'SET_DATA':
            return { ...state, data: action.payload };
        default:
            return state;
    }
};

const Component = () => {
    const [state, dispatch] = useReducer(reducer, { loading: false, data: [] });
};
```

## Persistência de Estado

### localStorage

Para persistir dados entre sessões:

```typescript
// Salvar
localStorage.setItem('@Jj:token', token);
localStorage.setItem('@Jj:user', JSON.stringify(user));

// Ler
const token = localStorage.getItem('@Jj:token');
const user = JSON.parse(localStorage.getItem('@Jj:user') || '{}');

// Remover
localStorage.removeItem('@Jj:token');
```

**Chaves utilizadas:**
- `@Jj:token` - Token JWT de autenticação
- `@Jj:user` - Dados do usuário autenticado

## Boas Práticas

- **Context API para Estado Global**: Usar Context API para estado compartilhado entre múltiplos componentes
- **useState para Estado Local**: Usar `useState` para estado local de componentes
- **Hooks Customizados**: Extrair lógica de estado para hooks customizados quando reutilizável
- **Persistência**: Usar `localStorage` apenas para dados que precisam persistir entre sessões
- **Inicialização de Estado**: Inicializar estado a partir de `localStorage` quando apropriado
- **Cleanup**: Limpar recursos (timeouts, subscriptions) em `useEffect` cleanup
- **Evitar Prop Drilling**: Usar Context API quando props precisam ser passadas através de muitos níveis

## Exemplo Completo

```typescript
import { useState, useEffect, useCallback } from 'react';
import { useAuth } from '../contexts/UserContext';
import api from '../services/api';

const Component = () => {
    const { user } = useAuth();
    const [loading, setLoading] = useState(false);
    const [data, setData] = useState<any[]>([]);

    useEffect(() => {
        async function fetchData() {
            setLoading(true);
            try {
                const response = await api.get('/endpoint');
                if (response.data.status === 'success') {
                    setData(response.data.result);
                }
            } catch (error) {
                console.error('Erro:', error);
            } finally {
                setLoading(false);
            }
        }

        if (user) {
            fetchData();
        }
    }, [user]);

    return (
        <div>
            {loading ? 'Carregando...' : data.map(item => <div key={item.id}>{item.nome}</div>)}
        </div>
    );
};
```
