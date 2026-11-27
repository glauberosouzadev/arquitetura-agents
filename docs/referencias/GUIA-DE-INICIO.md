# Guia de início de projeto

Passo a passo para sair deste modelo vazio e chegar à execução de tasks. Siga as fases **na ordem**: cada uma termina em um **gate**, um critério verificável. Não avance sem passar o gate.

> Os caminhos usam o layout da raiz (`docs/`, `scripts/`, `tasks/`). Se a arquitetura estiver dentro de `.claude/`, prefixe com `.claude/`.

```text
0 Briefing → 1 Agentes de liderança → 2 BRD→PRD→SDD→TSD (+ADRs) → 3 Agentes executores
           → 4 Ajuste de CLAUDE.md e scripts → 5 Épicos → Tasks → Execução
```

## Princípios

- **O briefing é a fonte.** Todo documento nasce dele e do documento anterior. Nada de requisito inventado (ver `CLAUDE.md`).
- **Você aprova cada documento** antes do próximo ser escrito. Em conflito entre fontes: instrução do usuário → TSD → SDD → PRD → BRD.
- **Lacuna vira pergunta,** não suposição. O Claude registra dúvidas e você responde.
- **Decisão arquitetural vira ADR** no momento em que é tomada.

---

## Fase 0 — Briefing

**Objetivo:** reunir a especificação bruta do que será construído.

**Passos**
1. Crie `docs/referencias/briefing.md` com o que você tem: problema, usuários, funcionalidades desejadas, restrições (prazo, stack obrigatória, integrações), o que está fora do escopo.
2. Anexe material de apoio em `docs/referencias/` (enunciado, wireframes, tokens de design, exemplos de API).
3. Peça ao Claude para ler tudo e devolver **só uma lista de perguntas** sobre lacunas e ambiguidades. Responda e atualize o briefing.

**Prompt sugerido**
> Leia `docs/referencias/` e liste as lacunas e ambiguidades do briefing como perguntas numeradas. Não crie nenhum documento.

**Gate:** `briefing.md` existe e as perguntas relevantes foram respondidas.

---

## Fase 1 — Agentes de liderança

**Objetivo:** criar os agentes que apoiam a escrita dos documentos. Os executores só podem ser definidos depois do TSD (fase 3), porque dependem da stack.

**Passos**
1. Para cada agente, copie `.claude/agents/_TEMPLATE.md` para `.claude/agents/<nome>.md`. Siga as instruções do topo do template: remova as crases do bloco, preencha todos os `{{...}}` e apague as seções que não se aplicarem.
2. Crie `po`, `arquiteto` e `tech-lead`. Eles só são acionados sob dúvida real (ver `.claude/agents/README.md`).
3. Defina `model` conforme a tabela "Perfil de modelo" do README de agentes. Nunca use `inherit`.
4. Restrinja `tools` ao necessário. Agentes de leitura não precisam de `Edit`, `Write` nem `Bash`.
5. Mantenha `token-optimizer` e `context-engineer`: já vêm prontos e só rodam a pedido seu, em par.

**Prompt sugerido**
> Crie o agente `po` a partir de `.claude/agents/_TEMPLATE.md`, seguindo a tabela de modelos de `.claude/agents/README.md`. Mostre o arquivo antes de salvar.

**Gate:** cada arquivo tem frontmatter com `name`, `description`, `model` explícito e `tools`, e `grep -r "{{" .claude/agents --exclude=_TEMPLATE.md` não retorna nada.

---

## Fase 2 — Documentação: BRD → PRD → SDD → TSD

**Objetivo:** especificar o projeto em camadas, cada uma derivada da anterior. Um documento só é criado quando o anterior existe e foi aprovado.

| Ordem | Documento | Responde | Deriva de | IDs |
|---|---|---|---|---|
| 1 | `docs/01-BRD.md` | Por que o projeto existe | briefing | `BR-xx` |
| 2 | `docs/02-PRD.md` | O que o produto faz | BRD | `FR-xx`, `RN-xx`, `NFR-xx`, `Q-xx` |
| 3 | `docs/03-SDD.md` | Como o sistema é desenhado | PRD | cabeçalhos `SDD-xx` |
| 4 | `docs/04-TSD.md` | Com qual tecnologia e contratos | SDD | cabeçalhos `TSD-xx` |

