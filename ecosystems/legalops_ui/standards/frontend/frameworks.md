# Frameworks e Bibliotecas Frontend no 123jus-ui

## React Router DOM

O projeto utiliza React Router DOM v5.3.0 para roteamento:

### Configuração

```typescript
import { BrowserRouter, Switch, Route, Redirect } from 'react-router-dom';

<BrowserRouter>
    <Switch>
        <Route path="/login" component={Login} />
        <Route path="/demandas" component={Demandas} />
    </Switch>
</BrowserRouter>
```

### Rotas Protegidas

Componente `PrivateRoutes` para rotas que requerem autenticação:

```typescript
import PrivateRoutes from './PrivateRoutes';

<PrivateRoutes path="/demandas" exact component={Demandas} isPrivate />
```

**Características:**
- Verifica se usuário está autenticado
- Redireciona para `/login` se não autenticado
- Redireciona para `/demandas` se autenticado tentando acessar rota pública

### Hooks do React Router

```typescript
import { useHistory, useParams, useLocation } from 'react-router-dom';

const Component = () => {
    const history = useHistory();
    const { id } = useParams<{ id: string }>();
    const location = useLocation();
    
    const handleNavigate = () => {
        history.push('/demandas');
    };
};
```

## styled-components

Framework CSS-in-JS para estilização com suporte a temas. Ver documento `css.md` para detalhes.

## Tailwind CSS

Framework CSS utilitário. Ver documento `css.md` para detalhes.

## react-hook-form

Biblioteca para gerenciamento de formulários:

```typescript
import { useForm } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import * as yup from 'yup';

const schema = yup.object().shape({
    nome: yup.string().required('Nome é obrigatório'),
    email: yup.string().email('Email inválido').required('Email é obrigatório'),
});

const Component = () => {
    const { register, handleSubmit, formState: { errors } } = useForm({
        resolver: yupResolver(schema)
    });
    
    const onSubmit = (data: any) => {
        // Processar dados
    };
    
    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <input {...register('nome')} />
            {errors.nome && <span>{errors.nome.message}</span>}
        </form>
    );
};
```

## yup

Biblioteca de validação de schemas usada com react-hook-form:

```typescript
import * as yup from 'yup';

const schema = yup.object().shape({
    cep: yup.string()
        .required('CEP é obrigatório')
        .test('cep-validation', 'CEP inválido', function(value) {
            const numbers = value?.replace(/\D/g, '') || '';
            return numbers.length === 8;
        }),
    email: yup.string()
        .required('Email é obrigatório')
        .email('Digite um e-mail válido'),
});
```

## axios

Cliente HTTP para chamadas à API:

```typescript
import api from '../services/api';

// GET
const response = await api.get('/proposta');

// POST
const response = await api.post('/proposta', data);

// PUT
const response = await api.put(`/proposta/${id}`, data);

// DELETE
const response = await api.delete(`/proposta/${id}`);

// Download de arquivo
await api.download('/arquivo/download', 'nome-arquivo.pdf');
```

**Configuração:**
- Base URL configurada via `REACT_APP_API_URL` ou padrão
- Interceptor para adicionar token JWT automaticamente
- Método `download` customizado para download de arquivos

## react-hot-toast

Biblioteca para notificações/toasts:

```typescript
import toast from 'react-hot-toast';

// Sucesso
toast.success('Operação realizada com sucesso');

// Erro
toast.error('Erro ao realizar operação');

// Loading
const toastId = toast.loading('Processando...');
toast.success('Concluído!', { id: toastId });
```

**Configuração:**
- Configurado globalmente no `Layout` com `Toaster`
- Estilos customizados para sucesso e erro
- Posição: top-center
- Duração: 3000ms

## @dnd-kit

Biblioteca para drag and drop (usado no Kanban de pendencias):

```typescript
import { DndContext } from '@dnd-kit/core';
import { SortableContext } from '@dnd-kit/sortable';

<DndContext onDragEnd={handleDragEnd}>
    <SortableContext items={items}>
        {/* Componentes sortable */}
    </SortableContext>
</DndContext>
```

## Boas Práticas

- **React Router**: Usar hooks do React Router para navegação
- **Formulários**: Sempre usar react-hook-form com yup para validação
- **API Calls**: Usar o cliente `api` configurado ao invés de axios direto
- **Notificações**: Usar react-hot-toast para feedback ao usuário
- **Estilização**: Preferir styled-components para componentes, Tailwind para utilitários
- **TypeScript**: Tipar todas as props, estados e retornos de hooks
