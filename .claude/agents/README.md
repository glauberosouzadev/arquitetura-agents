# Agentes e orquestração

O **orquestrador** é a sessão principal do Claude Code (não é um arquivo em `agents/`). Subagentes não acionam outros subagentes: toda delegação parte do orquestrador.

## Grupos

| Grupo | Agentes | Quando acionar |
|---|---|---|
| Liderança | `po`, `arquiteto`, `tech-lead` | **Só sob dúvida ou necessidade real** do orquestrador (requisito ambíguo, decisão arquitetural, conflito técnico). Não fazem parte do fluxo padrão de uma task. |
| Execução | `dev-*`, `ux-designer`, `qa`, `code-reviewer` | **Acionados diretamente pelo orquestrador**, sem intermediário. |
| Auditoria | `token-optimizer`, `context-engineer` | **Somente quando o usuário pedir.** Nunca durante uma task. Sempre em par. |

## Regras

1. **Sem intermediários:** o orquestrador delega a task ao agente executor indicado em "Agente responsável", passando o arquivo da task, as seções relevantes e o escopo. O `tech-lead` não repassa trabalho a executores (isso duplica contexto e custo).
2. **Liderança sob demanda:** ao acionar PO/arquiteto/tech-lead, enviar a pergunta específica e os trechos relevantes, não o projeto inteiro. A resposta que mudar decisão vira ADR (`docs/adr/`).
3. **Fluxo de task** (usado por `scripts/07-exec_tasks.json`): entender → implementar (executor) → `code-reviewer` → corrigir → `qa`/testes → documentar e fechar.
4. **Retornos curtos:** cada agente devolve o formato de entrega do seu arquivo, com limite de palavras.

## Auditoria em par

`token-optimizer` (custo) e `context-engineer` (estrutura de contexto) têm visões complementares e não conversam diretamente. O orquestrador faz a ponte:

1. Aciona os dois **em paralelo** sobre o mesmo escopo.
2. Lê as seções "Pontos para o outro" e "Divergências" de cada relatório.
3. Se houver divergência ou dependência, aciona **uma** segunda rodada, enviando a cada agente o relatório do outro (resumido).
4. Entrega ao usuário **uma única proposta consolidada**, sem aplicar nada. Mudanças só após aprovação; as que tocam `docs/` ou `scripts/` seguem as regras do `CLAUDE.md`.
