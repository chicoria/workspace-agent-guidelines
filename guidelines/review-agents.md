# Agentes De Revisao

Este documento define papeis reutilizaveis para revisar planos e codigo em qualquer repo do workspace.

## Planning Reviewer

Use antes de implementar slices nao triviais.

### Objetivo

Garantir que o plano e pequeno, claro, seguro e executavel por outro agente sem decisoes abertas.

### Checklist

- O problema esta claro e conectado ao objetivo do usuario.
- O slice cabe em ate um dia.
- O escopo evita refactors e arquivos nao relacionados.
- O plano identifica contratos, schemas, config, secrets, dados externos e deploy quando aplicavel.
- O criterio de aceite e verificavel por comando, teste, smoke ou observacao objetiva.
- A estrategia de testes cobre o risco principal.
- Ha rollback ou mitigacao clara.
- Dependencias e ordem de execucao estao explicitas.
- Lacunas estao documentadas em vez de escondidas como suposicoes.

### Resultado

```text
Planning Review: APROVADO | APROVADO COM AJUSTES | BLOQUEADO

MUST-FIX:
- ...

SHOULD-FIX:
- ...

Notas:
- ...
```

## Code Quality Reviewer

Use depois do refactor e antes de fechar o slice.

### Objetivo

Encontrar bugs, regressao, acoplamento indevido, fragilidade de testes, violacao de SoC e riscos operacionais antes de DONE.

### Checklist De Codigo

- Typecheck/lint/testes relevantes passam ou falhas estao explicadas.
- Strict mode respeitado.
- `any` e `!` estao ausentes ou justificados.
- Nomes sao claros e alinhados ao dominio.
- Erros sao explicitos e acionaveis.
- Nao ha fallback silencioso para default perigoso.
- Nao ha secrets, tokens ou valores de `.env.local` no diff.

### Checklist De SoC

- Regra de dominio esta separada de IO quando possivel.
- Entry point orquestra; nao concentra regra de negocio.
- Modulos de API externa recebem config resolvida; nao leem catalogo/env diretamente sem necessidade.
- Resolvedores de config sao testaveis sem rede/runtime.
- Funcoes puras cobrem decisoes importantes.
- Dependencias apontam em uma direcao clara.
- Abstracoes removem complexidade real.

### Checklist De Arquitetura

- Config especifica de tenant/produto/ambiente vem da fonte correta.
- Nao ha hardcode indevido de tenant, produto, dominio, lista, template ou secret.
- O mesmo codigo poderia servir outro tenant/produto com config diferente, quando o repo tiver esse requisito.
- Isolamento entre tenants/dados esta preservado e testado quando relevante.
- Mudancas em schema/catalogo/docs foram feitas no mesmo slice quando necessarias.

### Checklist De Testes

- Ha cobertura de happy path, edge cases e fail-fast paths proporcionais ao risco.
- Mocks nao compartilham estado entre testes.
- Nomes dos testes descrevem comportamento.
- Nao ha `it.only`, `describe.only` ou skips esquecidos.
- Testes de integracao/E2E externos sao opt-in e seguros por construcao.

### Resultado

```text
Code Quality Review: APROVADO | APROVADO COM RESSALVAS | REPROVADO

MUST-FIX:
- ...

SHOULD-FIX:
- ...

NICE-TO-HAVE:
- ...

Checks revisados:
- ...
```

## Regra De Bloqueio

- `MUST-FIX` bloqueia fechamento.
- `SHOULD-FIX` deve ser resolvido ou virar follow-up explicito.
- `NICE-TO-HAVE` nao bloqueia.
- O implementador nao deve autoaprovar mudancas nao triviais.
