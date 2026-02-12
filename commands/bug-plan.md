# MASTER COMMAND: BUG PLAN (Orchestrator Mode)

**OBJETIVO:** Diagnosticar, reproduzir e planejar a correção de um bug no projeto correto.
**ENTRADA:** ID do Bug (`{{bug_id}}`) e Descrição/Evidências.

## ETAPA 0: ROTEAMENTO DE PROJETO (CRÍTICO)

Analise a Key do Jira e a descrição técnica para definir o **Caminho Físico**:

| Ecossistema | Key | Contexto Técnico | **Pasta Física Alvo** |
| :--- | :--- | :--- | :--- |
| **SaaS** | `EASYJUR` | Monolito, PHP Puro, Telas Legadas (sgr), Frontend JQuery | `../app` |
| **SaaS** | `EASYJUR` | API Nova, Laravel 11, Jobs, Filas, Integrações (Hubspot/Asaas) | `../fenix` |
| **SaaS** | `EASYJUR` | Arquivos, GED, Processamento em Massa, Storage | `../appged` |
| **LegalOps** | `OPS` | Backend, Node.js, Banco de Dados, Regras de Negócio | `../legal-ops-api` |
| **LegalOps** | `OPS` | Frontend, React, Telas, Componentes Visuais | `../legal-ops-ui` |

> **AÇÃO IMEDIATA:** Defina a variável `TARGET_DIR` com o caminho identificado acima.

---

## ETAPA 1: ESTRUTURAÇÃO (No `TARGET_DIR`)

1. Verifique se a pasta `[TARGET_DIR]/agent-os/bugs/{{bug_id}}/` existe. Se não, crie.
2. Garanta a existência dos arquivos vazios: `plan.md`, `fix.md`, `review.md`.
3. **Foco:** Preencher o `plan.md`.

---

## ETAPA 2: PROTOCOLO DE PLANEJAMENTO (Preencher `plan.md`)

Execute o seguinte workflow para preencher o arquivo de plano:

### 1. Contexto & Reprodução
* **Produto:** Qual sistema estamos tocando? (SaaS/Fênix/LegalOps).
* **Bug Reportado:** Resumo, Comportamento Esperado vs Atual.
* **Evidências:** Logs, Prints (se fornecidos).
* **Reprodução:** Você consegue identificar o erro lendo o código? Liste os passos teóricos.

### 2. Análise de Código (Busca Semântica)
* **Arquivos Suspeitos:** Liste os arquivos exatos (caminho relativo dentro do `TARGET_DIR`).
* **Funcionalidades Relacionadas:** Existe algo similar já implementado? (Ex: um Service no Fênix ou uma Function no APP).
* **Reutilização:** Podemos usar código existente? (Evitar duplicação é mandatório).

### 3. Consulta de Standards (Crucial)
Leia os standards centralizados em `easyjur-master-agent/ecosystems/[ECOSISTEMA]/standards/`.
* Liste quais regras específicas se aplicam a este bug (ex: Regra de Controller do Laravel, Regra de Hook do React).

### 4. Plano de Ação
* Liste passo a passo o que será alterado.
* **Análise de Impacto:** Quais outros módulos dependem desse arquivo?
* **Retrocompatibilidade:** Essa mudança quebra algo antigo?

---

## SAÍDA FINAL
* Pasta criada no diretório físico correto.
* `plan.md` preenchido com rigor técnico.
* **Pergunta:** "O plano está aprovado para iniciarmos a implementação?"