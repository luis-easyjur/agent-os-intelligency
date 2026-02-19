# MASTER COMMAND: GENERATE SPECIFICATIONS (Analyst Mode)

**CONTEXTO:** Você atua como um Analista de Sistemas e Tech Writer detalhista.
**OBJETIVO:** Transformar as definições de alto nível (`context.md` e `architecture.md`) em uma Especificação Técnica detalhada (`spec.md`), buscando código reutilizável e preparando a estrutura para mockups visuais.
**ENTRADA:** ID da Epic/Feature (`{{epic_id}}`).

> ⚠️ **REGRA DE OURO:** NÃO escreva código real na spec. Mantenha as seções curtas, diretas e escaneáveis.

---

## ETAPA 0: ROTEAMENTO DE PROJETO (CRÍTICO)

Analise a Key do Jira (ou o contexto) para definir a **Pasta Física Alvo**:

| Ecossistema | Key | Contexto Técnico | **Pasta Física Alvo (TARGET_DIR)** |
| :--- | :--- | :--- | :--- |
| **SaaS** | `EASYJUR` | Monolito, PHP Puro, Telas Legadas | `../app` |
| **SaaS** | `EASYJUR` | API Nova, Laravel 11, Jobs, Filas | `../fenix` |
| **SaaS** | `EASYJUR` | Arquivos, GED, Storage | `../appged` |
| **LegalOps** | `OPS` | Backend, Node.js, Banco de Dados | `../legal-ops-api` |
| **LegalOps** | `OPS` | Frontend, React, Telas | `../legal-ops-ui` |

> **AÇÃO IMEDIATA:** Defina a variável `TARGET_DIR`.

---

## ETAPA 1: CARREGAMENTO DE CONTEXTO E CRIAÇÃO DA ESTRUTURA VISUAL

1. Vá para a pasta: `[TARGET_DIR]/agent-os/specs/{{epic_id}}/`.
2. **Leia obrigatoriamente:** `context.md` e `architecture.md`.
3. **Crie a pasta de Mockups:** Crie a pasta `[TARGET_DIR]/agent-os/specs/{{epic_id}}/visuals/`.

---

## ETAPA 2: BUSCA DE CÓDIGO REUTILIZÁVEL (Scout)

Antes de escrever a especificação, faça uma busca mental/semântica no repositório (`TARGET_DIR`) por padrões existentes que podem ser reaproveitados para esta feature:
- Componentes de UI similares.
- Models, Services ou Controllers com lógica relacionada.
- Padrões de API existentes.
Documente essas descobertas para incluir no arquivo final.

---

## ETAPA 3: GERAÇÃO DO ARQUIVO DE ESPECIFICAÇÃO (`spec.md`)

Crie o arquivo `[TARGET_DIR]/agent-os/specs/{{epic_id}}/spec.md` seguindo **ESTRITAMENTE** este formato:

```markdown
# Specification: {{epic_id}}

## Goal
[1 a 2 frases descrevendo o objetivo central baseado no context.md]

## User Stories
- Como um [tipo de usuário], eu quero [ação] para que [benefício]
- [Repetir para até 3 user stories principais]

## Specific Requirements
**[Nome do Requisito Específico]**
- [Até 8 sub-bullet points concisos para esclarecer sub-requisitos, regras de validação ou abordagem técnica de implementação]

## Visual Design
*(Se houver mockups na pasta `visuals/`, detalhar o comportamento de UI aqui)*
**`visuals/[nome-do-arquivo.png]`**
- [Até 8 bullets concisos descrevendo elementos de UI específicos para o desenvolvedor frontend]

## Existing Code to Leverage
**[Código, componente ou lógica existente encontrada na Etapa 2]**
- [Explique o que o código faz e como ele deve ser reutilizado ou estendido]

## Out of Scope
- [O que NÃO será feito nesta feature, baseado na arquitetura aprovada]

---

## SAÍDA FINAL

 - Confirme a criação do spec.md e da pasta visuals/.
 - Informe ao usuário: "Especificação gerada e pasta visuals/ criada. Se você tiver mockups ou prints de tela, coloque-os na pasta visuals/ agora. Após revisar, execute o comando de geração de tarefas (/create-tasks {{epic_id}})."