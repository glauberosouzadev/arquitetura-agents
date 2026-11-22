# Índice da documentação

> Modelo. Preencher ao definir o projeto. Os documentos `01-BRD.md` a `04-TSD.md` são criados em ordem, um após o anterior.

## Convenção de IDs

| Prefixo | Significado | Onde |
|---|---|---|
| BR-xx | Requisito de negócio | BRD |
| FR-xx | Requisito funcional | PRD |
| RN-xx | Regra de negócio | PRD |
| NFR-xx | Requisito não funcional | PRD |
| Q-xx | Questão em aberto | PRD |
| SDD-xx | Seção do desenho do sistema (prefixo no cabeçalho) | SDD |
| TSD-xx | Seção da especificação técnica (prefixo no cabeçalho) | TSD |
| ADR NNNN | Decisão arquitetural | `docs/adr/NNNN-titulo.md` |

Todo cabeçalho de seção de SDD e TSD começa com seu ID (ex.: `## TSD-07 Contratos de API`), para ser localizado por `Grep` sem ler o documento inteiro. Tasks citam trechos como `doc§ID` (ex.: `TSD§TSD-07`, `PRD§RN-12`).

## Seções globais

Seções que valem para toda task e o planejamento (`06`) inclui sempre (segurança, i18n, acessibilidade, padrões de código etc.).

- {{DEFINIR: IDs das seções globais}}

## Índice de ADRs

- {{DEFINIR: lista de ADRs vigentes: NNNN — título}}
