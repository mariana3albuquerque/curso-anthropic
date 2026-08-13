# 📚 Índice — Module 4: Production Engineering, Evals, and Security

> Este módulo foi dividido em 14 arquivos, um por tópico.

| # | Arquivo | Tópico |
|---|---|---|
| 1️⃣ | [`01-evals-judges.md`](01-evals-judges.md) | Design document, pipeline de eval, métodos de grading (exact/code/judge), calibração de judge, cobertura |
| 2️⃣ | [`02-postmortem-evals-checkpoint.md`](02-postmortem-evals-checkpoint.md) | 🔬 Post-mortem: extração de data errada (falta de caso graded) · 🧪 Checkpoint |
| 3️⃣ | [`03-testing-tracing.md`](03-testing-tracing.md) | Os 4 níveis de teste (unit/functional/integration/e2e), tracing, roteamento fetch-once vs. agentic search |
| 4️⃣ | [`04-postmortem-testing-checkpoint.md`](04-postmortem-testing-checkpoint.md) | 🔬 Post-mortem: seam de integration quebrado · 🧪 Checkpoint |
| 5️⃣ | [`05-failure-handling.md`](05-failure-handling.md) | Retriable vs. terminal, retries do SDK, `retry-after`, `is_error` em tool results |
| 6️⃣ | [`06-postmortem-failure-checkpoint.md`](06-postmortem-failure-checkpoint.md) | 🔬 Post-mortem: primeiro rate-limit derrubou a requisição · 🧪 Checkpoint (conserto de retry) |
| 7️⃣ | [`07-model-selection.md`](07-model-selection.md) | Tiers de modelo (Fable/Opus/Sonnet/Haiku), trade-off custo/latência/qualidade, roteamento · 🧪 Checkpoint |
| 8️⃣ | [`08-cost-orchestration.md`](08-cost-orchestration.md) | Instrumentação, alavancas de custo, streaming com tools, prompt caching, Batches API, orchestrator-worker, piso de confiabilidade |
| 9️⃣ | [`09-postmortem-cost-checkpoint.md`](09-postmortem-cost-checkpoint.md) | 🔬 Post-mortem: fan-out que triplicou a conta · 🧪 Checkpoint |
| 🔟 | [`10-security.md`](10-security.md) | Prompt injection, jailbreaks, menor privilégio, hooks, sandboxing OS-level, scoping regulatório |
| 1️⃣1️⃣ | [`11-postmortem-security-checkpoint.md`](11-postmortem-security-checkpoint.md) | 🔬 Post-mortem: página buscada que deu as ordens · 🧪 Checkpoint (config segura mínima) |
| 1️⃣2️⃣ | [`12-cumulative-hardening-task.md`](12-cumulative-hardening-task.md) | 🧪 Tarefa cumulativa: os 3 defeitos (segurança + failure handling) e correção completa |
| 1️⃣3️⃣ | [`13-cinco-conclusoes.md`](13-cinco-conclusoes.md) | 🏁 Fechamento: as cinco conclusões do módulo |
| 1️⃣4️⃣ | [`14-glossario.md`](14-glossario.md) | 📚 Glossário de termos-chave |

---

## 🗂️ Agrupamento por tema

| Tema | Arquivos |
|---|---|
| 📊 **Evals e judges** | 01, 02 |
| 🔍 **Testing e tracing** | 03, 04 |
| 🛡️ **Failure handling** | 05, 06 |
| 🎚️ **Seleção de modelo** | 07 |
| 💰 **Custo e orquestração** | 08, 09 |
| 🔒 **Segurança** | 10, 11 |
| 🧪 **Avaliação cumulativa** | 12 |
| 🏁 **Fechamento** | 13, 14 |
