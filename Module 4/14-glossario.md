# 📚 Termos-chave deste Módulo

> 🔤 Em ordem alfabética.

### 🕵️ Agentic search

Deixar o modelo emitir suas próprias queries, ler os resultados, e refinar através de várias rodadas, em vez de buscar um conjunto fixo de contexto uma única vez. Lida com perguntas multi-etapa e corpora mutáveis a um custo maior de token/latência, e evita a obsolescência e infraestrutura de um índice mantido.

### 📊 Eval

Um conjunto de casos de input, comportamentos esperados, e notas que define o que uma feature deve fazer antes de lançar. Rodar um eval produz um score num conjunto holdout, que transforma "pronto" de um julgamento numa medida rastreável conforme você muda o prompt, as tools, ou o modelo.

### ⏱️ Exponential backoff

Uma estratégia de retry que espera um intervalo crescente entre tentativas, até um teto e um número fixo de tentativas, geralmente com jitter aleatório. Previne que retries imediatos aprofundem um rate limit, e honra um valor `retry-after` quando a resposta o fornece.

### 🪝 Hook-based guardrail

Uma checagem que roda num ponto fixo do ciclo de vida do agente Claude Code, como `PreToolUse` antes de uma tool call, e pode bloquear uma ação e logá-la. <mark>Diferente de uma instrução em prompt, um hook é um controle aplicado que roda antes da ação protegida</mark> — a distinção que uma revisão regulada valoriza.

### 🔗 Integration test

Um teste que exercita o seam onde dois componentes fazem handoff, como output de retrieval passado para uma chamada de modelo. Captura as falhas silenciosas que testes unit e functional perdem, porque cada componente pode passar sozinho enquanto o handoff entre eles está errado.

### 🧑‍⚖️ LLM-as-judge

Um método de grading que usa uma segunda chamada de modelo com uma rubrica para pontuar outputs abertos que nenhuma regra de código consegue checar. Retorna um score com raciocínio, e só é confiável depois que você o calibra contra casos rotulados por humano e mede concordância.

### 🎭 Orchestrator-worker pattern

Uma forma multi-agente onde um agente lead planeja uma tarefa, gera subagents que trabalham em paralelo, cada um com seu próprio contexto, e compila os resultados deles. Ajuda em tarefas amplas que se dividem em partes independentes, a aproximadamente **quinze vezes** o custo de token de um único chat, no caso relatado pela Anthropic.

### 💉 Prompt injection

Um ataque onde instruções escondidas dentro de conteúdo que o agente busca são tratadas como comandos, porque o modelo lê todo seu contexto como um único stream, sem fronteira embutida entre instrução confiável e dado não-confiável. A defesa é tratar conteúdo buscado como dado e aplicar a fronteira de ação fora do prompt.

### ⚖️ Retriable versus terminal error

A primeira distinção para qualquer falha de produção. Um erro **retriable**, como um rate limit ou overload, é provável de ter sucesso numa tentativa posterior e recebe backoff. Um erro **terminal**, como uma bad request, falhará de novo identicamente e deve falhar rápido em vez de desperdiçar o orçamento de retry.
