# DH Status Dashboard

Dashboard interativo para acompanhamento em tempo real do estado de **Epics e Stories** do projeto **DHCM (Digital Health)** em Jira.

🔗 **[Abrir Dashboard](https://claudiogualdino.github.io/dh-status-dashboard/)**

---

## O que é

Dashboard dark-theme self-contained (HTML único) com 3 tabs:

| Tab | Conteúdo |
|-----|----------|
| 📊 **Resumo** | KPI cards (Total Epics, Stories Open, Completion %) + breakdown por status |
| 🗂 **Epics** | Todos os epics agrupados por status, layout 2 colunas |
| 📝 **Stories** | Todas as stories agrupadas por status, com referência ao epic |

Os dados são carregados de `data.json` — um snapshot actualizado manualmente após cada sync com o Jira.

---

## Ficheiros do repositório

| Ficheiro | Descrição |
|----------|-----------|
| `index.html` | Dashboard completo self-contained (app shell) |
| `data.json` | Snapshot dos dados Jira (epics + stories + data de sync) |
| `README.md` | Este ficheiro |

---

## Como actualizar os dados (sync semanal)

O sync é feito através do **claude.ai** onde o MCP Atlassian está autorizado.

### Passo 1 — Abrir o dashboard no claude.ai

Numa conversa no Project **DH Operations**:
```
abre o dashboard
```
O artefacto HTML é entregue imediatamente.

### Passo 2 — Sync do Jira

1. Abre o artefacto
2. **Triple-click no logo FH** → Admin Mode (logo pisca vermelho)
3. Caixa **"1 · Sync do Jira"**
   - Opcional: activar toggle **"Incluir Closed/Done/Canceled"** se quiseres ver issues fechados
   - Clica **🔄 Sync do Jira**
   - Aguarda ~30–60s (pagina automaticamente todos os resultados)

### Passo 3 — Publicar

1. Caixa **"2 · Publicar no GitHub Pages"**
2. Clica **⬇ Download data.json**
3. Clica **🔗 Abrir repositório**
4. No GitHub: clica em `data.json` → ✏️ **Edit** → ⬆ **Upload file** → selecciona o `data.json` descarregado → **Commit changes**
5. Dashboard actualiza em ~1 minuto

---

## Funcionalidades

### Vista pública (sem login)
- Carrega `data.json` automaticamente ao abrir
- Navega entre tabs Resumo / Epics / Stories
- Clica em qualquer **key Jira** (ex: `DHCM-1234`) para abrir o issue directamente
- Cada story mostra o epic associado com link

### Admin Mode (triple-click no logo FH)
- 🔄 **Sync do Jira** — carrega dados frescos via MCP Atlassian *(apenas no claude.ai)*
- Toggle **Incluir Closed/Done/Canceled** — controla o que é importado do Jira
- ⬇ **Download data.json** — para publicar no GitHub
- 🔗 **Abrir repositório** — link directo para este repo
- Clicar no badge **🔒 Admin** sai do modo admin

### Ordenação dos estados

**Epics** (do mais avançado para o mais recuado):
`Ready For Go Live → Train Testing → QA Approved → QA Testing → On Hold → Implemented → In Progress → Ready For Development → In Technical Analysis → In Business Requirements → Pending Approval → To Do → Backlog → Cancelled → Closed`

**Stories** (do mais avançado para o mais recuado):
`Ready For Go Live → Train Testing → QA Approved → QA Testing → Ready For QA → On Hold → Dev Approved → Dev Testing → Dev Refused → Tech Review → Implemented → In Progress → Ready For Development → In Team Arch Analysis → In Technical Analysis → In Business Requirements → Backlog → Cancelled → Closed`

---

## Arquitectura técnica

```
index.html (self-contained)
├── Lê data.json do mesmo domínio (GitHub Pages)
├── Admin: chama Anthropic API + Atlassian MCP (apenas no claude.ai)
│   ├── fetchAllPages('epics')   → mcp_tool_result blocks → mapIssue()
│   └── fetchAllPages('stories') → mcp_tool_result blocks → mapIssue()
└── Dados extraídos dos blocos mcp_tool_result (não do texto do modelo)
```

**Porquê esta arquitectura:**
- GitHub Pages não pode chamar `api.anthropic.com` (CORS)
- O sync precisa do ambiente claude.ai onde a API key é injetada automaticamente
- `data.json` é o "bridge" entre o sync (claude.ai) e a visualização (GitHub Pages)

---

## Configuração inicial (primeira vez)

1. Fazer upload de `index.html` para a raiz do repositório
2. Activar **GitHub Pages**: Settings → Pages → Branch: `main` / `root`
3. Aguardar ~2 minutos → abre `https://claudiogualdino.github.io/dh-status-dashboard/`
4. No Admin Mode, preencher o campo **URL do repositório** com `https://github.com/claudiogualdino/dh-status-dashboard`  
   *(guardado em localStorage — só precisa ser feito uma vez)*

---

## Stack

- HTML/CSS/JS vanilla — zero dependências externas
- Fonte: [DM Sans + DM Mono](https://fonts.google.com/) (Google Fonts)
- Dark theme consistente com os restantes dashboards DH (DHCM Roadmap, AEGON PDS)
- Anthropic API: `claude-sonnet-4-20250514` com MCP `mcp.atlassian.com`

---

*Mantido por Claudio Gualdino · Future Healthcare · Digital Health Product*