**Passos (repita para cada documento)**
1. Peça o documento ao Claude, citando o anterior. Cada requisito do PRD aponta para o `BR-xx` que atende; cada seção do SDD, para os `FR`/`RN`/`NFR`; cada seção do TSD, para o SDD.
2. Leia, corrija e **aprove explicitamente**. Itens em aberto ficam no PRD como `Q-xx` até serem respondidos.
3. Todo cabeçalho de seção do SDD e do TSD começa com o ID (ex.: `## TSD-07 Contratos de API`), para ser localizado por `Grep` sem ler o documento inteiro.

**Ao longo da fase**
- Registre cada decisão arquitetural em `docs/adr/NNNN-titulo.md` quando ela for tomada (stack, transporte, estratégia de dados, autenticação etc.).
- Preencha `docs/README.md`: as **seções globais** (IDs que valem para toda task: segurança, i18n, acessibilidade, padrões de código) e o **índice de ADRs**.
- Dúvida de requisito: acione o `po`. Dúvida estrutural: o `arquiteto`. Resposta que muda decisão vira ADR.

**Prompt sugerido**
> Escreva `docs/02-PRD.md` a partir de `docs/01-BRD.md` e do briefing, com IDs FR/RN/NFR/Q, rastreando cada item ao BR-xx. Não invente requisito: o que faltar vira Q-xx.

**Gate (por documento)**
- Tem IDs únicos e rastreáveis até o documento anterior.
- Não restam `{{...}}`.
- Você aprovou. O PRD não tem `Q-xx` bloqueante sem resposta.

**Gate (da fase):** os quatro documentos existem, `docs/README.md` está preenchido e as decisões relevantes têm ADR.

---

## Fase 3 — Agentes executores

**Objetivo:** criar os agentes que implementam, testam e revisam, agora que a stack está definida no TSD.

**Passos**
1. Escolha pela stack: `dev-frontend`, `dev-backend`, `dev-mobile`, `ux-designer` etc. Mais `qa` e `code-reviewer`, que existem em todo projeto.
2. Crie cada um a partir do `_TEMPLATE.md`. No campo de leitura, cite as **seções do TSD/SDD** que o papel consulta (por ID), e nada além.
3. Preencha os **comandos** do papel (instalar, testar o módulo, lint, typecheck). Dev roda só o módulo; `qa` roda a suíte; `code-reviewer` não executa testes.
4. Confira o `model` na tabela de `.claude/agents/README.md`. Se o projeto tiver papéis fora dela, acrescente a linha na tabela.
5. Cada agente deve ter exatamente um dono para cada responsabilidade. Dois agentes com o mesmo escopo indicam que um deles é desnecessário.

**Gate:** os agentes cobrem todos os valores possíveis do campo "Agente responsável" que as tasks usarão. Sem `{{...}}` nem `inherit`.

---

## Fase 4 — Ajuste de `CLAUDE.md` e `scripts/`

**Objetivo:** apontar o fluxo para o projeto real. Os caminhos de `scripts/` já apontam para `docs/01..04`, `docs/adr` e `tasks/`. O que falta é preencher os marcadores.

**Preencher (valores do projeto)**

| Onde | Marcador | O que colocar |
|---|---|---|
| `CLAUDE.md` seção "Projeto" | `{{PROJECT_NAME}}`, `{{DEFINIR}}`, `{{COMANDO}}` | nome, stack (resumo, detalhe em `TSD§...`), comandos reais, agentes ativos |
| `05-epic.json` | `{{PROJECT_NAME}}`, `{{DEFINIR: particularidades...}}`, regras de código | nome; se há fatias verticais ou fases; regras de qualidade |
| `06-epic_tasks.json` | `{{PROJECT_NAME}}`, exemplos de tasks, regras de código, `{{DEFINIR}}` | nome; exemplos de tasks bem dimensionadas; regras |
| `07-exec_tasks.json` | `{{PROJECT_NAME}}`, seções do TSD/SDD com stack, estrutura do repositório, comandos, estratégia de branches | nome; IDs `TSD§` das convenções; layout do código; comandos; fluxo de branches |
| `docs/README.md` | `{{DEFINIR}}` | seções globais e índice de ADRs (se não feito na fase 2) |

