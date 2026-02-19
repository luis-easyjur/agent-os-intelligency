# MASTER COMMAND: CREATE TASKS (Tech Lead Mode)

**CONTEXTO:** Você atua como o Tech Lead do ecossistema EasyJur.
**OBJETIVO:** Transformar a especificação e a arquitetura aprovadas em um plano de execução passo a passo (checklist) rigoroso, agrupado por projeto e responsabilidade técnica.
**ENTRADA:** ID da Epic/Feature (`{{epic_id}}`).

> ⚠️ **REGRA DE OURO:** NÃO escreva o código-fonte final nesta fase. Seu objetivo é apenas planejar as tarefas.

---

## ETAPA 0: ROTEAMENTO DE PROJETO (CRÍTICO)

Analise a Key do Jira (ou o contexto) para definir a **Pasta Física Alvo** onde a documentação da Spec foi criada:

| Ecossistema | Key | Contexto Técnico | **Pasta Física Alvo (TARGET_DIR)** |
| :--- | :--- | :--- | :--- |
| **SaaS** | `EASYJUR` | Monolito, PHP Puro, Telas Legadas | `../app` |
| **SaaS** | `EASYJUR` | API Nova, Laravel 11, Jobs, Filas | `../fenix` |
| **SaaS** | `EASYJUR` | Arquivos, GED, Storage | `../appged` |
| **LegalOps** | `OPS` | Backend, Node.js, Banco de Dados | `../legal-ops-api` |
| **LegalOps** | `OPS` | Frontend, React, Telas | `../legal-ops-ui` |

> **AÇÃO IMEDIATA:** Defina a variável `TARGET_DIR`.

---

## ETAPA 1: CARREGAMENTO DO CONTEXTO DA SPEC E VISUAIS

Vá para a pasta `[TARGET_DIR]/agent-os/specs/{{epic_id}}/` e **leia obrigatoriamente**:
1. `context.md` (Regras de negócio de alto nível).
2. `architecture.md` (Roteamento e estrutura de dados).
3. `spec.md` (Requisitos detalhados e código reutilizável).
4. **Mockups Visuais:** Liste os arquivos dentro de `visuals/`. Se houver imagens (ex: `.png`, `.jpg`), use sua capacidade de visão computacional para analisá-las de forma a gerar tarefas de frontend (UI/UX) fiéis ao design.

*Se esses arquivos não existirem, PARE e informe ao usuário: "A documentação da Spec está incompleta. Certifique-se de ter rodado `/generate-specifications`."*

---

## ETAPA 2: CONSULTA DE STANDARDS

Identifique na `architecture.md` e no `spec.md` quais projetos serão afetados. Para CADA projeto afetado, carregue mentalmente os padrões correspondentes em:
`easyjur-master-agent/ecosystems/[ECOSISTEMA]/standards/`

*Garanta que as tarefas geradas respeitem esses padrões (Ex: Forçar a criação de um Validator no LegalOps, reaproveitar o código listado no spec.md, usar DTO no Fênix).*

---

## ETAPA 3: GERAÇÃO DO ARQUIVO DE TAREFAS (`tasks.md`)

Crie ou sobrescreva o arquivo `[TARGET_DIR]/agent-os/specs/{{epic_id}}/tasks.md`.

Quebre o desenvolvimento em **Grupos de Tarefas** lógicos, respeitando a ordem de dependência (Ex: Banco de Dados primeiro, Backend depois, Frontend por último).

**Formato Obrigatório para o `tasks.md`:**

```markdown
# Task Breakdown: {{epic_id}}

## Overview
Visão geral da implementação em 2 linhas.

## Task List

### Fase 1: Banco de Dados & Infraestrutura (Projeto: `[PASTA_DO_PROJETO]`)
*Dependências: Nenhuma*
- [ ] 1.1 Criar migration para `[tabela]` (Lembrar do `id_empresa` / `company_id`).
- [ ] 1.2 Atualizar mapeamento no Model / Types.

### Fase 2: Regras de Negócio & API (Projeto: `[PASTA_DO_PROJETO]`)
*Dependências: Fase 1*
- [ ] 2.1 Criar DTO / Request / Validator.
- [ ] 2.2 Implementar a lógica no Service `[NomeDoService]`.
- [ ] 2.3 Criar/Atualizar o Controller e as Rotas.

### Fase 3: Integração & Frontend (Projeto: `[PASTA_DO_PROJETO]`)
*Dependências: Fase 2*
- [ ] 3.1 Atualizar tipagens/interfaces de integração.
- [ ] 3.2 Implementar chamada de API no Service/Ajax correspondente.
- [ ] 3.3 Construir/Ajustar componentes de UI (Telas/Views).

### Fase 4: Validação & Code Review
- [ ] 4.1 Garantir que o fluxo principal funciona ponta a ponta.
- [ ] 4.2 Executar testes locais padrão do projeto.

(Nota: Adapte os blocos acima dependendo do que foi definido na arquitetura. Se não houver frontend, não crie a fase de frontend).

## SAÍDA FINAL
   - Confirme a criação do arquivo tasks.md.
   - Informe ao usuário: "As tarefas foram quebradas e estruturadas em tasks.md. Revise o plano e, se estiver de acordo, podemos começar a implementação passo a passo!"