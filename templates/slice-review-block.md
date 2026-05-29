## Planning Review

> Preenchido por agente revisor ou humano antes de iniciar implementacao de slice nao trivial.
> Ver checklist completo em `guidelines/review-agents.md` (papel Planning Reviewer).

### YYYY-MM-DD HH:MM by <Revisor>

**Status:** PLAN_REVIEW → APROVADO_BUILD | BLOQUEADO

**Resultado:** APROVADO | APROVADO COM AJUSTES | BLOQUEADO

Evidencia:
- {comando rodado / artefato verificado}

MUST-FIX:
- ...

SHOULD-FIX:
- ...

Notas:
- ...

---

## Code Quality Review

> Preenchido por agente revisor ou humano depois do refactor e antes de DONE.
> Ver checklists completos em `guidelines/review-agents.md` (papel Code Quality Reviewer).
> Implementador nao pode autoaprovar — ver `guidelines/slice-validation.md`.

### YYYY-MM-DD HH:MM by <Revisor>

**Status:** CODE_REVIEW → DONE | IN_PROGRESS (se REPROVADO)

**Resultado:** APROVADO | APROVADO COM RESSALVAS | REPROVADO

Evidencia:
- {saida de testes / DebugView / caminho de arquivo / screenshot}

Codigo:
- ...

Arquitetura / SoC:
- ...

Testes:
- ...

MUST-FIX:
- ...

SHOULD-FIX:
- ...

NICE-TO-HAVE:
- ...
