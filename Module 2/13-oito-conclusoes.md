# 🏁 Oito Conclusões — Uma por Objetivo de Aprendizagem

> 🎯 Este módulo estabeleceu a **biblioteca de primitivos do Developer**, com cinco tipos de interação que sustentam todos os módulos seguintes. As oito conclusões abaixo amarram tudo que vimos: prompting, schemas de tools, engenharia de contexto, construção de agentes, escopo de memória e ingestão multimodal.

---

## 1️⃣ Quando um prompt falha, o tipo de falha diz qual técnica está faltando

```mermaid
flowchart LR
    A["📐 Output no formato errado"] --> A1["🔧 Falta: Output constraint"]
    B["🌊 Drift entre turnos"] --> B1["🔧 Falta: System prompt<br/>mais específico"]
    C["🎭 Estrutura alucinada"] --> C1["🔧 Falta: Few-shot examples"]

    style A fill:#fee2e2,color:#000000
    style A1 fill:#dcfce7,color:#000000
    style B fill:#fef3c7,color:#000000
    style B1 fill:#dcfce7,color:#000000
    style C fill:#e0e7ff,color:#000000
    style C1 fill:#dcfce7,color:#000000
```

> <mark>O instinto de reescrever a instrução e tentar de novo raramente funciona, porque nenhuma dessas falhas é um problema de fraseado.</mark> Diagnostique o tipo de falha primeiro, depois adicione a técnica correspondente.

📌 Quando instruções no nível do prompt não bastam — porque inputs não testados ainda quebram o parser — mova o controle de output para a **API** com **structured outputs**:
- 🧾 **JSON outputs** restringem a resposta final contra um schema
- 🔗 **Strict tool use** valida os argumentos que Claude passa às suas tools

⚠️ Custo: latência de compilação na primeira chamada + tokens de input adicionais.

---

## 2️⃣ Ajuste a profundidade de raciocínio à tarefa antes de ajustar o prompt

> ✅ Ative extended thinking **só** onde uma passada de raciocínio muda a resposta, e calibre o nível de esforço ao problema — em vez de aumentar em toda chamada por padrão.

> <mark>Blocos `thinking` precisam voltar à API sem alterações, ou a próxima requisição falha.</mark>

> ℹ️ Escolher **qual modelo** rodar é uma decisão distinta de **se** habilitar reasoning — isso é ensinado no módulo de MSO Foundations, anterior a este.

---

## 3️⃣ Um stream terminar não é o mesmo que uma mensagem estar completa

```mermaid
flowchart LR
    A["🌊 Streaming"] --> B["⚡ Ganha latência percebida"]
    A --> C["⚠️ Custo: você mesmo monta<br/>a resposta a partir de eventos parciais"]

    style A fill:#e0e7ff,color:#000000
    style B fill:#dcfce7,color:#000000
    style C fill:#fef3c7,color:#000000
```

**Regras de ouro:**
- ✅ Aja sobre um bloco **só depois** que ele fecha
- ✅ Só commite um turno ao histórico **depois** do `message_stop`
- ✅ Num stream interrompido: **descarte** o turno parcial e refaça a requisição

> <mark>O modo de falha a reconhecer: um erro de tool-use num retry que remonta a um bloco pela metade de um stream que caiu — não ao schema.</mark>

---

## 4️⃣ Toda seleção de tool errada remonta ao schema — e na maioria das vezes, à descrição

> 💬 Claude escolhe uma tool lendo o campo `description` e comparando com o pedido do usuário — o que significa que **duas tools que dizem "use isto para encontrar informação" são indistinguíveis do ponto de vista de Claude**, mesmo que os input schemas sejam completamente diferentes.

```mermaid
flowchart TD
    A["🐛 Bug de seleção errada"] --> B["📝 Adicionar CONDIÇÃO DE EXCLUSÃO<br/>à descrição"]
    B --> C["✅ Uma frase que resolve<br/>a maioria dos bugs"]

    style A fill:#fee2e2,color:#000000
    style B fill:#fef3c7,color:#000000
    style C fill:#dcfce7,color:#000000
```

> <mark>A frase que resolve a maioria dos bugs de tool errada é a condição de exclusão: uma linha em toda descrição nomeando quando NÃO chamar a tool</mark> — escrita no design, não depois que a primeira chamada errada aparece num log.

🌐 Quando alguém já escreveu as tools por você, **MCP** permite conectar a um servidor mantido em vez de autorar cada schema à mão — mas cada servidor conectado adiciona suas definições de tools à janela de contexto, **usadas ou não**. Conecte deliberadamente e controle o custo de carregamento.

---

## 5️⃣ Contexto é um orçamento fixo, e tool outputs o gastam mais rápido que qualquer outra coisa no loop

```mermaid
flowchart LR
    A["🧪 Dev: fixtures pequenas<br/>50 turnos limpos"] --> B["🏭 Produção: tool outputs<br/>3-5x mais longos"]
    B --> C["🔴 Teto atingido<br/>no turno 8"]

    style A fill:#dbeafe,color:#000000
    style B fill:#fef3c7,color:#000000
    style C fill:#fee2e2,color:#000000
```

| Estratégia | O que compra de volta |
|---|---|
| ✂️ **Pruning** | Headroom, descartando o que veio depois de um ponto |
| 🗜️ **Compaction** | Headroom, condensando o que já foi aprendido |
| 🤖 **Subagent handoffs** | Headroom, isolando exploração num contexto separado |

