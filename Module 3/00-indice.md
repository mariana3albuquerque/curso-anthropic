# 📚 Índice — Module 3: Claude Code, MCP & Integration

> Este módulo foi dividido em 13 arquivos, um por tópico.

| # | Arquivo | Tópico |
|---|---|---|
| 1️⃣ | [`01-permission-modes-human-gates.md`](01-permission-modes-human-gates.md) | Explore/plan/code loop, os 6 modos de permissão, escopos de settings, posicionamento do gate humano |
| 2️⃣ | [`02-postmortem-bypass-checkpoint1.md`](02-postmortem-bypass-checkpoint1.md) | 🔬 Post-mortem: bypass mode que deletou arquivos de produção · 🧪 Checkpoint 1 |
| 3️⃣ | [`03-durable-project-context.md`](03-durable-project-context.md) | CLAUDE.md, rules instruction files, hooks (PreToolUse/PostToolUse/etc.), subagents |
| 4️⃣ | [`04-postmortem-claudemd-checkpoint2.md`](04-postmortem-claudemd-checkpoint2.md) | 🔬 Post-mortem: CLAUDE.md de 847 linhas que diluiu a regra crítica · 🧪 Checkpoint 2 |
| 5️⃣ | [`05-packaging-workflows.md`](05-packaging-workflows.md) | Skills (portabilidade entre Claude Code/API/SDK/Managed Agents), custom commands, plugins, marketplace |
| 6️⃣ | [`06-checkpoints-postmortem-plugin.md`](06-checkpoints-postmortem-plugin.md) | 🧪 Checkpoint 3 (skill por runtime) · 🔬 Post-mortem: plugin com path absoluto · 🧪 Checkpoint 4 |
| 7️⃣ | [`07-mcp-servers.md`](07-mcp-servers.md) | Servidores MCP, transporte (stdio/HTTP), escopo, prompt caching, RAG, permissões por tool, exemplo GitHub |
| 8️⃣ | [`08-postmortem-apikey-checkpoint5.md`](08-postmortem-apikey-checkpoint5.md) | 🔬 Post-mortem: API key vazada no .mcp.json committed · 🧪 Checkpoint 5 |
| 9️⃣ | [`09-enterprise-integration.md`](09-enterprise-integration.md) | Padrões de autenticação (OAuth/API key/filesystem), gestão de secrets, requisitos regulatórios, modernização de código |
| 🔟 | [`10-postmortem-oauth-checkpoint6.md`](10-postmortem-oauth-checkpoint6.md) | 🔬 Post-mortem: OAuth que funcionou em staging e falhou em produção · 🧪 Checkpoint 6 |
| 1️⃣1️⃣ | [`11-cumulative-integration-task.md`](11-cumulative-integration-task.md) | 🧪 Tarefa cumulativa: os 3 bugs (permissão + path + secret) e sua correção completa |
| 1️⃣2️⃣ | [`12-sete-conclusoes.md`](12-sete-conclusoes.md) | 🏁 Fechamento: as sete conclusões do módulo |
| 1️⃣3️⃣ | [`13-glossario.md`](13-glossario.md) | 📚 Glossário de termos-chave |

---

## 🗂️ Agrupamento por tema

| Tema | Arquivos |
|---|---|
| 🔐 **Permissões e gates humanos** | 01, 02 |
| 🗂️ **Contexto durável de projeto** | 03, 04 |
| 📦 **Empacotamento (skills/plugins)** | 05, 06 |
| 🌐 **Servidores MCP** | 07, 08 |
| 🏛️ **Integração enterprise** | 09, 10 |
| 🧪 **Avaliação cumulativa** | 11 |
| 🏁 **Fechamento** | 12, 13 |
