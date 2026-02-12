# Padrões de Escrita de Testes para 123jus-api

## Contexto do Projeto

O 123jus-api utiliza **Jest** para testes unitários e de integração e **Supertest** para testes de API. Como se trata de um sistema backend sem frontend, os testes são focados na validação das APIs e da lógica de negócio.

## Boas Práticas

- **Cobertura de API Completa**: Escrever testes para todos os endpoints da API, validando respostas, códigos de status HTTP e formatos de dados
- **Testes Unitários para Lógica de Negócio**: Implementar testes unitários para services e outras classes com lógica de negócio complexa
- **Testes de Integração**: Criar testes de integração que validem a interação entre componentes, incluindo banco de dados
- **Mocks e Stubs para Dependências Externas**: Utilizar mocks para simular serviços externos como AWS S3, evitando chamadas reais durante testes
- **Testar Cenários de Sucesso e Falha**: Incluir testes para validar comportamento em cenários de sucesso e falha (erros de validação, exceções, etc.)
- **Testes de Jobs e Cron**: Testar processamento agendado de jobs e cron
- **Testar Comportamento, Não Implementação**: Focar testes no que o código faz, não em como faz, para reduzir fragilidade
- **Nomes de Teste Claros**: Usar nomes descritivos que expliquem o que está sendo testado e o resultado esperado

## Estrutura de Testes

Os testes estão localizados em `src/test/` e seguem a estrutura:

```
src/test/
├── unitario/
│   ├── SolicitanteValidator.spec.ts
│   └── EscritorioSolicitanteValidator.spec.ts
```

## Exemplos de Estrutura de Testes

### Testes de Feature (API)

```typescript
import request from 'supertest';
import app from '../../app';

describe('POST /proposta', () => {
    it('deve criar uma proposta com sucesso', async () => {
        const token = 'valid-jwt-token';
        const data = {
            id_comarca: 1,
            id_servico: 1,
            id_operador: 1,
            status: 'Pendente'
        };
        
        const response = await request(app)
            .post('/proposta')
            .set('Authorization', `Bearer ${token}`)
            .send(data);
        
        expect(response.status).toBe(200);
        expect(response.body.status).toBe('success');
        expect(response.body.result).toHaveProperty('id_proposta');
    });
    
    it('deve retornar erro quando comarca não existe', async () => {
        const token = 'valid-jwt-token';
        const data = {
            id_comarca: 999,
            id_servico: 1,
            id_operador: 1
        };
        
        const response = await request(app)
            .post('/proposta')
            .set('Authorization', `Bearer ${token}`)
            .send(data);
        
        expect(response.status).toBe(200);
        expect(response.body.status).toBe('error');
        expect(response.body.result).toEqual(
            expect.arrayContaining([
                expect.objectContaining({
                    field: 'id_comarca',
                    message: expect.any(String)
                })
            ])
        );
    });
});
```

### Testes Unitários

```typescript
import PropostaValidator from '../../api/validators/PropostaValidator';
import IProposta from '../../types/IProposta';

describe('PropostaValidator', () => {
    describe('create', () => {
        it('deve validar proposta com dados válidos', async () => {
            const data: Partial<IProposta> = {
                id_comarca: 1,
                id_servico: 1,
                id_operador: 1,
                status: 'Pendente'
            };
            
            const result = await PropostaValidator.create(data);
            
            expect(result).toHaveProperty('id_comarca', 1);
            expect(result).toHaveProperty('id_servico', 1);
        });
        
        it('deve lançar erro quando comarca não é informada', async () => {
            const data: Partial<IProposta> = {
                id_servico: 1,
                id_operador: 1
            };
            
            await expect(PropostaValidator.create(data)).rejects.toThrow();
        });
    });
});
```

### Testes de Integração

```typescript
import { Connect } from '../../api/repositories/Connection';
import PropostaService from '../../api/services/PropostaService';

describe('PropostaService - Integração', () => {
    beforeEach(async () => {
        // Limpar dados de teste
        await Connect.table('proposta').where('id_operador', 999).delete();
    });
    
    afterEach(async () => {
        // Limpar dados de teste
        await Connect.table('proposta').where('id_operador', 999).delete();
    });
    
    it('deve criar proposta e salvar no banco', async () => {
        const data: IProposta = {
            id_comarca: 1,
            id_servico: 1,
            id_operador: 999,
            id_usuario: 1,
            status: 'Pendente',
            tipo: 'proposta',
            criado: new Date().toISOString()
        };
        
        const result = await PropostaService.create(data, { id_operador: 999 });
        
        const proposta = await Connect.table('proposta')
            .where('id_proposta', result.id_proposta)
            .first();
        
        expect(proposta).toBeDefined();
        expect(proposta.id_operador).toBe(999);
    });
});
```

## Configuração do Jest

Configuração em `jest.config.js`:

```javascript
module.exports = {
    preset: 'ts-jest',
    testEnvironment: 'node',
    roots: ['<rootDir>/src'],
    testMatch: ['**/__tests__/**/*.ts', '**/?(*.)+(spec|test).ts'],
    transform: {
        '^.+\\.ts$': 'ts-jest',
    },
    collectCoverageFrom: [
        'src/**/*.ts',
        '!src/**/*.d.ts',
    ],
};
```

## Mocks

### Mock de Services

```typescript
jest.mock('../../api/services/PropostaService', () => ({
    create: jest.fn(),
    getById: jest.fn(),
}));
```

### Mock de Repositories

```typescript
jest.mock('../../api/repositories/PropostaRepositories', () => ({
    insert: jest.fn(),
    getById: jest.fn(),
}));
```

### Mock de Storage

```typescript
jest.mock('../../api/services/StorageService', () => ({
    salvar: jest.fn().mockResolvedValue('caminho/arquivo.pdf'),
    obter: jest.fn().mockResolvedValue(stream),
    deletar: jest.fn().mockResolvedValue(undefined),
}));
```

## Testes de Autenticação

```typescript
describe('Autenticação', () => {
    it('deve rejeitar requisição sem token', async () => {
        const response = await request(app)
            .get('/proposta')
            .send();
        
        expect(response.status).toBe(200);
        expect(response.body.status).toBe('error');
        expect(response.body.message).toContain('não autorizado');
    });
    
    it('deve aceitar requisição com token válido', async () => {
        const token = 'valid-jwt-token';
        const response = await request(app)
            .get('/proposta')
            .set('Authorization', `Bearer ${token}`)
            .send();
        
        expect(response.status).toBe(200);
        expect(response.body.status).toBe('success');
    });
});
```

## Boas Práticas Adicionais

- **Isolamento**: Cada teste deve ser independente e não depender de outros testes
- **Setup e Teardown**: Usar `beforeEach` e `afterEach` para preparar e limpar dados de teste
- **Dados de Teste**: Criar dados de teste específicos para cada teste
- **Assertions Claras**: Usar assertions descritivas que expliquem o que está sendo testado
- **Cobertura**: Buscar alta cobertura de código, especialmente em lógica de negócio crítica
- **Performance**: Testes devem executar rapidamente, usar mocks quando necessário
