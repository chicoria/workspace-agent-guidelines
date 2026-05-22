# Workflow Unificado Para Mudancas Por Agentes

Use este workflow em qualquer repo do workspace. Regras locais em `AGENTS.md` continuam validas e podem adicionar restricoes especificas.

## 1. Comece pelo contexto

- Leia o `AGENTS.md` local antes de alterar arquivos.
- Identifique se ha docs de arquitetura, planos, tickets, ADRs, runbooks ou guidelines especificas.
- Entenda o estado do worktree com `git status --short --branch`.
- Nao reverta nem sobrescreva mudancas existentes sem instrucao explicita.
- Nunca exponha ou commite secrets, tokens, cookies, chaves privadas ou `.env.local`.

## 2. Classifique a mudanca

Classifique antes de implementar:

- **trivial**: texto pequeno, comentario, typo, sem impacto comportamental;
- **codigo local**: altera uma funcao/modulo com risco limitado;
- **feature/bug relevante**: altera comportamento observavel;
- **cross-module**: afeta contratos, integracoes, schema, deploy ou multiplos repos;
- **externo/sensivel**: envolve pagamentos, CRM, analytics, dados pessoais, producao ou secrets.

Mudancas nao triviais devem ser feitas em slices pequenos, com plano, validacao e rollback.

## 3. Trabalhe por slice

Um slice deve caber em ate um dia e ter:

- objetivo e problema;
- escopo fora/dentro;
- arquivos ou modulos provaveis;
- riscos e dependencias;
- testes e validacao executavel;
- criterio de aceite;
- rollback ou caminho de reversao;
- registro append-only de execucao, decisoes e gotchas.

Se o slice ficar amplo demais, divida antes de executar.

## 4. Planning Review antes de implementar

Para mudancas nao triviais, peca revisao de planejamento por agente especialista ou humano antes da implementacao.

O Planning Reviewer verifica:

- o slice e pequeno e executavel;
- o objetivo e o criterio de aceite sao claros;
- ha plano de testes proporcional ao risco;
- dependencias, migracoes e ordem de deploy estao claras;
- rollback existe ou o risco de ausencia esta documentado;
- regras locais, catalogos, schemas, secrets e integracoes foram considerados.

Resultado permitido:

- `APROVADO`: pode implementar;
- `APROVADO COM AJUSTES`: implementar apos incorporar ajustes listados;
- `BLOQUEADO`: nao implementar ate corrigir o plano.

## 5. Implemente incrementalmente

- Prefira TDD quando houver comportamento novo ou bug reproduzivel: Red -> Green -> Refactor.
- Implemente o minimo necessario para passar o criterio do slice.
- Mantenha funcoes puras para regra de dominio sempre que possivel.
- Separe IO, runtime, rede, banco e APIs externas da logica testavel.
- Use padroes locais do repo antes de criar nova abstracao.
- Evite duplicacao real, mas nao crie abstracoes especulativas.

## 6. Guard rails de codigo

- TypeScript strict quando aplicavel.
- `any` e non-null assertion (`!`) precisam de justificativa clara.
- Nomes devem expressar dominio e responsabilidade.
- Erros devem ser explicitos, com mensagem util e fail-fast quando config obrigatoria estiver ausente.
- Nao use fallback silencioso para defaults perigosos.
- Nao hardcode tenant, produto, dominio, token, ID de lista, ID de template ou credencial quando isso pertence a config.
- Secrets devem vir de env local, cofre, Cloudflare/GitHub secrets ou mecanismo definido pelo repo.

## 7. Code Quality Review antes de fechar

Para todo slice com codigo, execute revisao por agente de qualidade ou humano. O implementador nao deve autoaprovar mudancas nao triviais.

O revisor classifica achados:

- `MUST-FIX`: bloqueia fechamento;
- `SHOULD-FIX`: resolver no slice ou registrar follow-up explicito;
- `NICE-TO-HAVE`: melhoria opcional.

Nenhum slice fecha com `MUST-FIX` aberto.

## 8. Validacao

Rode os checks relevantes ao risco:

- testes unitarios do modulo/worker afetado;
- typecheck/lint quando existirem;
- testes E2E ou smoke quando comportamento integrado mudar;
- validacao de JSON/schema quando config mudar;
- dry-run antes de deploy quando houver suporte;
- `git diff --check` antes de finalizar.

Se nao puder rodar um check relevante, registre o motivo e o risco residual.

## 9. Finalizacao

Antes de responder ou marcar DONE:

- confirme `git status --short --branch`;
- liste arquivos alterados relevantes;
- registre testes/comandos executados;
- registre checks nao executados;
- indique pendencias, blockers ou follow-ups;
- confirme se docs/config/catalogos precisaram mudar ou por que nao.
