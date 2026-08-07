# 🔬 Post-mortem: o Batch Job que Não Era um Batch de Verdade

> 🎯 **Ideia central:** dividir um job em pedaços e processá-los um depois do outro **não é batching** — é serialização com passos extras. A Message Batches API existe justamente porque fazer loop sobre inputs contra a API síncrona esbarra em rate limits assim que o volume fica real, **não importa como você fatie a lista de input.**

---

## 💬 A conversa que revelou o problema de verdade

Um desenvolvedor vinha re-executando o mesmo job noturno de classificação havia três noites, sempre batendo em erros de rate limit no mesmo ponto. A pergunta certa do dev sênior expôs a causa raiz.

```mermaid
sequenceDiagram
    participant Dev as 👨‍💻 Desenvolvedor
    participant Sr as 🧑‍🏫 Dev Sênior

    Dev->>Sr: "Meu job noturno bate rate limit.<br/>Já dividi em chunks menores. O que mais eu faço?"
    Sr->>Dev: "Como você está submetendo?"
    Dev->>Sr: "Faço loop sobre a lista e chamo<br/>a API para cada item."
    Sr->>Dev: "❌ Isso não é batching.<br/>São chamadas seriais contra o endpoint síncrono."
    Note over Sr,Dev: Dividir a lista em chunks não muda<br/>o que a API vê — ela ainda vê<br/>uma requisição por item, uma atrás da outra
    Dev->>Sr: "Então o rate limit dispara porque<br/>estou fazendo milhares de chamadas síncronas?"
    Sr->>Dev: "✅ Exato."
    Sr->>Dev: "A Message Batch API aceita até<br/>100.000 requisições OU 256 MB por chamada,<br/>retorna um batch_id, processa assíncrono"
    Dev->>Sr: "E o tradeoff?"
    Sr->>Dev: "Latência não-determinística — pode levar horas.<br/>Errado para tempo real, perfeito para job noturno."
```

---

## 🕵️ Onde estava o erro de raciocínio

> <mark>Dividir uma lista em chunks e fazer loop contra a API síncrona não é batching, mesmo que pareça que deveria ser.</mark> Isso produz **exatamente o mesmo número** de chamadas de API que a versão sem chunks, e esbarra nos **mesmos rate limits**.

```mermaid
flowchart TD
    A["📋 Lista de 10.000 itens"] --> B{"Como submeter?"}
    B -->|"❌ 'Batching' errado"| C["Divide em chunks de 100<br/>+ loop chamando API síncrona<br/>100 vezes"]
    C --> D["🔴 API ainda vê 10.000<br/>requisições individuais,<br/>uma atrás da outra"]
    D --> E["💥 Mesmo rate limit<br/>de antes"]

    B -->|"✅ Batching de verdade"| F["Message Batches API<br/>1 chamada de batch"]
    F --> G["🆔 batch_id retornado"]
    G --> H["✅ Processamento assíncrono,<br/>sem disparar rate limit"]

    style A fill:#e0e7ff,color:#000000
    style B fill:#fff7cd,color:#000000
    style C fill:#fecaca,color:#000000
    style D fill:#fee2e2,color:#000000
    style E fill:#fee2e2,color:#000000
    style F fill:#dcfce7,color:#000000
    style G fill:#dcfce7,color:#000000
    style H fill:#dcfce7,color:#000000
```

> <mark>A Message Batches API é um modelo de submissão diferente — não um "batch size" menor.</mark> Fatiar a lista não muda o modelo de submissão; só muda o tamanho de cada fatia que ainda é enviada uma por uma.

---

## ⚖️ Comparativo: "batching" falso vs. batching de verdade

| | ❌ Loop + chunks contra API síncrona | ✅ Message Batches API |
|---|---|---|
| 🔢 Número de chamadas que a API vê | Uma por item — igual à versão sem chunks | Uma única chamada de batch |
| 🚦 Risco de rate limit | Alto — dispara do mesmo jeito | Não dispara — não são requisições individuais |
| 💰 Custo por token | Preço padrão (síncrono) | Menor |
| ⏱️ Latência | Imediata por chamada, mas serializada | Não-determinística — pode levar horas |
| 🎯 Adequado para | Interação em tempo real com usuário esperando | Workload offline, alto volume |

---

## 🧾 Como funciona a Message Batches API

| Passo | O que acontece |
|---|---|
| 1️⃣ Submissão | Você envia até **100.000 requisições ou 256 MB** por chamada de batch (o que vier primeiro) |
| 2️⃣ Retorno | A API devolve um **`batch_id`** |
| 3️⃣ Polling | Seu código checa o status do batch periodicamente, num cronograma, até a API informar que terminou |
| 4️⃣ Resultado | Você baixa os resultados quando prontos |

> ⚠️ <mark>Os resultados retornam em ordem arbitrária — não na ordem em que as requisições foram submetidas.</mark> Use o campo `custom_id` em cada requisição para casar os resultados de volta com os inputs correspondentes.

---

## 🚨 O que observar

> ✅ **Regra prática:** use a Message Batches API sempre que o workload for de **alto volume e offline**. Recorra à API síncrona **só** quando houver um usuário esperando do outro lado.

### 📋 Checklist de prevenção

- [ ] Estou usando a **Message Batches API de verdade**, ou só fatiando uma lista e fazendo loop contra o endpoint síncrono?
- [ ] Meu job é realmente offline (sem usuário esperando em tempo real)?
- [ ] Estou usando `custom_id` em cada requisição do batch para reconciliar os resultados, já que eles voltam fora de ordem?
- [ ] Se estou batendo rate limit repetidamente com "chunks", já verifiquei se ainda são chamadas individuais contra a API síncrona por trás?
