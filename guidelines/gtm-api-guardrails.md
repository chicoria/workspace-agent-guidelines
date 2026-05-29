# GTM API — Guardrails operacionais

> Aplicar sempre que um agente precisar de ler, auditar ou configurar containers GTM Web ou sGTM via Tag Manager API v2.

## Regra 1 — Ler sempre a versão publicada antes de qualquer acção

**Nunca usar o endpoint de workspace para auditar o estado de produção.**

| Endpoint | O que retorna | Quando usar |
|---|---|---|
| `GET .../workspaces/{id}/tags` | Rascunho (alterações não publicadas) | Só para ver o que está em edição |
| `GET .../versions:live` | **Versão publicada em produção** | **Sempre** que precisar saber o que está activo |
| `GET .../versions` | Histórico de versões publicadas | Auditoria / rollback |

```bash
# ERRADO — mostra rascunho, pode estar vazio mesmo com produção configurada
GET /tagmanager/v2/accounts/{a}/containers/{c}/workspaces/{w}/tags

# CORRECTO — mostra o que está realmente em produção
GET /tagmanager/v2/accounts/{a}/containers/{c}/versions:live
```

**Gotcha confirmado em produção (2026-05-29):** o container sGTM `241313282` (workspace 16) aparecia vazio via endpoint de workspace, mas a versão live v19 tinha 2 tags, 2 clients, 1 trigger e 8 variáveis em produção.

## Regra 2 — Sequência obrigatória para qualquer configuração GTM

1. **Ler** `versions:live` — inventariar o que existe (tags, triggers, vars, clients).
2. **Identificar** o workspace de trabalho activo (`workspaces` → workspace com `description` de trabalho, ou o Default).
3. **Aplicar** mudanças no workspace (criar/editar tags, triggers, vars).
4. **Rever** diff do workspace antes de publicar (`workspaces/{id}/status`).
5. **Publicar** uma nova versão (nunca editar directamente a versão live).
6. **Confirmar** com `versions:live` que a nova versão está activa.
7. **Exportar** o container actualizado para JSON versionado no repo (`trafego/gtm/`).

Nunca publicar sem rever o diff. Nunca assumir que o workspace reflecte produção.

## Regra 3 — GTM Web vs sGTM: funções distintas

| Container | Função | O que vive aqui |
|---|---|---|
| **GTM Web** (`GTM-XXXXXXX`) | Browser-side | Variáveis DL, triggers custom event, tags GA4 Event, tags Pixel browser |
| **sGTM** (server container) | Server-side | Clients (GA4, GA4 MP), triggers, tag GA4 re-forward, tag Meta CAPI |

Antes de criar uma tag, confirmar em qual container ela pertence:
- Eventos `dataLayer.push` → **GTM Web** (tags `gaawe`, triggers `customEvent`)
- Encaminhamento server-side para GA4 / Meta CAPI → **sGTM** (trigger no sGTM, não no Web)
- O sGTM recebe de dois caminhos: (A) GTM Web → sGTM e (B) Worker → sGTM directamente

## Regra 4 — Lookup Tables no sGTM: nunca hardcode

O sGTM multi-tenant usa Lookup Tables para resolver `tenant|produto → pixelId / token / measurementId`. Ao adicionar suporte a um novo tenant ou produto:
- **Editar as Lookup Tables existentes** — não criar tags duplicadas por tenant.
- Nunca hardcode de IDs ou tokens em tags — usar sempre as variáveis `LT - *`.
- Confirmar que as LTs cobrem o novo `host` / `produto` antes de publicar.

## Regra 5 — Autenticação via Service Account

O `GOOGLE_SERVICE_ACCOUNT_JSON` no `.env.local` pode estar num formato inválido (string escapada vs JSON raw). Sempre verificar:

```bash
# Verificar qual credencial está disponível
# 1. GOOGLE_APPLICATION_CREDENTIALS → path para ficheiro JSON (mais fiável)
# 2. GOOGLE_SERVICE_ACCOUNT_JSON → string JSON inline (verificar se é JSON válido)
node -e "JSON.parse(process.env.GOOGLE_SERVICE_ACCOUNT_JSON)" 2>&1
```

Se `GOOGLE_SERVICE_ACCOUNT_JSON` falhar, usar `GOOGLE_APPLICATION_CREDENTIALS` (path para ficheiro).

**Scopes necessários:**
- Leitura GTM + GA4: `https://www.googleapis.com/auth/tagmanager.readonly https://www.googleapis.com/auth/analytics.readonly`
- Escrita GTM: `https://www.googleapis.com/auth/tagmanager.edit.containers`
- GA4 Admin (dimensões): `https://www.googleapis.com/auth/analytics.edit`
