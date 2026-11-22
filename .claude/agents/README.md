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

## Perfil de modelo

Critério: custo de um erro × frequência de uso. Quem decide pouco e com alto impacto usa o modelo mais forte; quem roda muito sobre especificação pronta usa o intermediário. Todo agente declara `model` no frontmatter; nunca `inherit`.

| Agente | model | Motivo |
|---|---|---|
| `po`, `arquiteto`, `tech-lead` | opus | Raros (só sob dúvida); erro contamina todas as tasks seguintes. |
| `code-reviewer` | opus | Roda em toda task, mas recebe só diff e restrições; evita os mesmos pontos cegos do dev. |
| `dev-*` | sonnet | A task chega com trechos, escopo e critérios prontos: é execução. |
| `qa` | sonnet | Testes contra critérios explícitos. Não usar haiku: teste frouxo dá falsa confiança. |
| `ux-designer` | sonnet | Sem decisão estrutural. |
| `context-engineer`, `token-optimizer` | opus | Raros, a pedido do usuário; achar contradições e padrões exige raciocínio forte. |

`haiku` só para tarefa puramente mecânica; hoje nenhum agente o usa.

**Escalada de dev:** se a task tiver `Complexidade: alta` (concorrência, segurança, migração de dados, refatoração entre módulos), o orquestrador aciona o dev com `model: opus` por override. Padrão: sonnet.

## Pacote de contexto (orquestrador → executor)

O planejamento (`06`) extrai os trechos dos documentos **uma vez** e os grava na task; a execução (`07`) só valida. O executor recebe:

- caminho da task, requisitos (IDs), trechos normativos (citação literal com origem `doc§ID`, nunca paráfrase de regra, contrato ou schema), restrições invioláveis, escopo (dentro/fora, arquivos-alvo), dependências entregues, ADRs citados e comandos do módulo;
- **camadas:** L0 núcleo (restrições invioláveis, escopo, critérios de aceite, comandos; ≤1,5k; vai a todos); L1 normativo literal filtrado pela camada da task (alvo 6k, teto 10k); L2 anexos volumosos por referência `doc§ID` + cabeçalho exato, lidos só por quem os produz.
- **literal ou referência:** vai literal em L1 o que, se violado, passa silencioso (códigos de erro, autorização do papel tocado, decisões que divergem de outro documento), a interface consumida pela camada da task e os estados exigidos. Vai como L2 o conteúdo a transcrever (copy/i18n, DDL, tokens visuais, fixtures) e o que pertence a outra camada. Sem ID ou cabeçalho estável, o trecho vira literal.
- **omissão declarada:** trecho que não se aplica vira `[omitido: motivo; doc§ID]`, nunca corte silencioso. Copiar tabelas sem espaço de alinhamento; medir tamanho sem ele.
- **por destinatário:** cada bloco do L1 é marcado no planejamento com `[dev]`, `[qa]` e/ou `[review]`; o orquestrador monta o pacote por filtro mecânico, sem julgar relevância. `qa` recebe L0 + blocos `[qa]` (≤2,5k); `code-reviewer` recebe L0 + blocos `[review]` e o diff (≤3k). Nenhum dos dois recebe L2.
- **restrições invioláveis** nunca são cortadas. Os valores acima são padrões iniciais, parametrizáveis em `context_budget` (`scripts/06-epic_tasks.json`); acima do teto, dividir a task ou justificar no campo Orçamento, e revisar os valores com medições reais do projeto.

O executor pode ler além do pacote: o código dentro do escopo, os ADRs citados e `Grep` em `docs/` pelos IDs citados. O executor lê os anexos L2 marcados como obrigatórios e lista no retorno os que leu. Se faltar regra ou o código contradisser um trecho, devolve `BLOQUEIO: <pergunta>` e para. Antes de delegar, o orquestrador confirma por `Grep` que os trechos citados não mudaram.

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
