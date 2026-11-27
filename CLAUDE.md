# CLAUDE.md

Este repositório segue o padrão **Project Engineering Base**: arquitetura multiagêntica com Spec-Driven Development. A explicação completa está em `docs/referencias/Arquitetura multiagentica.md`. Consulte a seção pertinente antes de agir: §3 agentes, §4 documentos, §5 planejamento, §6 execução.

## Estrutura obrigatória

Mantenha esta estrutura. Não mova pastas nem crie diretórios paralelos com a mesma função.

```text
.
├── CLAUDE.md                  # este arquivo: regras para o Claude
├── README.md                  # visão geral para pessoas
├── .claude/
│   ├── settings.json          # permissões e hooks do projeto
│   ├── agents/                # um .md por agente; criar a partir de _TEMPLATE.md
│   └── skills/                # capacidades reutilizáveis (uma pasta por skill, com SKILL.md)
├── docs/
│   ├── README.md              # índice: convenção de IDs, seções globais, ADRs
│   ├── 01-BRD.md              # negócio     (criado ao definir o projeto)
│   ├── 02-PRD.md              # produto     (criado após o BRD)
│   ├── 03-SDD.md              # sistema     (criado após o PRD)
│   ├── 04-TSD.md              # tecnologia  (criado após o SDD)
│   ├── adr/                   # decisões arquiteturais: NNNN-titulo.md
│   └── referencias/           # material de apoio
├── scripts/
│   ├── 05-epic.json           # docs → épicos
│   ├── 06-epic_tasks.json     # épico → tasks
│   └── 07-exec_tasks.json     # task → implementação, teste, review, conclusão
└── tasks/
    ├── epics/                 # EPIC-NN-slug.md (done/ = concluídos)
    ├── NNNN-slug.md           # tasks abertas (numeração global)
    └── done/                  # tasks concluídas
```

## Fluxo de trabalho

Passo a passo de início de projeto (agentes, documentos, ajuste dos scripts): `docs/referencias/GUIA-DE-INICIO.md`.

1. **Especificação:** BRD → PRD → SDD → TSD, nesta ordem. Um documento só é criado quando o anterior existe.
2. **Planejamento:** rodar `scripts/05-epic.json` para gerar os épicos, depois `scripts/06-epic_tasks.json` para cada épico.
3. **Execução:** rodar `scripts/07-exec_tasks.json` para cada task. Ao concluir, a task vai para `tasks/done/` e, quando todas terminam, o épico vai para `tasks/epics/done/`.

Em conflito entre fontes, vale esta ordem: instrução atual do usuário → TSD → SDD → PRD → BRD.

## Regras

- Não inventar requisitos nem decisões arquiteturais: consulte `docs/`. Se faltar informação, pergunte.
- Não alterar `docs/` por conta própria; registre divergências e lacunas no retorno para o usuário decidir.
- Toda decisão arquitetural relevante vira um ADR em `docs/adr/`.
- Não criar agentes, documentos, pastas ou processos sem necessidade identificável (doc de arquitetura, §11).
- Não declarar concluída uma atividade sem evidência verificável (testes, typecheck, lint, review).
- Commit e push só com pedido explícito do usuário.
- Tasks em paralelo só se não houver dependência nem arquivo/recurso em comum; caso contrário, sequencial. Testes: dev roda só o módulo, `qa` roda a suíte uma vez, orquestrador e `code-reviewer` não rodam testes; só o bloco Evidências da task.
- Orquestração: o orquestrador (sessão principal) aciona os agentes executores diretamente; PO, arquiteto e tech-lead só sob dúvida ou necessidade real. `token-optimizer` e `context-engineer` só a pedido do usuário, nunca durante uma task, sempre em par. Detalhes em `.claude/agents/README.md`.

## Projeto

> Preencher ao iniciar o projeto. Enquanto houver `{{...}}`, o projeto ainda não foi definido.

- **Nome:** {{PROJECT_NAME}}
- **Stack:** {{DEFINIR}}
- **Comandos:** instalar `{{COMANDO}}` · testar `{{COMANDO}}` · lint `{{COMANDO}}` · build `{{COMANDO}}`
- **Agentes ativos:** `token-optimizer`, `context-engineer` (auditoria sob demanda) · {{DEFINIR: demais agentes}}