> ✅ Qual aplicar depende de **se você ainda precisa do estado anterior**.

> <mark>Quando a seleção de tool começa a degradar depois de um número fixo de turnos, a janela é o primeiro lugar a olhar — não o schema.</mark>

---

## 6️⃣ A decisão workflow-ou-agente define o custo de tudo que vem depois, e checkpoints humanos pertencem ao design

```mermaid
flowchart TD
    Q["Consigo escrever os<br/>passos exatos em código?"] -->|"✅ Sim"| W["🔧 Workflow"]
    Q -->|"❌ Não"| A["🤖 Agente"]

    style Q fill:#fef3c7,color:#000000
    style W fill:#dbeafe,color:#000000
    style A fill:#e0e7ff,color:#000000
```

| Escolha errada | O que acontece |
|---|---|
| 🤖 Agente onde workflow bastaria | Custo de contexto extra + comportamento que vive só em transcripts |
| 🔧 Workflow onde agente é necessário | Quebra na **primeira vez** que um input foge do caminho previsto |

> <mark>Se uma tool pode tomar uma ação irreversível, o checkpoint de human-in-the-loop entra ANTES do loop ser montado — não depois que a primeira escrita chega a um ambiente de cliente.</mark>

---

## 7️⃣ O escopo de memória é decidido pela forma da sessão, não pelo que é mais fácil de implementar

> 💬 In-context memory é o padrão mais simples de escrever — <mark>é também o que falha mais cedo, quando sessões de produção acabam sendo mais curtas e numerosas do que as sessões longas e contínuas usadas em desenvolvimento.</mark>

| Padrão | Trade-off |
|---|---|
| 💬 In-context | Simples, mas falha cedo em sessões curtas/numerosas |
| 🗄️ External storage | Adiciona latência, mas o estado sobrevive entre sessões |
| 📝 Summarized | Reduz custo, mas perde o que o summarizer não preservou |
| 🚫 Stateless | Correto para jobs que completam e encerram |

```mermaid
flowchart LR
    A["⏰ Refactor sob pressão<br/>de produção"] --> A1["~1 hora"]
    B["🎯 Mesma decisão,<br/>na fase de design"] --> B1["~20 minutos"]

    style A fill:#fee2e2,color:#000000
    style A1 fill:#fecaca,color:#000000
    style B fill:#dcfce7,color:#000000
    style B1 fill:#dcfce7,color:#000000
```

> ℹ️ Carregar **instruções repetíveis** entre tarefas é um problema separado de carregar **estado** — o padrão para isso é uma **Skill**: um arquivo markdown que Claude carrega sob demanda, comparando sua descrição, em vez de instruções injetadas em toda sessão.

---

## 8️⃣ Calcule o custo de um input multimodal antes de escrever o código de ingestão, e case a API com o workload

**Fórmula:** `⌈largura / 28⌉ × ⌈altura / 28⌉` tokens visuais — o teto por imagem varia por tier de modelo.

> <mark>Um original em alta resolução nos modelos mais novos pode custar muitas vezes o que um thumbnail custa no seu conjunto de teste</mark> — a fórmula precisa rodar contra o **maior input esperado em produção**, não os inputs que você tem à mão.

| Método de envio | Melhor para |
|---|---|
| 🔡 Inline base64 | Imagens pontuais |
| 📁 Files API | Assets reutilizados entre requisições |
| 📦 Message Batches API | Trabalho offline, custo por token menor, latência não-determinística |

> ⚠️ <mark>O erro que vale a pena evitar: chamar a API síncrona dentro de um loop e tratar isso como batching.</mark>

---

## 🗺️ Mapa geral das oito conclusões

```mermaid
flowchart TD
    T1["1️⃣ Diagnostique o tipo<br/>de falha do prompt"] --> T2["2️⃣ Case profundidade<br/>de raciocínio à tarefa"]
    T2 --> T3["3️⃣ Stream ≠ mensagem<br/>completa"]
    T3 --> T4["4️⃣ Seleção errada de tool<br/>= schema/descrição"]
    T4 --> T5["5️⃣ Contexto é orçamento<br/>fixo — tool outputs gastam rápido"]
    T5 --> T6["6️⃣ Workflow ou agente decide<br/>o custo de tudo depois"]
    T6 --> T7["7️⃣ Escopo de memória segue<br/>a forma da sessão"]
    T7 --> T8["8️⃣ Calcule custo multimodal<br/>antes de ingerir"]

    style T1 fill:#e0e7ff,color:#000000
    style T2 fill:#dbeafe,color:#000000
    style T3 fill:#fef3c7,color:#000000
    style T4 fill:#fed7aa,color:#000000
    style T5 fill:#fecaca,color:#000000
    style T6 fill:#fde68a,color:#000000
    style T7 fill:#bbf7d0,color:#000000
    style T8 fill:#ddd6fe,color:#000000
```

---

> 🔮 **O que vem a seguir:** este módulo estabeleceu a **biblioteca de primitivos do Developer**, com cinco tipos de interação que todos os módulos Developer seguintes vão usar. Os padrões vistos aqui — arte de prompting, schemas de tools, engenharia de contexto, construção de agentes, escopo de memória e ingestão multimodal — formam a fundação para todo módulo que vem depois.
