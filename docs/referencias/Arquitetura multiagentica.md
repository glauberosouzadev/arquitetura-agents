# PROJECT ENGINEERING BASE

## Arquitetura Genérica para Desenvolvimento de Software com Agentes de IA

**Modelo:** Agentic Software Engineering  
**Abordagem:** Spec-Driven Development + Multi-Agent Systems + Workflow Orchestration  
**Objetivo:** Padronizar a organização, o planejamento, a execução e a validação de projetos de software utilizando agentes especializados de IA.

---

# 1. VISÃO GERAL DA ARQUITETURA

A arquitetura organiza o desenvolvimento em cinco camadas principais:

```text
┌──────────────────────────────────────────────────────────┐
│                    PROJECT ENGINEERING                   │
│                                                          │
│  ┌────────────────────────────────────────────────────┐  │
│  │  01. GOVERNANÇA E ESPECIALIZAÇÃO                    │  │
│  │                                                    │  │
│  │  Agentes especializados                            │  │
│  │  Arquiteto | Tech Lead | PO | UX | Dev | QA         │  │
│  └────────────────────────────────────────────────────┘  │
│                           │                              │
│                           ▼                              │
│  ┌────────────────────────────────────────────────────┐  │
│  │  02. ENGENHARIA DE REQUISITOS                       │  │
│  │                                                    │  │
│  │  BRD → PRD → SDD → TSD                              │  │
│  │                                                    │  │
│  │  Negócio → Produto → Sistema → Tecnologia          │  │
│  └────────────────────────────────────────────────────┘  │
│                           │                              │
│                           ▼                              │
│  ┌────────────────────────────────────────────────────┐  │
│  │  03. PLANEJAMENTO E DECOMPOSIÇÃO                    │  │
│  │                                                    │  │
│  │  EPICS → TASKS → EXECUTION TASKS                    │  │
│  │                                                    │  │
│  │  Funcionalidades → Atividades → Execução           │  │
│  └────────────────────────────────────────────────────┘  │
│                           │                              │
│                           ▼                              │
│  ┌────────────────────────────────────────────────────┐  │
│  │  04. EXECUÇÃO E CAPACIDADES                         │  │
│  │                                                    │  │
│  │  Agentes + Skills + Ferramentas + Scripts           │  │
│  │                                                    │  │
│  │  Desenvolvimento, automação e integração           │  │
│  └────────────────────────────────────────────────────┘  │
│                           │                              │
│                           ▼                              │
│  ┌────────────────────────────────────────────────────┐  │
│  │  05. QUALIDADE E CONTROLE                           │  │
│  │                                                    │  │
│  │  QA → Code Review → Validação → Conclusão           │  │
│  │                                                    │  │
│  │  Feedback, rastreabilidade e controle de estado     │  │
│  └────────────────────────────────────────────────────┘  │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

---

# 2. ESTRUTURA DE DIRETÓRIOS

Estrutura de referência independente de tecnologia.

```text
PROJECT/
│
├── .claude/
│   │
│   ├── agents/
│   │   ├── README.md
│   │   ├── _TEMPLATE.md
│   │   ├── arquiteto.md
│   │   ├── tech-lead.md
│   │   ├── po.md
│   │   ├── ux-designer.md
│   │   ├── dev-backend.md
│   │   ├── dev-frontend.md
│   │   ├── dev-mobile.md
│   │   ├── qa.md
│   │   └── code-reviewer.md
│   │
│   ├── skills/
│   │   ├── README.md
│   │   ├── skill-01/
│   │   ├── skill-02/
│   │   └── skill-03/
│   │
│   ├── settings.json
│   └── README.md
│
├── docs/
│   │
│   ├── adr/
│   │
│   ├── referencias/
│   │   ├── README.md
│   │   └── [documentos-de-referencia]
│   │
│   ├── 01-BRD.md
│   ├── 02-PRD.md
│   ├── 03-SDD.md
│   ├── 04-TSD.md
│   └── README.md
│
├── scripts/
│   ├── README.md
│   ├── 05-epic.json
│   ├── 06-epic_tasks.json
│   └── 07-exec_tasks.json
│
├── tasks/
│   │
│   ├── README.md
│   │
│   ├── epics/
│   │   ├── README.md
│   │   └── done/
│   │
│   └── done/
│
├── tests/
│   ├── README.md
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
├── src/
│   └── [codigo-fonte-do-projeto]
│
├── CLAUDE.md
├── README.md
├── PROJECT-ENGINEERING-BASE.md
└── .gitignore
```

**Observações:**

- `.claude/` representa a configuração dos agentes para o ambiente Claude Code. Em outros ambientes, a configuração pode ser adaptada.
- `src/` e `tests/` representam a organização convencional do código e dos testes, podendo mudar conforme a tecnologia.
- Os agentes devem ser criados conforme a necessidade real do projeto.
- Skills são capacidades reutilizáveis, não necessariamente agentes independentes.
- Os documentos podem começar como estruturas vazias e evoluir durante o projeto.

---

# 3. CAMADA DE AGENTES

## Objetivo

Distribuir responsabilidades entre agentes especializados, evitando concentração excessiva de contexto e responsabilidades em um único agente.

### Organização funcional

```text
                    ORQUESTRADOR
                         │
          ┌──────────────┼──────────────┐
          │              │              │
          ▼              ▼              ▼
       PRODUTO        ARQUITETURA     EXECUÇÃO
          │              │              │
          ▼              ▼              ▼
          PO          ARQUITETO      TECH LEAD
          │              │              │
          ▼              ▼              │
     REQUISITOS       DESIGN             │
                         │              │
                         └──────┬───────┘
                                │
               ┌────────────────┼───────────────┐
               │                │               │
               ▼                ▼               ▼
          DEV BACKEND       DEV FRONTEND     DEV MOBILE
               │                │               │
               └────────────────┼───────────────┘
                                │
                                ▼
                              QA
                                │
                                ▼
                         CODE REVIEWER
                                │
                                ▼
                         VALIDAÇÃO FINAL
