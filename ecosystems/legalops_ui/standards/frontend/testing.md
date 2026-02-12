# Padrões de Testes no 123jus-ui

## Visão Geral

O projeto possui dependências de teste instaladas, mas ainda não possui uma suíte completa de testes implementada. As bibliotecas disponíveis são:

- **@testing-library/react** v13.1.1 - Testes de componentes React
- **@testing-library/jest-dom** v5.16.4 - Matchers customizados para Jest
- **@testing-library/user-event** v13.5.0 - Simulação de eventos de usuário
- **@types/jest** v27.4.1 - Tipos TypeScript para Jest

## Estrutura de Testes

### Configuração

O projeto utiliza Create React App, que já vem configurado com Jest e React Testing Library. A configuração padrão do CRA é suficiente para começar a escrever testes.

### Localização de Testes

Testes devem ser colocados próximos aos componentes que testam:

```
src/
  components/
    Button/
      Button.tsx
      Button.test.tsx
  pages/
    Dashboard/
      index.tsx
      index.test.tsx
```

Ou em uma pasta `__tests__`:

```
src/
  components/
    Button/
      Button.tsx
      __tests__/
        Button.test.tsx
```

## Padrões de Teste

### Teste de Componente Simples

```typescript
import { render, screen } from '@testing-library/react';
import ButtonDefault from '../form/ButtonDefault';

describe('ButtonDefault', () => {
    it('deve renderizar o texto do botão', () => {
        render(<ButtonDefault>Clique aqui</ButtonDefault>);
        expect(screen.getByText('Clique aqui')).toBeInTheDocument();
    });

    it('deve estar desabilitado quando disabled é true', () => {
        render(<ButtonDefault disabled>Botão</ButtonDefault>);
        expect(screen.getByText('Botão')).toBeDisabled();
    });
});
```

### Teste com Hooks

```typescript
import { render, screen, waitFor } from '@testing-library/react';
import { UserContext } from '../contexts/UserContext';
import Component from './Component';

const mockUser = {
    id: '1',
    nome: 'Teste',
    email: 'teste@teste.com',
    profile: 'admin'
};

describe('Component', () => {
    it('deve renderizar dados do usuário', async () => {
        render(
            <UserContext.Provider value={{ user: mockUser }}>
                <Component />
            </UserContext.Provider>
        );

        await waitFor(() => {
            expect(screen.getByText('Teste')).toBeInTheDocument();
        });
    });
});
```

### Teste com React Router

```typescript
import { render, screen } from '@testing-library/react';
import { BrowserRouter } from 'react-router-dom';
import { useHistory } from 'react-router-dom';
import Component from './Component';

describe('Component', () => {
    it('deve navegar corretamente', () => {
        render(
            <BrowserRouter>
                <Component />
            </BrowserRouter>
        );

        // Testar navegação
    });
});
```

### Teste de Interação do Usuário

```typescript
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import InputDefault from '../form/InputDefault';

describe('InputDefault', () => {
    it('deve atualizar valor quando usuário digita', async () => {
        const user = userEvent.setup();
        const handleChange = jest.fn();

        render(
            <InputDefault
                value=""
                onChangeText={handleChange}
                title="Nome"
            />
        );

        const input = screen.getByLabelText('Nome');
        await user.type(input, 'Teste');

        expect(handleChange).toHaveBeenCalled();
    });
});
```

### Teste de Chamadas de API

```typescript
import { render, screen, waitFor } from '@testing-library/react';
import api from '../services/api';
import Component from './Component';

jest.mock('../services/api');

describe('Component', () => {
    it('deve carregar dados da API', async () => {
        const mockData = [{ id: 1, nome: 'Teste' }];
        (api.get as jest.Mock).mockResolvedValue({
            data: {
                status: 'success',
                result: mockData
            }
        });

        render(<Component />);

        await waitFor(() => {
            expect(screen.getByText('Teste')).toBeInTheDocument();
        });
    });
});
```

