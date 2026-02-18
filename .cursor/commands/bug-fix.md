# MASTER COMMAND: BUG FIX (Orchestrator Mode)

> **⚠️ INSTRUÇÃO DE SISTEMA:**
> 1. Analise a Key do JIRA (ex: `EASYJUR` ou `OPS`).
> 2. Identifique o projeto físico na tabela do `.cursorrules` (ex: `../app`, `../fenix`).
> 3. **TODA** a execução deste prompt deve acontecer considerando esse diretório alvo como a raiz.
> 4. Crie a pasta `agent-os/bugs/{{bug_id}}` DENTRO desse diretório alvo.

---

**OBJETIVO:** Aplicar a correção seguindo rigorosamente o plano, alterando o mínimo de código possível.
**PRÉ-REQUISITOS:** `plan.md` aprovado na pasta do bug dentro do projeto alvo.

## ETAPA 0: CONTEXTO
1. Localize o projeto alvo (ex: `../fenix`, `../app`) baseado no ID do bug.
2. Leia: `[TARGET_DIR]/agent-os/bugs/{{bug_id}}/plan.md`.
3. Leia: `[TARGET_DIR]/agent-os/bugs/{{bug_id}}/review.md` (se houver feedbacks de review anteriores).

---

## ETAPA 1: VALIDAÇÃO PRÉ-IMPLEMENTAÇÃO

Antes de gerar qualquer código, verifique mentalmente:
1.  **Reutilização:** Estou criando uma função que já existe em `helpers/`, `Services/` ou `utils/`? Se sim, PARE e reutilize.
2.  **Standards:** Estou seguindo as regras do `easyjur-master-agent/ecosystems/[ECOSISTEMA]/standards`?
    * *SaaS (APP):* Use `QueryBuilder`/`functions.php`. Não use Eloquent. Respeite o legado.
    * *Fênix:* Use DTOs, Services e Repositories.
    * *LegalOps:* Use Validators e Services.

---

## ETAPA 2: IMPLEMENTAÇÃO
1.  Edite os arquivos listados no `plan.md`.
2.  **Não refatore** código fora do escopo do bug.
3.  **Não quebre** retrocompatibilidade sem justificativa extrema.
4.  Mantenha comentários em Inglês (ou Português se o legado exigir).

---

## ETAPA 3: DOCUMENTAÇÃO (`fix.md`)

Atualize o arquivo `[TARGET_DIR]/agent-os/bugs/{{bug_id}}/fix.md` com:

* **Arquivos Modificados:** Lista exata.
* **Resumo da Correção:** O que foi feito tecnicamente.
* **Código Reutilizado:** O que você aproveitou do sistema.
* **Standards Aplicados:** Quais regras guiaram a correção.
* **Análise de Impacto Realizada:** O que foi verificado para garantir que nada mais quebrou.

---

## SAÍDA FINAL
* Código aplicado.
* `fix.md` atualizado.
* **Próximo Passo:** Solicite a execução do `/bug-review`.