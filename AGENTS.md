# Agent Instructions - Workspace Guidelines

Este repo contem instrucoes reutilizaveis para agentes. Ao alterar qualquer guideline:

- preserve compatibilidade com repos que apontam para estes arquivos;
- mantenha regras em portugues claro e operacional;
- prefira checklists curtos, comandos executaveis e criterios de aceite observaveis;
- nao inclua secrets, tokens, credenciais, cookies ou valores reais de `.env.local`;
- atualize `README.md` quando criar, mover ou remover documentos publicos;
- valide `git diff --check` antes de finalizar.

Para mudancas substanciais nestas guidelines, aplique o proprio fluxo:

1. descreva o problema;
2. proponha uma alteracao pequena;
3. revise clareza, aplicabilidade e conflitos com regras locais;
4. registre qualquer decisao de tradeoff no resumo final.

## Governanca de Slices

Este repo define a camada de validacao por slice usada ao longo da implementacao de qualquer plano multi-slice no workspace:

- `guidelines/slice-validation.md`: papel Slice Validator, maquina de estados estrita, regras de bloqueio.
- `templates/slice-status-ledger.md`: template do ledger de status (source of truth de progresso).
- `templates/slice-review-block.md`: blocos de Planning Review e Code Quality Review com campos Status e Evidencia.

**Regras operacionais:**
- O implementador nao autoaprova o proprio slice — Slice Validator e sempre um agente ou revisor diferente.
- Toda transicao de estado exige evidencia registrada (saida de comando, teste verde, artefato verificavel).
- `MUST-FIX` impede DONE em qualquer revisao.
- Planos ativos com >= 3 slices ou risco medio/alto devem adotar esta camada.
- Ao abrir um plano: criar `STATUS-*.md` a partir de `templates/slice-status-ledger.md`; ao fim de cada slice: lancar Slice Validator separado antes de transitar para DONE.