```

### Responsabilidades


| Agente        | Responsabilidade                            |
| ------------- | ------------------------------------------- |
| Orquestrador  | Coordenar o fluxo e distribuir atividades   |
| PO            | Requisitos, objetivos e regras de negócio   |
| Arquiteto     | Arquitetura, padrões e decisões estruturais |
| Tech Lead     | Direcionamento técnico e integração         |
| UX Designer   | Experiência e interface                     |
| Dev Backend   | Implementação backend                       |
| Dev Frontend  | Implementação frontend                      |
| Dev Mobile    | Implementação mobile                        |
| QA            | Testes e validação funcional                |
| Code Reviewer | Revisão de código e conformidade técnica    |


Os agentes devem ter instruções claras contendo:

1. Identidade e responsabilidade.
2. Escopo de atuação.
3. Contexto e documentos que devem consultar.
4. Ferramentas e skills disponíveis.
5. Restrições e limites de atuação.
6. Critérios de qualidade.
7. Formato esperado de entrega.
8. Condições para solicitar intervenção humana.

---

# 4. CAMADA DE DOCUMENTAÇÃO

## Objetivo

Transformar conhecimento do negócio em especificações progressivamente mais detalhadas.

Referencia: [https://medium.com/@habibullah.diu/understanding-brd-prd-sdd-tsd-the-blueprint-of-modern-software-engineering-689aac9acc0b](https://medium.com/@habibullah.diu/understanding-brd-prd-sdd-tsd-the-blueprint-of-modern-software-engineering-689aac9acc0b)

```text
        NECESSIDADE / PROBLEMA
                 │
                 ▼
          01. BRD
     Business Requirements
                 │
                 ▼
          02. PRD
      Product Requirements
                 │
                 ▼
          03. SDD
       Software Design
                 │
                 ▼
          04. TSD
    Technical Specification
                 │
                 ▼
          PLANEJAMENTO
