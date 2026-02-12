# Padrões de Tratamento de Erros

## Boas Práticas

- **Mensagens Amigáveis ao Usuário**: Fornecer mensagens de erro claras e acionáveis aos usuários sem expor detalhes técnicos ou informações de segurança
- **Falhar Rápido e Explicitamente**: Validar entrada e verificar pré-condições cedo; falhar com mensagens de erro claras ao invés de permitir estado inválido
- **Tipos de Erro Específicos**: Usar objetos de erro específicos ao invés de genéricos para permitir tratamento direcionado
- **Tratamento de Erro Centralizado**: Tratar erros em limites apropriados (controllers) ao invés de espalhar blocos try-catch em todos os lugares
- **Degradação Graciosa**: Projetar sistemas para degradar graciosamente quando serviços não críticos falham ao invés de quebrar completamente
- **Estratégias de Retry**: Implementar exponential backoff para falhas transitórias em chamadas de serviços externos
- **Limpar Recursos**: Sempre limpar recursos (file handles, conexões) em blocos finally ou mecanismos equivalentes

## Padrão de Tratamento no 123jus-api

### Controllers

Todos os controllers devem usar try-catch para tratamento de erros:

```typescript
import ErrorReturn from '../../helpers/serviceDefault/errorReturn';
import SuccessReturn from '../../helpers/serviceDefault/successReturn';

class PropostaController {
    async create(req: Request, res: Response) {
        try {
            const data: IProposta = req.body;
            const result = await PropostaService.create(data, req);
            
            const retorno = SuccessReturn({
                result,
                message: "Proposta criada com sucesso"
            });
            
            res.status(retorno.code).json(retorno);
        } catch (e: any) {
            const retorno = ErrorReturn({
                message: e.message || "Ocorreu um erro",
                result: e.erros || []
            });
            return res.status(retorno.code).json(retorno);
        }
    }
}
```

### Validators

Validators lançam erros quando validação falha:

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
}
```

### Services

Services podem lançar erros que são capturados pelos controllers:

```typescript
class PropostaService {
    async create(data: IProposta, req: any): Promise<IProposta> {
        // Validação
        if (!data.id_comarca) {
            throw {
                message: "Comarca é obrigatória",
                erros: [{ field: "id_comarca", message: "Informe uma comarca válida" }]
            };
        }
        
        // Lógica de negócio
    }
}
```

## Formato de Erro

Erros são lançados como objetos com propriedades `message` e `erros`:

```typescript
throw {
    message: "Erro na validação",
    erros: [
        { field: "id_comarca", message: "Comarca não encontrada" },
        { field: "id_servico", message: "Serviço inválido" }
    ]
};
```

## Códigos de Status HTTP

- **200 OK**: Resposta padrão (sucesso ou erro com mensagem)
- **201 Created**: Recurso criado com sucesso (quando aplicável)
- **400 Bad Request**: Requisição malformada
- **401 Unauthorized**: Falha na autenticação
- **403 Forbidden**: Acesso negado
- **404 Not Found**: Recurso não encontrado
- **422 Unprocessable Entity**: Erros de validação ou regras de negócio
- **500 Internal Server Error**: Erros inesperados do servidor

**Nota**: O sistema atual usa principalmente código 200 para todas as respostas, diferenciando sucesso/erro através do campo `status` no JSON.

## Tratamento de Erros Assíncronos

Para operações assíncronas, sempre usar try-catch:

```typescript
async function processarArquivo(caminho: string) {
    try {
        const arquivo = await StorageService.obter(caminho);
        // Processar arquivo
    } catch (error) {
        console.error('Erro ao processar arquivo:', error);
        throw {
            message: "Erro ao processar arquivo",
            erros: [{ field: "arquivo", message: error.message }]
        };
    }
}
```

## Logging de Erros

Erros devem ser logados para monitoramento:

```typescript
catch (e: any) {
    console.error('Erro em PropostaController.create:', e);
    const retorno = ErrorReturn({
        message: e.message || "Ocorreu um erro",
        result: e.erros || []
    });
    return res.status(retorno.code).json(retorno);
}
```
