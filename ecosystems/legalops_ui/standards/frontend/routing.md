# Padrões de Roteamento no 123jus-ui

## Visão Geral

O 123jus-ui utiliza React Router DOM v5.3.0 para gerenciamento de rotas e navegação. O sistema implementa rotas públicas e privadas com autenticação.

## Configuração de Rotas

### BrowserRouter

O `BrowserRouter` envolve toda a aplicação em `App.tsx`:

```typescript
import { BrowserRouter } from 'react-router-dom';

function App() {
    return (
        <BrowserRouter>
            <Routes />
        </BrowserRouter>
    );
}
```

### Definição de Rotas

Rotas são definidas em `src/routes/index.tsx`:

```typescript
import { Switch, Redirect } from 'react-router-dom';
import PrivateRoutes from './PrivateRoutes';

function Routes() {
    return (
        <Switch>
            {/* Rotas públicas */}
            <PrivateRoutes path="/login" exact component={Login} />
            <PrivateRoutes path="/cadastrar" exact component={Register} />
            
            {/* Rotas privadas */}
            <Layout>
                <PrivateRoutes path="/demandas" exact component={Demandas} isPrivate />
                <PrivateRoutes path="/demandas/:id" exact component={Demanda} isPrivate />
            </Layout>
        </Switch>
    );
}
```

## Rotas Protegidas

### PrivateRoutes

Componente customizado para rotas protegidas em `src/routes/PrivateRoutes.tsx`:

```typescript
import { Route, Redirect } from 'react-router-dom';
import { useAuth } from '../contexts/UserContext';

interface RouteProps {
    isPrivate?: boolean;
    component: React.ComponentType;
}

const Route: React.FC<RouteProps> = ({ isPrivate = false, component: Component, ...rest }) => {
    const { user } = useAuth();

    return (
        <Route
            {...rest}
            render={({ location }) => {
                return isPrivate === !!user ? (
                    <Component />
                ) : (
                    <Redirect
                        to={{
                            pathname: isPrivate ? "/login" : "/demandas",
                            state: { form: location },
                        }}
                    />
                );
            }}
        />
    );
};
```

**Características:**
- Verifica se usuário está autenticado através do `UserContext`
- Redireciona para `/login` se não autenticado tentando acessar rota privada
- Redireciona para `/demandas` se autenticado tentando acessar rota pública
- Preserva localização original para redirecionamento após login

## Rotas Principais

### Rotas Públicas

- `/` - Home do site
- `/login` - Página de login
- `/cadastrar` - Página de registro
- `/cadastro-completo` - Completar cadastro
- `/solicitarservico` - Solicitar serviço
- `/servico-completo` - Serviço completo
- `/recuperar-senha/:id` - Recuperação de senha
- `/registro-solicitante/:id` - Registro de solicitante
- `/quemsomos` - Quem somos
- `/solicitardemanda` - Solicitar demanda
- `/receberoportunidades` - Receber oportunidades
- `/contato` - Contato
- `/politicas-cookies` - Políticas de cookies

### Rotas Privadas

- `/demandas` - Listagem de demandas
- `/demandas/:id` - Detalhes de uma demanda
- `/demandas/:id/:aba` - Detalhes de demanda com aba específica
- `/demandas/:id/orientacoes/orientacoes-completas` - Formulário de orientações completas
- `/operadores` - Listagem de operadores
- `/operadores/:id` - Perfil de operador
- `/profissionais` - Listagem de profissionais
- `/profissionais/:id` - Perfil de profissional
- `/solicitantes` - Listagem de solicitantes
- `/solicitantes/:id` - Perfil de solicitante
- `/notificacoes` - Notificações
- `/nova-proposta` - Nova proposta
- `/propostas/:id` - Editar proposta
- `/Nova-Demanda` - Nova demanda
- `/formulario-proposta` - Formulário de proposta
- `/cadastrar/operador` - Cadastrar operador
- `/operador/concluir` - Concluir cadastro de operador
- `/proposta/concluir` - Concluir proposta

### Rotas Temporariamente Desativadas

As seguintes rotas redirecionam para `/demandas`:
- `/dashboard` → `/demandas`
- `/financeiro` → `/demandas`
- `/propostas` → `/demandas`

## Hooks do React Router

### useHistory

Para navegação programática:

```typescript
import { useHistory } from 'react-router-dom';

const Component = () => {
    const history = useHistory();
    
    const handleNavigate = () => {
        history.push('/demandas');
    };
    
    const handleGoBack = () => {
        if (window.history.length > 1) {
            history.goBack();
        } else {
            history.push('/demandas');
        }
    };
};
```

### useParams

Para obter parâmetros da URL:

```typescript
import { useParams } from 'react-router-dom';

const Component = () => {
    const { id } = useParams<{ id: string }>();
    // id contém o valor do parâmetro :id na rota
};
```

### useLocation

Para obter informações sobre a localização atual:

```typescript
import { useLocation } from 'react-router-dom';

const Component = () => {
    const location = useLocation();
    // location.pathname, location.search, location.state
};
```

## Navegação

### Navegação Programática

```typescript
import { useHistory } from 'react-router-dom';

const handleSuccess = () => {
    history.push('/demandas');
};

const handleCancel = () => {
    history.goBack();
};
```

### Links

```typescript
import { Link } from 'react-router-dom';

<Link to="/demandas">Demandas</Link>
<Link to={`/demandas/${id}`}>Ver Demanda</Link>
```

## Boas Práticas

- **Rotas Protegidas**: Sempre usar `isPrivate` para rotas que requerem autenticação
- **Redirecionamento**: Usar `Redirect` para redirecionamentos condicionais
- **Preservar Estado**: Usar `location.state` para preservar estado durante redirecionamentos
- **Parâmetros Tipados**: Tipar parâmetros de rota usando TypeScript
- **Navegação Segura**: Verificar histórico antes de usar `goBack()`
- **Rotas Aninhadas**: Usar `Layout` para rotas que compartilham layout comum

## Exemplo Completo

```typescript
import { useHistory, useParams } from 'react-router-dom';
import { useAuth } from '../../contexts/UserContext';

const DemandaPage = () => {
    const history = useHistory();
    const { id } = useParams<{ id: string }>();
    const { user } = useAuth();
    
    const handleEdit = () => {
        history.push(`/demandas/${id}/editar`);
    };
    
    const handleBack = () => {
        if (window.history.length > 1) {
            history.goBack();
        } else {
            history.push('/demandas');
        }
    };
    
    return (
        <div>
            <button onClick={handleBack}>Voltar</button>
            {user?.profile === 'operador' && (
                <button onClick={handleEdit}>Editar</button>
            )}
        </div>
    );
};
```