```

### Documentos

**01-BRD.md**

Define o contexto do negócio:

- Problema identificado.
- Objetivos.
- Público envolvido.
- Necessidades.
- Restrições de negócio.
- Resultados esperados.

**02-PRD.md**

Define o produto:

- Funcionalidades.
- Requisitos funcionais.
- Requisitos não funcionais.
- Jornadas de usuário.
- Regras de negócio.
- Critérios de aceitação.
- Priorização.

**03-SDD.md**

Define o desenho do sistema:

- Arquitetura geral.
- Componentes.
- Domínios.
- Módulos.
- Fluxos de comunicação.
- Integrações.
- Modelagem de dados.
- Decisões arquiteturais.

**04-TSD.md**

Define a implementação técnica:

- Tecnologias.
- Estrutura do projeto.
- Bibliotecas.
- Contratos de API.
- Configurações.
- Infraestrutura.
- Estratégias de testes.
- Padrões de implementação.

**Princípio:** os documentos devem manter consistência entre si. Uma decisão técnica não deve contradizer uma regra de negócio ou requisito sem que a alteração seja documentada.

---

# 5. CAMADA DE PLANEJAMENTO

## Objetivo

Converter especificações em unidades de trabalho executáveis.

```text
               DOCUMENTAÇÃO
                    │
                    ▼
                  EPIC
                    │
          ┌─────────┼─────────┐
          │         │         │
          ▼         ▼         ▼
        TASK 1    TASK 2    TASK 3
          │         │         │
          ▼         ▼         ▼
       EXEC 1    EXEC 2    EXEC 3
          │         │         │
          └─────────┼─────────┘
                    │
                    ▼
                 ENTREGA
```

### Hierarquia

**EPIC**

Representa uma grande capacidade ou entrega funcional.

**TASK**

Representa uma unidade de trabalho necessária para completar uma Epic.

**EXECUTION TASK**

Representa uma atividade suficientemente detalhada para ser executada por um agente ou desenvolvedor.

### Regras de planejamento

- Toda Epic deve possuir um objetivo identificável.
- Toda Task deve estar relacionada a uma Epic.
- Toda Execution Task deve possuir um resultado esperado.
- As atividades devem possuir critérios de aceitação.
- Dependências devem ser identificadas.
- Tarefas concluídas devem preservar seu histórico.
- O planejamento deve permanecer rastreável até os requisitos de origem.

---

# 6. CAMADA DE EXECUÇÃO

## Objetivo

Permitir que agentes executem atividades utilizando ferramentas e capacidades especializadas.

```text
              EXECUTION TASK
                     │
                     ▼
               ORQUESTRADOR
                     │
                     ▼
              SELEÇÃO DO AGENTE
                     │
                     ▼
              CONTEXTO NECESSÁRIO
                     │
                     ▼
              SKILLS E FERRAMENTAS
                     │
                     ▼
                 EXECUÇÃO
                     │
                     ▼
                RESULTADO
                     │
                     ▼
                 VALIDAÇÃO
```

### Princípios

- Cada atividade deve possuir escopo delimitado.
- O agente deve consultar a documentação pertinente.
- O agente não deve inventar requisitos ou decisões arquiteturais.
- Alterações relevantes devem ser documentadas.
- Dependências e bloqueios devem ser comunicados.
- A execução deve gerar evidências verificáveis.
- Atividades incompletas não devem ser declaradas concluídas.

---

# 7. CAMADA DE QUALIDADE

## Objetivo

Garantir que o resultado produzido esteja de acordo com os requisitos e padrões definidos.

```text
                  IMPLEMENTAÇÃO
                       │
                       ▼
                 TESTES AUTOMATIZADOS
                       │
                       ▼
                  VALIDAÇÃO QA
                       │
                       ▼
                  CODE REVIEW
                       │
                       ▼
                CRITÉRIOS ATENDIDOS?
                    /       \
                  NÃO       SIM
                  │          │
                  ▼          ▼
              CORREÇÃO    CONCLUSÃO
                  │          │
                  └───↺      ▼
                         ATUALIZAÇÃO
                         DO ESTADO
