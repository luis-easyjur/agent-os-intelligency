# Padrões de Formulários no 123jus-ui

## Visão Geral

O 123jus-ui utiliza `react-hook-form` com `yup` para gerenciamento e validação de formulários. Componentes de formulário reutilizáveis estão em `src/components/form/`.

## react-hook-form

### Configuração Básica

```typescript
import { useForm } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import * as yup from 'yup';

const schema = yup.object().shape({
    nome: yup.string().required('Nome é obrigatório'),
    email: yup.string().email('Email inválido').required('Email é obrigatório'),
});

const Component = () => {
    const { register, handleSubmit, formState: { errors }, watch, setValue } = useForm({
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

## Componentes de Formulário

### InputDefault

Input padrão com suporte a máscaras:

```typescript
import InputDefault from '../form/InputDefault';

<InputDefault
    title="Nome"
    value={watch('nome')}
    onChangeText={(value) => setValue('nome', value)}
    error={errors.nome?.message}
    obrigatorio
    mask=""
/>
```

**Props:**
- `title` - Título do campo
- `value` - Valor do campo
- `onChangeText` - Callback de mudança
- `error` - Mensagem de erro
- `obrigatorio` - Indica se campo é obrigatório
- `mask` - Máscara de input (ex: "99999-999" para CEP)
- `type` - Tipo do input (text, email, password, etc.)
- `disabled` - Desabilita o campo

### InputMoney

Input para valores monetários:

```typescript
import InputMoney from '../form/InputMoney';

<InputMoney
    title="Valor"
    value={watch('valor')}
    onChangeText={(value) => setValue('valor', value)}
    error={errors.valor?.message}
/>
```

### InputCheckbox

Checkbox:

```typescript
import InputCheckbox from '../form/InputCheckbox';

<InputCheckbox
    title="Aceito os termos"
    checked={watch('aceito')}
    onChange={(checked) => setValue('aceito', checked)}
/>
```

### SelectEditavel

Select editável:

```typescript
import SelectEditavel from '../form/SelectEditavel';

<SelectEditavel
    title="Comarca"
    value={watch('id_comarca')}
    onChange={(value) => setValue('id_comarca', value)}
    options={comarcas}
    error={errors.id_comarca?.message}
/>
```

### ButtonDefault

Botão padrão:

```typescript
import ButtonDefault from '../form/ButtonDefault';

<ButtonDefault
    type="submit"
    className="access"
    loading={isSubmitting}
    disabled={!isValid}
>
    Salvar
</ButtonDefault>
```

**Variantes:**
- `access` - Botão primário (vermelho)
- `cancel` - Botão de cancelar
- `save` - Botão de salvar
- `danger` - Botão de perigo
- `continue` - Botão de continuar

### DateTimePickerEditavel

Seletor de data/hora:

```typescript
import DateTimePickerEditavel from '../form/DateTimePickerEditavel';

<DateTimePickerEditavel
    title="Data"
    value={watch('data')}
    onChange={(value) => setValue('data', value)}
    error={errors.data?.message}
/>
```

## Validação com yup

### Schema de Validação

```typescript
import * as yup from 'yup';

const schema = yup.object().shape({
    nome: yup.string().required('Nome é obrigatório'),
    email: yup.string()
        .required('Email é obrigatório')
        .email('Digite um e-mail válido'),
    cep: yup.string()
        .required('CEP é obrigatório')
        .test('cep-validation', 'CEP inválido', function(value) {
            const numbers = value?.replace(/\D/g, '') || '';
            return numbers.length === 8;
        }),
    telefone: yup.string()
        .required('Telefone é obrigatório')
        .min(11, 'Telefone inválido')
        .max(11, 'Telefone inválido'),
    valor: yup.number()
        .required('Valor é obrigatório')
        .min(0, 'Valor deve ser positivo'),
});
```

### Validações Customizadas

```typescript
const schema = yup.object().shape({
    senha: yup.string()
        .required('Senha é obrigatória')
        .min(6, 'Senha deve ter no mínimo 6 caracteres'),
    confirmarSenha: yup.string()
        .required('Confirmação de senha é obrigatória')
        .oneOf([yup.ref('senha')], 'Senhas não coincidem'),
});
```

## Máscaras

### Máscaras Disponíveis

Funções de máscara em `src/utils/masks.ts`:

```typescript
import { applyCepMask, applyProcessoMask, applyMoneyMask } from '../utils/masks';

// CEP: 12345-678
const cepMasked = applyCepMask('12345678');

// Processo: 1234567-89.2023.4.12.3456
const processoMasked = applyProcessoMask('12345678920234123456');

// Dinheiro: R$ 1.234,56
const moneyMasked = applyMoneyMask(1234.56);
```

### Uso em Inputs

```typescript
<InputDefault
    mask="99999-999" // CEP
    value={watch('cep')}
    onChangeText={(value) => setValue('cep', value)}
/>
```

## Exemplo Completo

```typescript
import { useForm } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import * as yup from 'yup';
import InputDefault from '../form/InputDefault';
import ButtonDefault from '../form/ButtonDefault';
import api from '../services/api';
import toast from 'react-hot-toast';

const schema = yup.object().shape({
    nome: yup.string().required('Nome é obrigatório'),
    email: yup.string()
        .required('Email é obrigatório')
        .email('Digite um e-mail válido'),
    cep: yup.string()
        .required('CEP é obrigatório')
        .test('cep-validation', 'CEP inválido', function(value) {
            const numbers = value?.replace(/\D/g, '') || '';
            return numbers.length === 8;
        }),
});

const FormComponent = () => {
    const { register, handleSubmit, formState: { errors }, watch, setValue } = useForm({
        resolver: yupResolver(schema)
    });
    
    const [loading, setLoading] = useState(false);

    const onSubmit = async (data: any) => {
        setLoading(true);
        try {
            const response = await api.post('/endpoint', data);
            
            if (response.data.status === 'error') {
                throw new Error(response.data.message);
            }
            
            toast.success('Formulário enviado com sucesso');
        } catch (error: any) {
            toast.error(error.message || 'Erro ao enviar formulário');
        } finally {
            setLoading(false);
        }
    };
    
    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <InputDefault
                title="Nome"
                value={watch('nome')}
                onChangeText={(value) => setValue('nome', value)}
                error={errors.nome?.message}
                obrigatorio
            />
            
            <InputDefault
                title="Email"
                type="email"
                value={watch('email')}
                onChangeText={(value) => setValue('email', value)}
                error={errors.email?.message}
                obrigatorio
            />
            
            <InputDefault
                title="CEP"
                mask="99999-999"
                value={watch('cep')}
                onChangeText={(value) => setValue('cep', value)}
                error={errors.cep?.message}
                obrigatorio
            />
            
            <ButtonDefault
                type="submit"
                className="access"
                loading={loading}
            >
                Enviar
            </ButtonDefault>
        </form>
    );
};
```

## Boas Práticas

- **Sempre usar react-hook-form**: Usar react-hook-form para todos os formulários
- **Validação com yup**: Usar yup para schemas de validação
- **Componentes reutilizáveis**: Usar componentes de formulário de `components/form/`
- **Feedback visual**: Exibir erros de validação nos componentes
- **Loading states**: Gerenciar estados de loading durante submissão
- **Máscaras**: Aplicar máscaras apropriadas para campos (CEP, telefone, processo, etc.)
- **TypeScript**: Tipar dados do formulário quando possível