**Não preencher agora (parâmetros de execução):** `{{EPIC_PATH}}`, `{{TASK_NUMBER}}` e `{{PAPEL}}`. Eles são informados a cada execução (qual épico, qual task, qual agente).

**Passos**
1. Preencha os valores a partir do TSD. Não invente: tudo que for decisão técnica já deve estar no TSD ou em um ADR.
2. Confira que os caminhos em `inputs` existem (`ls docs/0*.md docs/adr`).
3. Valide a sintaxe dos JSONs.

**Verificações**
```bash
grep -rn "{{" scripts CLAUDE.md docs/README.md        # só devem sobrar EPIC_PATH, TASK_NUMBER, PAPEL
for f in scripts/*.json; do python3 -m json.tool "$f" > /dev/null && echo "ok $f"; done
```

**Gate:** nenhum `{{...}}` restante além dos três parâmetros de execução, JSONs válidos e todos os caminhos de `inputs` existem.

---

## Fase 5 — Épicos, tasks e execução

**Objetivo:** transformar a especificação em trabalho executável e entregá-lo task a task.

1. **Épicos:** rode `scripts/05-epic.json`. Saída em `tasks/epics/EPIC-NN-slug.md`. **Revise** antes de seguir: cada FR/RN/NFR deve estar em algum épico, e nenhum épico pode ter escopo fora do PRD.
2. **Tasks:** para cada épico, rode `scripts/06-epic_tasks.json` com `epic_path`. Saída em `tasks/NNNN-slug.md` (numeração global). O 06 extrai **uma vez** os trechos normativos dos documentos e os grava na task; confira que cada task tem escopo, critérios de aceite testáveis, dependências e agente responsável.
3. **Execução:** para cada task, rode `scripts/07-exec_tasks.json` com `task_number` e `epic_path`. O fluxo é: entender → implementar (executor) → `qa` → `code-reviewer` → corrigir → reverificar → documentar e fechar. A task concluída vai para `tasks/done/` e, com todas as tasks feitas, o épico vai para `tasks/epics/done/`.

**Regras de execução**
- Tasks em paralelo só se não houver dependência nem arquivo/recurso em comum; senão, sequencial.
- Task só é concluída com evidência verificável (bloco `## Evidências`).
- Se a implementação divergir do TSD, o Claude **não altera `docs/`**: registra a divergência para você decidir.
- Commit e push só a pedido seu.

**Gate:** épicos revisados antes do 06, tasks revisadas antes do 07 e evidências registradas em cada task fechada.

---

## Quando algo muda depois

- **Requisito novo ou alterado:** atualize o documento mais alto afetado (BRD/PRD) e desça a cadeia até o TSD. Depois, gere ou ajuste épicos e tasks. Tasks já planejadas guardam trechos literais: valide-os por `Grep` antes de executar.
- **Decisão técnica nova:** ADR, e referência no TSD.
- **Auditoria de custo e estrutura:** peça `token-optimizer` e `context-engineer` **em par**, fora de qualquer task em andamento.

## Checklist rápido

- [ ] 0. `briefing.md` pronto, perguntas respondidas
- [ ] 1. `po`, `arquiteto`, `tech-lead` criados
- [ ] 2. BRD, PRD, SDD, TSD aprovados; `docs/README.md` e ADRs preenchidos
- [ ] 3. Agentes executores criados (`dev-*`, `qa`, `code-reviewer`, `ux-designer` se houver)
- [ ] 4. `CLAUDE.md` e `scripts/` sem `{{...}}` (exceto parâmetros de execução)
- [ ] 5. Épicos revisados → tasks revisadas → execução com evidências
