# Validacao por Slice — Slice Validator

> Complementa `review-agents.md` (Planning Reviewer + Code Quality Reviewer).
> Aplicar em qualquer repo do workspace que trabalhe com slice files.

## Papel: Slice Validator

O Slice Validator garante que o que foi **proposto** em cada slice foi de fato **entregue** com evidencia objetiva antes de fechar (DONE). E o unico agente autorizado a transitar um slice para DONE.

**Regra absoluta:** o implementador nao pode autoaprovar o proprio slice. O Slice Validator deve ser um agente ou revisor diferente do implementador.

## Maquina de estados estrita

```
NOT_STARTED
  └─► PLAN_REVIEW          (Planning Reviewer emite resultado)
        ├─► APROVADO_BUILD  (Planning APROVADO ou APROVADO COM AJUSTES incorporados)
        │     └─► IN_PROGRESS  (TDD: Red → Green → Refactor)
        │           └─► CODE_REVIEW  (Code Quality Reviewer emite resultado)
        │                 ├─► DONE          (zero MUST-FIX; criterio de aceite verificado)
        │                 └─► IN_PROGRESS   (REPROVADO: volta para implementar)
        └─► BLOQUEADO       (Planning BLOQUEADO: nao inicia implementacao)

  ⟂ BLOCKED  (qualquer estagio; exige nota do bloqueio e condicao de saida)
  ⟂ ROLLED_BACK  (slice revertido apos DONE)
```

Nenhuma transicao e valida sem **evidencia registrada** (saida de comando, teste verde, caminho de arquivo, screenshot, DebugView ou outro artefato verificavel).

## Responsabilidades do Slice Validator

1. Ler o **criterio de aceite objetivo** e a **evidencia exigida** do slice (declarados no slice file e no `STATUS-*.md` correspondente).
2. Verificar objetivamente: rodar os testes/comandos do slice, confirmar que o artefato existe e corresponde ao proposto, aplicar os checklists de Codigo/SoC/Arquitetura/Testes de `review-agents.md`.
3. Emitir veredito e **atualizar o status com evidencia** no bloco de revisao do slice file (`templates/slice-review-block.md`).
4. Atualizar o campo `Status` no slice file e no ledger `STATUS-*.md` correspondente.

## Regras de bloqueio (de `review-agents.md`)

- `MUST-FIX` **impede DONE** em qualquer revisao (Planning ou Code Quality).
- `SHOULD-FIX` deve ser resolvido no slice ou registrado como follow-up explicito rastreavel.
- `NICE-TO-HAVE` nao bloqueia.
- Um slice nao entra em `APROVADO_BUILD` com Planning Review `BLOQUEADO`.
- `REPROVADO` no Code Quality volta o slice para `IN_PROGRESS` (nao fecha).

## TDD — obrigatorio para comportamento novo

1. **Red:** escrever o teste que falha antes de qualquer implementacao.
2. **Green:** implementar o minimo necessario para o teste passar.
3. **Refactor:** melhorar sem quebrar o verde.

O Slice Validator verifica que os testes existiam antes da implementacao (via historico de commits ou evidencia equivalente).

## Ledger de status

Cada plano que usa slices deve ter um `STATUS-*.md` com o ledger (tabela: Slice · Criterio de aceite objetivo · Evidencia exigida · Status). O Slice Validator e o unico que atualiza o campo `Status` no ledger. Ver template em `templates/slice-status-ledger.md`.

## Como usar em um repo

1. Referenciar esta guideline no `AGENTS.md` local (ver `templates/repo-AGENTS.stub.md`).
2. Criar o ledger `STATUS-*.md` com `templates/slice-status-ledger.md` ao abrir um plano.
3. Usar `templates/slice-review-block.md` em cada slice file (campos `Status` e `Evidencia`).
4. Ao fim de cada slice: lancar agente separado como Slice Validator (`run_in_background=false`; nao e o implementador).

## Compatibilidade

Aditivo. Repos que so usam `change-workflow.md`/`review-agents.md` continuam validos sem adotar esta camada. A adocao e recomendada para planos com >= 3 slices ou risco medio/alto.
