# Padrões de Validação no 123jus-ui

## Boas Práticas

- **Validar no Servidor**: Sempre validar no servidor; nunca confiar apenas em validação do lado do cliente para segurança ou integridade de dados
- **Lado do Cliente para UX**: Usar validação do lado do cliente para fornecer feedback imediato ao usuário, mas duplicar verificações no servidor
- **Falhar Cedo**: Validar entrada o mais cedo possível e rejeitar dados inválidos antes do processamento
- **Mensagens de Erro Específicas**: Fornecer mensagens de erro claras e específicas por campo que ajudem usuários a corrigir sua entrada
- **Allowlists ao Invés de Blocklists**: Quando possível, definir o que é permitido ao invés de tentar bloquear tudo que não é
- **Validação de Tipo e Formato**: Verificar tipos de dados, formatos, intervalos e campos obrigatórios sistematicamente
- **Sanitizar Entrada**: Sanitizar entrada do usuário para prevenir ataques (XSS, etc.)
- **Validação de Regras de Negócio**: Validar regras de negócio (ex: status válido, datas válidas) antes de enviar para API
- **Validação Consistente**: Aplicar validação consistentemente em todos os formulários

## Validação com react-hook-form e yup

### Estrutura de Validação

O projeto utiliza `react-hook-form` com `yup` para validação de formulários:

```typescript
import { useForm } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
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

### Uso em Formulários

Validação integrada com componentes de formulário:

```typescript
import InputDefault from '../form/InputDefault';

const Component = () => {
    const { register, handleSubmit, formState: { errors } } = useForm({
        resolver: yupResolver(schema)
    });
    
    return (
        <form onSubmit={handleSubmit(onSubmit)}>
            <InputDefault
                title="Nome"
                value={watch('nome')}
                onChangeText={(value) => setValue('nome', value)}
                error={errors.nome?.message}
                obrigatorio
            />
        </form>
    );
};
```

## Validações Utilitárias

Funções de validação em `src/utils/validations.ts`:

```typescript
// Validação de CEP
export const validateCep = (cep: string): boolean => {
    if (!cep) return false;
    const numbers = cep.replace(/\D/g, '');
    return numbers.length === 8;
};

// Validação de processo
export const validateProcesso = (processo: string): boolean => {
    if (!processo) return false;
    const numbers = processo.replace(/\D/g, '');
    return numbers.length === 20;
};

// Validação de campo obrigatório
export const validateRequired = (value: any): boolean => {
    if (value === null || value === undefined) return false;
    if (typeof value === 'string') return value.trim().length > 0;
    if (typeof value === 'number') return !isNaN(value);
    if (Array.isArray(value)) return value.length > 0;
    return true;
};
```

## Validação de Datas

Validações de datas em `src/utils/dateValidations.ts`:

```typescript
export const validatePrazoFatal = (
    data: Date | string | null | undefined,
    prazoAceite: Date | string | null | undefined,
    prazoConclusao: Date | string | null | undefined
): ValidacaoResult => {
    if (!data) {
        return { valid: false, error: 'Prazo fatal é obrigatório' };
    }
    
    // Validações de data...
};
```

## Tipos de Validação

### Validação de Campos Obrigatórios

```typescript
const schema = yup.object().shape({
    nome: yup.string().required('Nome é obrigatório'),
});
```

### Validação de Formato

```typescript
const schema = yup.object().shape({
    email: yup.string().email('Digite um e-mail válido'),
    cep: yup.string().test('cep-validation', 'CEP inválido', function(value) {
        const numbers = value?.replace(/\D/g, '') || '';
        return numbers.length === 8;
    }),
});
```

### Validação de Valores Permitidos

```typescript
const schema = yup.object().shape({
    modalidade: yup.string().oneOf(['presencial', 'online'], 'Modalidade inválida'),
});
```

### Validação de Regras de Negócio

```typescript
const schema = yup.object().shape({
    prazo_aceite: yup.date()
        .min(new Date(), 'Prazo de aceite não pode ser no passado')
        .required('Prazo de aceite é obrigatório'),
});
```

## Formato de Erros

Erros de validação seguem formato do yup:

```typescript
{
    nome: { message: "Nome é obrigatório", type: "required" },
    email: { message: "Digite um e-mail válido", type: "email" }
}
```

## Exibição de Erros

Componentes de formulário exibem erros automaticamente:

```typescript
<InputDefault
    title="Nome"
    value={value}
    onChangeText={onChange}
    error={errors.nome?.message}
    obrigatorio
/>
```

## Boas Práticas Específicas

- **Validar antes de enviar**: Sempre validar dados antes de enviar para API
- **Mensagens claras**: Mensagens de erro devem ser compreensíveis e acionáveis
- **Validação em tempo real**: Usar validação em tempo real para melhor UX
- **Validação de permissões**: Verificar permissões do usuário antes de permitir operações
- **Validação de propriedade**: Verificar se usuário tem acesso ao recurso antes de permitir alterações
- **Validação de transições de status**: Validar se transições de status são permitidas
- **Validação de integridade**: Validar relacionamentos e integridade referencial
