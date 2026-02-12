# Padrões de Componentes de UI no 123jus-ui

## Visão Geral

O 123jus-ui possui uma biblioteca de componentes de UI reutilizáveis localizados em `src/components/Ui/` e `src/components/form/`. Estes componentes seguem padrões consistentes de design e comportamento.

## Componentes de Formulário

### InputDefault

Input padrão com suporte a máscaras e validação:

```typescript
import InputDefault from '../form/InputDefault';

<InputDefault
    title="Nome"
    value={value}
    onChangeText={(value) => setValue(value)}
    error={errors.nome?.message}
    obrigatorio
    mask=""
    type="text"
    disabled={false}
/>
```

**Props:**
- `title` - Título do campo
- `value` - Valor do campo
- `onChangeText` - Callback de mudança
- `error` - Mensagem de erro
- `obrigatorio` - Indica se campo é obrigatório
- `mask` - Máscara de input (ex: "99999-999" para CEP)
- `type` - Tipo do input (text, email, password, tel, number)
- `disabled` - Desabilita o campo
- `placeholder` - Placeholder do campo
- `search` - Indica se é campo de busca

### InputMoney

Input para valores monetários com formatação automática:

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

Checkbox customizado:

```typescript
import InputCheckbox from '../form/InputCheckbox';

<InputCheckbox
    title="Aceito os termos"
    checked={watch('aceito')}
    onChange={(checked) => setValue('aceito', checked)}
/>
```

### SelectEditavel

Select editável com busca:

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

Botão padrão com variantes:

```typescript
import ButtonDefault from '../form/ButtonDefault';

<ButtonDefault
    type="submit"
    className="access" // access | cancel | save | danger | continue
    loading={isSubmitting}
    disabled={!isValid}
>
    Salvar
</ButtonDefault>
```

**Variantes:**
- `access` - Botão primário (vermelho)
- `cancel` - Botão de cancelar (borda cinza)
- `save` - Botão de salvar (cinza)
- `danger` - Botão de perigo (vermelho escuro)
- `continue` - Botão de continuar (vermelho)

### DateTimePickerEditavel

Seletor de data/hora editável:

```typescript
import DateTimePickerEditavel from '../form/DateTimePickerEditavel';

<DateTimePickerEditavel
    title="Data"
    value={watch('data')}
    onChange={(value) => setValue('data', value)}
    error={errors.data?.message}
/>
```

## Componentes de UI

### NotificationCard

Card de notificação:

```typescript
import NotificationCard from '../Ui/NotificationCard';

<NotificationCard
    notification={notification}
    onClick={() => history.push(notification.action)}
/>
```

### DemandCard

Card de demanda:

```typescript
import DemandCard from '../Ui/DemandCard';

<DemandCard
    demanda={demanda}
    onAction={handleAction}
/>
```

### ProfessionalCard

Card de profissional:

```typescript
import ProfessionalCard from '../Ui/ProfessionalCard';

<ProfessionalCard
    profissional={profissional}
    onClick={() => history.push(`/profissionais/${profissional.id}`)}
/>
```

### OperatorCard

Card de operador:

```typescript
import OperatorCard from '../Ui/OperatorCard';

<OperatorCard
    operador={operador}
    onClick={() => history.push(`/operadores/${operador.id}`)}
/>
```

### GraficCard

Card de gráfico usando recharts:

```typescript
import GraficCard from '../Ui/GraficCard';

<GraficCard info={dadosGrafico} />
```

## Componentes de Listagem

### SkeletonListaDemandas

Skeleton loading para lista de demandas:

```typescript
import SkeletonListaDemandas from '../ListaDemandas/SkeletonListaDemandas';

{loading && <SkeletonListaDemandas />}
```

### PaginacaoListaDemandas

Paginação customizada:

```typescript
import PaginacaoListaDemandas from '../ListaDemandas/PaginacaoListaDemandas';

<PaginacaoListaDemandas
    paginacao={paginacao}
    carregaPagina={handlePageChange}
/>
```

## Componentes de Filtro

### DemandsFilter

Filtros de demandas:

```typescript
import DemandsFilter from '../Ui/DemandsFilter';

<DemandsFilter
    filtros={filtros}
    atualizaFiltros={setFiltros}
/>
```

### ProfessionalsFilter

Filtros de profissionais:

```typescript
import ProfessionalsFilter from '../Ui/ProfessionalsFilter';

<ProfessionalsFilter
    filtros={filtros}
    atualizaFiltros={setFiltros}
/>
```

## Boas Práticas

- **Reutilização**: Usar componentes de UI ao invés de criar novos componentes similares
- **Consistência**: Manter padrões visuais consistentes usando componentes existentes
- **Props Tipadas**: Sempre tipar props dos componentes
- **Estados de Loading**: Usar skeletons ou spinners apropriados
- **Feedback Visual**: Exibir erros e estados apropriados
- **Acessibilidade**: Incluir atributos ARIA quando necessário

## Exemplo Completo

```typescript
import { useState } from 'react';
import InputDefault from '../form/InputDefault';
import ButtonDefault from '../form/ButtonDefault';
import SkeletonListaDemandas from '../ListaDemandas/SkeletonListaDemandas';

const Component = () => {
    const [loading, setLoading] = useState(false);
    const [value, setValue] = useState('');

    if (loading) {
        return <SkeletonListaDemandas />;
    }

    return (
        <form>
            <InputDefault
                title="Nome"
                value={value}
                onChangeText={setValue}
                obrigatorio
            />
            <ButtonDefault
                type="submit"
                className="access"
                loading={loading}
            >
                Salvar
            </ButtonDefault>
        </form>
    );
};
```
