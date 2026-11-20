# Template de agente

Este arquivo é um **modelo**, não um agente. Para criar um agente:

1. Copie este arquivo para `.claude/agents/<nome-do-agente>.md` (ex.: `tech-lead.md`).
2. Substitua todo o conteúdo do novo arquivo pelo bloco abaixo (sem as crases do bloco de código).
3. Preencha os marcadores `{{...}}` e remova as seções que não se aplicarem.

> Este template não começa com frontmatter de propósito: assim o Claude Code não o carrega como um agente de verdade.

## Modelo

```markdown
---
name: {{nome-em-kebab-case}}
description: {{Quando usar este agente. Seja específico: o orquestrador decide acioná-lo por esta frase.}}
tools: {{Lista de ferramentas permitidas, ex.: Read, Grep, Glob, Edit, Write, Bash, Agent. Omita para herdar todas.}}
model: {{sonnet | opus | haiku | inherit}}
---

# {{Nome do agente}}

## 1. Identidade e responsabilidade
{{Quem é este agente e qual o seu papel no time.}}

## 2. Escopo de atuação
- **Faz:** {{o que está dentro do escopo}}
- **Não faz:** {{o que está fora do escopo e deve ser delegado}}

## 3. Contexto e documentos que deve consultar
- `docs/01-BRD.md` — {{quando consultar}}
- `docs/02-PRD.md` — {{quando consultar}}
- `docs/03-SDD.md` — {{quando consultar}}
- `docs/04-TSD.md` — {{quando consultar}}
- `docs/adr/` — decisões arquiteturais vigentes

## 4. Ferramentas e skills disponíveis
- {{skill ou ferramenta}} — {{para quê}}

## 5. Restrições e limites
- Não inventar requisitos ou decisões arquiteturais.
- Não alterar `docs/` sem aprovação; registrar divergências no retorno.
- {{outras restrições do papel}}

## 6. Critérios de qualidade
- {{o que caracteriza uma entrega boa deste agente}}

## 7. Formato de entrega
{{Estrutura esperada do retorno: o que listar, limite de palavras, evidências.}}

## 8. Quando pedir intervenção humana
- {{situações em que deve parar e perguntar ao usuário}}
```