```

### Critérios mínimos

- Requisitos atendidos.
- Regras de negócio respeitadas.
- Testes pertinentes executados.
- Ausência de regressões conhecidas.
- Código compatível com os padrões estabelecidos.
- Documentação atualizada quando necessário.
- Revisão concluída.
- Evidências de validação registradas.

---

# 8. CICLO DE VIDA DO PROJETO

```
       ┌─────────────────────┐
       │   INICIALIZAÇÃO     │
       │                     │
       │ Contexto e objetivos│
       └──────────┬──────────┘
                  │
                  ▼
       ┌─────────────────────┐
       │    ESPECIFICAÇÃO    │
       │                     │
       │ BRD / PRD / SDD / TSD│
       └──────────┬──────────┘
                  │
                  ▼
       ┌─────────────────────┐
       │     PLANEJAMENTO    │
       │                     │
       │ EPICS / TASKS       │
       └──────────┬──────────┘
                  │
                  ▼
       ┌─────────────────────┐
       │      EXECUÇÃO       │◄──────────┐
       │                     │           │
       │ Agentes e ferramentas│           │
       └──────────┬──────────┘           │
                  │                      │
                  ▼                      │
       ┌─────────────────────┐           │
       │      VALIDAÇÃO      │           │
       │                     │           │
       │ QA / Review / Tests │           │
       └──────────┬──────────┘           │
                  │                      │
             APROVADO?                   │
              /    \                     │
            NÃO    SIM                   │
             │      │                    │
             │      ▼                    │
             │  ┌─────────────────┐      │
             │  │    CONCLUSÃO    │      │
             │  │                 │      │
             │  │ Entrega e estado│      │
             │  └─────────────────┘      │
             │                           │
             └───────────────────────────┘
```

O ciclo pode ser repetido por funcionalidade, Epic ou entrega.

---

# 9. PRINCÍPIOS ARQUITETURAIS

A estrutura deve respeitar os seguintes princípios:


| Princípio              | Aplicação                                          |
| ---------------------- | -------------------------------------------------- |
| Separation of Concerns | Responsabilidades delimitadas                      |
| Single Responsibility  | Agentes e módulos com escopo definido              |
| DRY                    | Evitar duplicação de conhecimento e instruções     |
| KISS                   | Evitar complexidade desnecessária                  |
| SOLID                  | Aplicar quando pertinente ao desenho do software   |
| Traceability           | Rastrear requisitos até entregas                   |
| Modularity             | Permitir evolução independente                     |
| Reproducibility        | Tornar processos repetíveis                        |
| Observability          | Registrar resultados, falhas e decisões            |
| Human-in-the-Loop      | Preservar supervisão humana em decisões relevantes |


---

# 10. GOVERNANÇA E CONTROLE

A arquitetura deve estabelecer mecanismos para:

- Controle de alterações nos documentos.
- Registro de decisões arquiteturais.
- Versionamento de especificações.
- Controle de estado das tarefas.
- Registro de falhas e bloqueios.
- Verificação de dependências.
- Validação antes de concluir entregas.
- Preservação do histórico de execução.

Decisões importantes devem ser registradas, preferencialmente em documentos próprios, como Architecture Decision Records (ADR), em `docs/adr/`.

---

# 11. ADAPTAÇÃO POR PROJETO

Esta arquitetura é uma base, não uma imposição.

A cada novo projeto, o orquestrador deve identificar:

1. Qual é a natureza do projeto?
2. Quais agentes são realmente necessários?
3. Quais documentos precisam ser produzidos?
4. Quais tecnologias serão utilizadas?
5. Quais skills e ferramentas serão necessárias?
6. Quais etapas podem ser automatizadas?
7. Quais decisões exigem aprovação humana?
8. Quais critérios de qualidade devem ser aplicados?

Não criar agentes, documentos, diretórios ou processos sem necessidade identificável.

A estrutura deve crescer conforme a complexidade do projeto, e não o contrário.

---

# 12. OBJETIVO FINAL

Construir um ambiente de engenharia de software no qual:

- O conhecimento seja estruturado.
- As responsabilidades sejam distribuídas.
- As especificações orientem a implementação.
- O planejamento seja rastreável.
- Os agentes trabalhem de maneira coordenada.
- A execução produza resultados verificáveis.
- A qualidade seja validada sistematicamente.
- A intervenção humana permaneça possível e controlada.

**Filosofia central:**

> Não utilizar a inteligência artificial apenas para produzir código, mas para estruturar, coordenar e executar um processo de engenharia de software.

---

**PROJECT ENGINEERING BASE**  
*Glauber Souza*
