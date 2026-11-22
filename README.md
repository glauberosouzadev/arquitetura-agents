# arquitetura-agents

Modelo genérico de arquitetura multiagêntica (Spec-Driven Development + Multi-Agent Systems), feito para ser **reutilizado como ponto de partida em novos projetos**. Ele não pertence a nenhum projeto específico e não contém código, agentes nem documentos de um produto.

A explicação completa da arquitetura está em [`docs/referencias/Arquitetura multiagentica.md`](docs/referencias/Arquitetura%20multiagentica.md).

## Estrutura

```text
.
├── .claude/
│   ├── agents/        # agentes do projeto; _TEMPLATE.md é o modelo
│   ├── skills/        # capacidades reutilizáveis (napkin, playwright-cli)
│   └── settings.json  # regras do projeto: permissões e hooks
├── docs/
│   ├── adr/           # Architecture Decision Records
│   └── referencias/   # material de apoio (inclui o doc da arquitetura)
├── scripts/           # roteiros genéricos: épicos → tasks → execução
├── tasks/
│   ├── epics/         # épicos (done/ guarda os concluídos)
│   └── done/          # tasks concluídas
├── CLAUDE.md          # regras e estrutura obrigatória para o Claude
└── README.md
```

O `CLAUDE.md` é lido automaticamente pelo Claude Code em toda sessão e define o padrão que o projeto deve seguir.

## O que vem pronto e o que fica para depois

Este modelo é **intencionalmente um esqueleto**. Nada abaixo é criado de antemão, porque só faz sentido quando o conceito do projeto estiver definido:

- **Agentes** (`.claude/agents/`): ao iniciar o projeto, escolha quantos e quais agentes terá (ex.: arquiteto, tech lead, PO, UX, dev, QA, code reviewer, mais os auditores sob demanda token-optimizer e context-engineer). O orquestrador aciona os executores diretamente; liderança só sob dúvida (ver `.claude/agents/README.md`). Crie cada um a partir de `.claude/agents/_TEMPLATE.md`.
- **ADRs** (`docs/adr/`): registre cada decisão arquitetural relevante quando ela for tomada.
- **`.gitignore`**: criado ao iniciar o projeto, conforme a stack. Lembre de ignorar `.claude/settings.local.json`, `.env*` e `.playwright-cli/`.
- **Documentos** (`docs/`): BRD, PRD, SDD e TSD são criados em ordem, conforme o projeto é definido.
- **Código e testes** (`src/`, `tests/`): dependem da tecnologia escolhida.

## Scripts

Os arquivos de `scripts/` são roteiros em JSON, usados em sequência:

| Arquivo | Função |
|---|---|
| `05-epic.json` | Gera os épicos a partir dos documentos (TSD → SDD → PRD → BRD) |
| `06-epic_tasks.json` | Decompõe um épico em tasks atômicas |
| `07-exec_tasks.json` | Executa uma task: implementa, testa, revisa e conclui |

Eles trazem marcadores `{{...}}` (ex.: `{{PROJECT_NAME}}`, `{{DEFINIR: ...}}`) que devem ser preenchidos quando o projeto for definido.

## Como usar em um novo projeto

1. Copie este repositório (ou use como template) para o novo projeto.
2. Defina o conceito e crie `docs/01-BRD.md` a `docs/04-TSD.md`.
3. Escolha e crie os agentes em `.claude/agents/` a partir do `_TEMPLATE.md`.
4. Ajuste as permissões e os hooks em `.claude/settings.json`.
5. Preencha os marcadores `{{...}}` em `scripts/` e na seção "Projeto" do `CLAUDE.md`.
6. Gere os épicos, depois as tasks, e execute.
