# Padrões de Validação

## Boas Práticas

- **Validar no Servidor**: Sempre validar no servidor; nunca confiar apenas em validação do lado do cliente para segurança ou integridade de dados
- **Lado do Cliente para UX**: Usar validação do lado do cliente para fornecer feedback imediato ao usuário, mas duplicar verificações no servidor
- **Falhar Cedo**: Validar entrada o mais cedo possível e rejeitar dados inválidos antes do processamento
- **Mensagens de Erro Específicas**: Fornecer mensagens de erro claras e específicas por campo que ajudem usuários a corrigir sua entrada
- **Allowlists ao Invés de Blocklists**: Quando possível, definir o que é permitido ao invés de tentar bloquear tudo que não é
- **Validação de Tipo e Formato**: Verificar tipos de dados, formatos, intervalos e campos obrigatórios sistematicamente
- **Sanitizar Entrada**: Sanitizar entrada do usuário para prevenir ataques de injeção (SQL, XSS, command injection)
- **Validação de Regras de Negócio**: Validar regras de negócio (ex: status válido, datas válidas) na camada apropriada da aplicação
- **Validação Consistente**: Aplicar validação consistentemente em todos os pontos de entrada (endpoints de API, jobs em background)

## Validators no 123jus-api

### Estrutura de Validators

Validators são classes TypeScript localizadas em `src/api/validators/`:

```typescript
class PropostaValidator {
    setError(erros: Array<any>) {
        if (erros.length > 0) {
            throw {
                message: erros.length + " Erros encontrados",
                erros
            };
        }
    }
    
    async create(data: Partial<IProposta>): Promise<Partial<IProposta>> {
        const erros: Array<any> = [];
        
        // Validações
        if (!data.id_comarca || isNaN(data.id_comarca)) {
            erros.push({ field: "id_comarca", message: "Informe uma comarca válida" });
        }
        
        if (!data.id_servico || isNaN(data.id_servico)) {
            erros.push({ field: "id_servico", message: "Informe um serviço válido" });
        }
        
        // Mais validações...
        
        this.setError(erros);
        return data;
    }
}
```

### Uso em Controllers

Validators são chamados antes de processar requisições:

```typescript
class PropostaController {
    async create(req: Request, res: Response) {
        try {
            const data: IProposta = req.body;
            const dataValidada = await PropostaValidator.create(data);
            const result = await PropostaService.create(dataValidada, req);
            // ...
        } catch (e: any) {
            // Tratamento de erro
        }
    }
}
```

## Tipos de Validação

### Validação de Campos Obrigatórios

```typescript
if (!data.id_comarca || isNaN(data.id_comarca)) {
    erros.push({ field: "id_comarca", message: "Informe uma comarca válida" });
}
```

### Validação de Tipos

```typescript
if (typeof data.valor_cliente !== 'number' || isNaN(data.valor_cliente)) {
    erros.push({ field: "valor_cliente", message: "Informe o valor do cliente" });
}
```

### Validação de Valores Permitidos

```typescript
if (data.modalidade !== "presencial" && data.modalidade !== "online") {
    erros.push({ field: "modalidade", message: "Modalidade deve ser online ou presencial" });
}
```

### Validação de Regras de Negócio

```typescript
if (proposta.status !== 'Em Triagem') {
    erros.push({ field: "status", message: "Apenas propostas em triagem podem ser aprovadas" });
}
```

### Validação de Existência no Banco

```typescript
const comarca = await ComarcaRepositories.get({ filtros: { id_comarca: data.id_comarca } });
if (comarca.length == 0) {
    erros.push({ field: "id_comarca", message: "Comarca não encontrada" });
}
```

### Validação de Datas

```typescript
const agora = moment().format('YYYY-MM-DD HH:mm:ss');
if (agora > data.prazo_aceite || !data.prazo_aceite) {
    erros.push({ field: "prazo_aceite", message: "Informe um prazo correto para o aceite" });
}
```

## ValidatorDefault

Classe base com validações comuns:

```typescript
import ValidatorDefault from './ValidatorDefault';

await ValidatorDefault.dataPassada(data.prazo_aceite, "Prazo de aceite");
```

## Formato de Erros

Erros de validação seguem formato padronizado:

```typescript
{
    message: "2 Erros encontrados",
    erros: [
        { field: "id_comarca", message: "Comarca não encontrada" },
        { field: "id_servico", message: "Serviço inválido" }
    ]
}
```

## Boas Práticas Específicas

- **Validar antes de processar**: Sempre validar dados antes de passar para services
- **Mensagens claras**: Mensagens de erro devem ser compreensíveis e acionáveis
- **Validação de permissões**: Validar permissões do usuário antes de permitir operações
- **Validação de propriedade**: Verificar se usuário tem acesso ao recurso antes de permitir alterações
- **Validação de transições de status**: Validar se transições de status são permitidas
- **Validação de integridade**: Validar relacionamentos e integridade referencial
