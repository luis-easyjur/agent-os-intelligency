# MASTER COMMAND: BUG REVIEW (Orchestrator Mode)

**OBJETIVO:** Revisar a correção aplicada para garantir qualidade e conformidade com o plano.
**NÃO GERE CÓDIGO NESTA ETAPA.** Apenas analise.

## ETAPA 0: CARREGAMENTO
1. Vá para o diretório do projeto: `[TARGET_DIR]/agent-os/bugs/{{bug_id}}/`.
2. Leia `plan.md` e `fix.md`.
3. Leia os arquivos de código que foram alterados na etapa de Fix.

---

## ETAPA 1: AUDITORIA DE QUALIDADE (Checklist)

Compare o código gerado com os **Standards do Ecossistema** (`easyjur-master-agent/ecosystems/...`):

1.  **Duplicação:** O código criou algo que já existia? (Erro comum).
2.  **Conformidade Arquitetural:**
    * Se `../app` (SaaS): Inseriu lógica de banco direto na View? (Proibido).
    * Se `../fenix`: Usou DTOs corretamente? Respeitou a injeção de dependência?
    * Se `../legal-ops-api`: Tratou erros com o padrão `ErrorReturn`?
3.  **Segurança:** Há riscos de SQL Injection ou falta de validação de input?
4.  **Plano vs Execução:** A correção seguiu o que foi planejado ou desviou?

---

## ETAPA 2: RELATÓRIO DE REVISÃO (`review.md`)

Atualize o arquivo `review.md` com a estrutura obrigatória:

### STATUS FINAL: [APROVADO] ou [REQUER AJUSTES]

### Resumo da Revisão
Parecer técnico sobre a solução.

### Pontos Bloqueadores (Se houver)
*Listar apenas se o Status for REQUER AJUSTES.*
- [ ] Arquivo X: Descrição do problema.
- [ ] Arquivo Y: Violação do Standard Z.

### Validações Realizadas
- [x] Seguiu o plano?
- [x] Sem código duplicado?
- [x] Retrocompatibilidade mantida?

---

## AÇÃO FINAL
* **Se APROVADO:** Informe que o bug está resolvido e documentado.
* **Se REQUER AJUSTES:** Solicite rodar `/bug-fix` novamente com as instruções de correção.