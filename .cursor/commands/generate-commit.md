# MASTER COMMAND: GENERATE COMMIT (Semantic Mode)

**CONTEXTO:** Você é um Engenheiro de Software Sênior, pragmático e focado na qualidade do histórico do projeto.
**OBJETIVO:** Gerar uma mensagem de commit semântico rigorosa baseada no diff ou descrição fornecida.

## ETAPA 0: ROTEAMENTO E CONTEXTO
1. Identifique a Key do Jira (ex: `EASYJUR-123`, `OPS-456`) fornecida ou implícita.
2. Identifique o **PROJETO ALVO** na tabela de roteamento (ex: `../saas`, `../fenix`).
3. **Analise as mudanças:** Verifique as alterações pendentes (git diff) dentro do diretório do projeto alvo.

---

## ETAPA 1: ANÁLISE TÉCNICA (O "O Que" e o "Como")
Analise os inputs na seguinte ordem de prioridade:
1.  **Prioridade Máxima:** O `DIFF_DO_CÓDIGO` (A verdade absoluta do que mudou).
2.  **Prioridade Média:** A descrição fornecida pelo usuário ou o conteúdo de `fix.md`.

**Decisão do Tipo:** Escolha estritamente um destes:
- `feat` (Nova funcionalidade)
- `fix` (Correção de bug)
- `docs` (Documentação)
- `style` (Formatação, espaços)
- `refactor` (Nem bug, nem feature)
- `test` (Testes)
- `chore` (Build, configs)

---

## ETAPA 2: DETERMINAÇÃO DE COMPLEXIDADE
- **Simples:** (Typo, CSS, ajuste pequeno) -> Corpo conciso (2-3 linhas).
- **Média:** (Feature, refactor pequeno) -> Corpo detalhado (4-6 linhas). Focar na abordagem técnica.
- **Complexa:** (Arquitetura, módulo novo) -> Corpo extenso e estruturado (listas, contexto, impacto).

---

## ETAPA 3: GERAÇÃO DA MENSAGEM

Monte o commit seguindo estritamente este formato:

**Header:**
`[TICKET_JIRA] <tipo>: <descrição curta>`
- `<descrição curta>`: Minúsculas, imperativo ("adiciona", não "adicionado"), máx 50 chars, sem ponto final.

**Body:**
- Deixe uma linha em branco após o header.
- Explique o **PORQUÊ** e o **COMO**.
- Use listas (`-`) para clareza.

---

## SAÍDA ESPERADA

Apenas o bloco de código com a mensagem final, pronto para ser copiado.

Exemplo de Saída:
```text
[EASYJUR-123] fix: corrige erro de validação no cadastro

- Ajusta a regex de validação de email no LoginController para aceitar novos domínios
- Atualiza os testes unitários correspondentes
- Remove verificação legada que causava falsos negativos