### Teste de Formulários

```typescript
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { useForm } from 'react-hook-form';
import FormComponent from './FormComponent';

describe('FormComponent', () => {
    it('deve validar campos obrigatórios', async () => {
        const user = userEvent.setup();
        render(<FormComponent />);

        const submitButton = screen.getByRole('button', { name: /salvar/i });
        await user.click(submitButton);

        await waitFor(() => {
            expect(screen.getByText(/nome é obrigatório/i)).toBeInTheDocument();
        });
    });
});
```

## Matchers do Jest DOM

O projeto utiliza `@testing-library/jest-dom` que fornece matchers adicionais:

```typescript
expect(element).toBeInTheDocument();
expect(element).toBeVisible();
expect(element).toBeDisabled();
expect(element).toBeEnabled();
expect(element).toHaveClass('className');
expect(element).toHaveTextContent('text');
expect(element).toHaveValue('value');
expect(element).toBeChecked();
expect(element).toBeRequired();
```

## Boas Práticas

- **Testar Comportamento**: Testar o que o componente faz, não como faz
- **Acessibilidade**: Usar queries acessíveis (`getByRole`, `getByLabelText`)
- **Evitar Testes Frágeis**: Não testar detalhes de implementação
- **Cobertura Razoável**: Focar em funcionalidades críticas
- **Mock de Dependências**: Mockar chamadas de API e dependências externas
- **Cleanup**: Limpar mocks entre testes
- **Nomes Descritivos**: Usar nomes de teste que descrevem o comportamento esperado

## Estrutura de Teste Recomendada

```typescript
describe('ComponentName', () => {
    beforeEach(() => {
        // Setup comum
    });

    afterEach(() => {
        // Cleanup
    });

    describe('quando [condição]', () => {
        it('deve [comportamento esperado]', () => {
            // Arrange
            // Act
            // Assert
        });
    });
});
```

## Exemplo Completo

```typescript
import { render, screen, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { BrowserRouter } from 'react-router-dom';
import { UserContext } from '../contexts/UserContext';
import api from '../services/api';
import Component from './Component';

jest.mock('../services/api');

const mockUser = {
    id: '1',
    nome: 'Teste',
    email: 'teste@teste.com',
    profile: 'admin'
};

describe('Component', () => {
    beforeEach(() => {
        jest.clearAllMocks();
    });

    it('deve renderizar dados do usuário', () => {
        render(
            <BrowserRouter>
                <UserContext.Provider value={{ user: mockUser }}>
                    <Component />
                </UserContext.Provider>
            </BrowserRouter>
        );

        expect(screen.getByText('Teste')).toBeInTheDocument();
    });

    it('deve carregar dados da API ao montar', async () => {
        const mockData = [{ id: 1, nome: 'Item 1' }];
        (api.get as jest.Mock).mockResolvedValue({
            data: {
                status: 'success',
                result: mockData
            }
        });

        render(
            <BrowserRouter>
                <UserContext.Provider value={{ user: mockUser }}>
                    <Component />
                </UserContext.Provider>
            </BrowserRouter>
        );

        await waitFor(() => {
            expect(screen.getByText('Item 1')).toBeInTheDocument();
        });

        expect(api.get).toHaveBeenCalledWith('/endpoint');
    });

    it('deve executar ação ao clicar no botão', async () => {
        const user = userEvent.setup();
        const handleAction = jest.fn();

        render(
            <BrowserRouter>
                <UserContext.Provider value={{ user: mockUser }}>
                    <Component onAction={handleAction} />
                </UserContext.Provider>
            </BrowserRouter>
        );

        const button = screen.getByRole('button', { name: /ação/i });
        await user.click(button);

        expect(handleAction).toHaveBeenCalledTimes(1);
    });
});
```

## Nota

Embora o projeto tenha as dependências de teste instaladas, ainda não há uma suíte completa de testes implementada. Este documento serve como guia para quando os testes forem implementados no projeto.
