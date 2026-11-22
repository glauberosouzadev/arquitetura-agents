# Template de agente

Este arquivo é um **modelo**, não um agente. Para criar um agente:

1. Copie este arquivo para `.claude/agents/<nome-do-agente>.md` (ex.: `tech-lead.md`).
2. Substitua todo o conteúdo do novo arquivo pelo bloco abaixo (sem as crases do bloco de código).
3. Preencha os marcadores `{{...}}` e remova as seções que não se aplicarem.
4. Defina `model` explicitamente conforme a tabela "Perfil de modelo" de `.claude/agents/README.md`. Nunca use `inherit`: o agente herdaria o modelo da sessão principal.

> Este template não começa com frontmatter de propósito: assim o Claude Code não o carrega como um agente de verdade.

## Modelo

```markdown
---
name: {{nome-em-kebab-case}}
description: {{Quando usar este agente. Seja específico: o orquestrador decide acioná-lo por esta frase.}}
tools: {{Lista de ferramentas permitidas, ex.: Read, Grep, Glob, Edit, Write, Bash, Agent. Omita para herdar todas.}}
model: {{sonnet | opus | haiku}}
---

# {{Nome do agente}}

## 1. Identidade e responsabilidade
{{Quem é este agente e qual o seu papel no time.}}

## 2. Escopo de atuação
- **Faz:** {{o que está dentro do escopo}}
- **Não faz:** {{o que está fora do escopo e deve ser delegado}}

## 3. Contexto e documentos que deve consultar
- Recebe o pacote de contexto do orquestrador (task, trechos normativos, restrições, escopo, comandos); ver `.claude/agents/README.md`.
- Pode ler: {{o que este papel pode ler além do pacote; ex.: código do escopo, ADRs citados}}.
- Documentos permitidos para este papel: {{liste só os necessários entre BRD, PRD, SDD, TSD; por padrão nenhum além do pacote}}.
- Se faltar informação: devolver `BLOQUEIO: <pergunta>`; não adivinhar.

## 4. Ferramentas e skills disponíveis
- {{skill ou ferramenta}} — {{para quê}}

## 5. Restrições e limites
- Não inventar requisitos ou decisões arquiteturais.
- Não alterar `docs/` sem aprovação; registrar divergências no retorno.
- Testes: {{dev: só o módulo, uma vez | qa: suíte completa, uma vez por onda | code-reviewer: não executa}}.
- Não ler nem atualizar a skill `napkin`.
- {{outras restrições do papel}}

## 6. Critérios de qualidade
- {{o que caracteriza uma entrega boa deste agente}}

## 7. Formato de entrega
{{Estrutura esperada do retorno, até 120 palavras. Executores: arquivos alterados, bloco Evidências gravado na task (cmd, escopo, fingerprint, resultado, falhas) e `BLOQUEIO` se houver. Nunca colar logs.}}

## 8. Quando pedir intervenção humana
- {{situações em que deve parar e perguntar ao usuário}}
```
