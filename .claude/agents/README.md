# Agentes e orquestração

O **orquestrador** é a sessão principal do Claude Code (não é um arquivo em `agents/`). Subagentes não acionam outros subagentes: toda delegação parte do orquestrador.

## Grupos

| Grupo | Agentes | Quando acionar |
|---|---|---|
| Liderança | `po`, `arquiteto`, `tech-lead` | **Só sob dúvida ou necessidade real** do orquestrador (requisito ambíguo, decisão arquitetural, conflito técnico). Não fazem parte do fluxo padrão de uma task. |
| Execução | `dev-*`, `ux-designer`, `qa`, `code-reviewer` | **Acionados diretamente pelo orquestrador**, sem intermediário. |
| Auditoria | `token-optimizer`, `context-engineer` | **Somente quando o usuário pedir.** Nunca durante uma task. Sempre em par. |

## Regras

1. **Sem intermediários:** o orquestrador delega a task ao agente executor indicado em "Agente responsável". O `tech-lead` não repassa trabalho a executores.
2. **Liderança sob demanda:** enviar a pergunta específica e os trechos relevantes, não o projeto inteiro. Resposta que mudar decisão vira ADR (`docs/adr/`).
3. **Fluxo de task** (`scripts/07-exec_tasks.json`): entender → implementar (executor) → `qa` → `code-reviewer` → corrigir → reverificar o escopo alterado → documentar e fechar.
4. **Retornos curtos:** executor, `qa` e `code-reviewer` devolvem até 120 palavras; reviewer lista bloqueantes primeiro. Logs nunca entram no retorno.
5. **Skill `napkin`:** só na sessão principal; subagentes não a leem nem a atualizam.

## Pacote de contexto (orquestrador → executor)

O planejamento (`06`) extrai os trechos dos documentos **uma vez** e os grava na task; a execução (`07`) só valida. O executor recebe:

- caminho da task, requisitos (IDs), trechos normativos (citação literal com origem `doc§ID`, nunca paráfrase de regra, contrato ou schema), restrições invioláveis, escopo (dentro/fora, arquivos-alvo), dependências entregues, ADRs citados e comandos do módulo;
- tamanho: até ~2,5k tokens (teto 4k). Restrições invioláveis nunca são cortadas para caber.

O executor pode ler além do pacote: o código dentro do escopo, os ADRs citados e `Grep` em `docs/` pelos IDs citados. Se faltar regra ou o código contradisser um trecho, devolve `BLOQUEIO: <pergunta>` e para. Antes de delegar, o orquestrador confirma por `Grep` que os trechos citados não mudaram.

## Paralelismo

Tasks só rodam em paralelo se **todas** as condições valerem: sem dependência entre si, "Conflita com" vazio e arquivos-alvo disjuntos. Qualquer vínculo (mesmo arquivo, módulo ou recurso compartilhado como banco, porta ou configuração) obriga execução **sequencial** na ordem de dependência. Isolamento por worktree não faz parte do modelo; se um projeto precisar, decidir por ADR.

## Testes: dono único

| Papel | Executa | Não executa |
|---|---|---|
| Dev | testes, lint e typecheck só do módulo, uma vez, antes de devolver | a suíte completa |
| `qa` | a suíte completa, uma vez por onda de tasks paralelas, depois que todas terminarem | o escopo já coberto por evidência com fingerprint idêntico |
| `code-reviewer` | nada; lê o diff e as evidências | testes |
| Orquestrador | nunca roda testes; confere o bloco Evidências | testes |

Execução em modo silencioso: só resultado e falhas (nome e primeira linha, máx. 30 linhas) entram no contexto.

**Bloco `## Evidências` na task** (vai com ela para `tasks/done/`):

```text
- cmd: <comando exato>
- escopo: módulo | suíte
- fingerprint: HEAD=<sha> diff=<sha256 de git diff HEAD nos arquivos do escopo>
- resultado: pass | fail (N testes, M falhas)
- falhas: <nome e primeira linha de cada uma>
```

Se o fingerprint mudar (ex.: correção após o review), reexecutar apenas o escopo alterado.

## Auditoria em par

`token-optimizer` (custo) e `context-engineer` (estrutura de contexto) têm visões complementares e não conversam diretamente. O orquestrador faz a ponte:

1. Aciona os dois **em paralelo** sobre o mesmo escopo.
2. Lê as seções "Pontos para o outro" e "Divergências" de cada relatório.
3. Se houver divergência ou dependência, aciona **uma** segunda rodada, enviando a cada agente o relatório do outro (resumido).
4. Entrega ao usuário **uma única proposta consolidada**, sem aplicar nada. Mudanças só após aprovação; as que tocam `docs/` ou `scripts/` seguem as regras do `CLAUDE.md`.
