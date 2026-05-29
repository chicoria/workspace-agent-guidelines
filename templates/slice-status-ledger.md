# STATUS — {Nome do Plano}

> Source of truth de progresso do `PLANO-{NOME}.md`.
> Atualizado a cada slice pelo **Slice Validator** (nao pelo implementador).
> Ultima atualizacao: YYYY-MM-DD (criacao).

## Recovery point (ordem de leitura obrigatoria)

1. `PLANO-{NOME}.md` — design (o que e por que).
2. Este arquivo — onde estamos agora.
3. `slices/{grupo}/{N}-*.md` do slice em foco — detalhe executavel + Execucao append-only.

## Maquina de estados estrita

```
NOT_STARTED → PLAN_REVIEW → APROVADO_BUILD → IN_PROGRESS → CODE_REVIEW → DONE   (⟂ BLOCKED)
```

Regras: `MUST-FIX` impede DONE; `REPROVADO` volta para IN_PROGRESS; nao entra em APROVADO_BUILD com Planning BLOQUEADO; **toda transicao exige evidencia registrada**. Implementador **nao autoaprova**.

Ver `guidelines/slice-validation.md` para a definicao completa do papel Slice Validator.

## Ledger

| Slice | Criterio de aceite objetivo (o "proposto") | Evidencia exigida | Status |
|---|---|---|---|
| {id} | {o que deve ser verdade para fechar} | {saida de comando, teste verde, arquivo, screenshot} | NOT_STARTED |

## Legenda de estados

| Estado | Significado |
|---|---|
| `NOT_STARTED` | Nao iniciado |
| `PLAN_REVIEW` | Aguardando Planning Review |
| `APROVADO_BUILD` | Planning aprovado; pronto para implementar |
| `IN_PROGRESS` | Em implementacao (TDD ativo) |
| `CODE_REVIEW` | Aguardando Code Quality Review |
| `DONE` | Criterio de aceite verificado; zero MUST-FIX |
| `BLOCKED` | Bloqueado; exige nota + condicao de saida |
| `ROLLED_BACK` | Revertido apos DONE |

## Ordem de execucao

{descrever dependencias entre slices}

## Pre-requisitos transversais

- {listar bloqueantes globais do plano}

## Log de mudancas do STATUS

- **YYYY-MM-DD:** {evento}
