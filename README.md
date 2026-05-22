# Workspace Agent Guidelines

Fonte reutilizavel de workflow, guard rails e criterios de revisao para agentes que trabalham nos repos deste workspace.

## Como usar

Cada repo deve ter um `AGENTS.md` local apontando para:

```text
/Users/chicoria/git/workspace-agent-guidelines/guidelines/change-workflow.md
```

O `AGENTS.md` local deve manter regras especificas do projeto. Quando houver conflito, vence a regra mais especifica do repo, desde que ela nao enfraqueca seguranca, privacidade, testes ou protecao de secrets.

## Documentos

- `guidelines/change-workflow.md`: workflow padrao para qualquer mudanca.
- `guidelines/review-agents.md`: papeis e checklists para agentes revisores.
- `templates/repo-AGENTS.stub.md`: bloco reutilizavel para ativar a guideline em um repo.
- `templates/slice-review-block.md`: blocos de revisao para arquivos de slice/plano.

## Atualizacao

Mudancas neste repo devem ser pequenas, revisaveis e refletir pratica real usada nos repos. Evite transformar a guideline em documentacao teorica: cada regra precisa ser executavel por um agente ou verificavel por um revisor.
