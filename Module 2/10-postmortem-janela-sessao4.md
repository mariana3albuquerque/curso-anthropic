# 🔬 Post-mortem: o Agente que Encheu a Janela na Sessão Quatro

> 🎯 **Ideia central:** o agente roda perfeitamente em desenvolvimento porque você o executa numa **única sessão longa e contínua** — a janela nunca enche, e a memória in-context segura tudo. Mas produção roda **múltiplas sessões mais curtas**, com mais turnos ao longo de mais dias — e a janela enche na **sessão quatro**.

---

## 🎬 Setup

Um agente foi construído para ajudar um engenheiro de suporte com casos de escalação contínuos.

```mermaid
flowchart LR
    A["🧪 Desenvolvimento<br/>1 sessão longa contínua<br/>10-15 turnos"] --> B["🟢 In-context memory<br/>segura tudo corretamente"]
    B --> C["🚢 Shipado SEM medir<br/>uso de tokens por sessão"]

    style A fill:#dbeafe,color:#000000
    style B fill:#dcfce7,color:#000000
    style C fill:#fee2e2,color:#000000
```

> <mark>O desenvolvedor shipou sem medir o uso de tokens por sessão.</mark> Em dev, o estado in-context guardava o histórico completo sem problema nenhum — porque nunca havia mais de uma sessão em jogo.

---

## 🧾 Post-mortem: estado in-context infla até fechar a janela

### 🏭 Em produção

Cada sessão era **mais curta**, mas o **estado acumulava entre sessões**.

```mermaid
flowchart TD
    S1["Sessão 1"] --> S2["Sessão 2"]
    S2 --> S3["Sessão 3"]
    S3 --> S4["Sessão 4<br/>📈 histórico injetado<br/>já passa de 40.000 tokens"]
    S4 --> X["🔴 Antes de processar<br/>UMA ÚNICA tool call"]

    style S1 fill:#dbeafe,color:#000000
    style S2 fill:#e0e7ff,color:#000000
    style S3 fill:#fef3c7,color:#000000
    style S4 fill:#fed7aa,color:#000000
    style X fill:#fee2e2,color:#000000
```

### 📊 A conta que estourou

| Componente | Tokens consumidos |
|---|---|
| 📜 Histórico in-context injetado (sessão 4) | **> 40.000** |
| ➕ System prompt + schemas de tools registradas | soma total **> 45.000** |
| ⏰ Momento em que isso já foi gasto | **antes** do primeiro turno produtivo da sessão |

> <mark>Mais de 45.000 tokens do orçamento de contexto foram consumidos antes do primeiro turno produtivo da sessão.</mark> Conforme as tool calls se acumulavam ao longo da sessão, o orçamento restante se esgotava antes do agente conseguir completar a análise.

### 🎭 O sintoma enganoso

```mermaid
flowchart LR
    A["📉 Orçamento esgotado<br/>no meio da sessão"] --> B["🔴 Agente retorna<br/>resultados incompletos"]
    B --> C["🤔 Parece falha de<br/>seleção de tool"]
    C -.->|"causa real"| D["🧠 Problema de arquitetura<br/>de memória"]

    style A fill:#fed7aa,color:#000000
    style B fill:#fecaca,color:#000000
    style C fill:#fef3c7,color:#000000
    style D fill:#fee2e2,color:#000000
```

> ⚠️ O agente começou a retornar resultados incompletos — um sintoma que, à primeira vista, <mark>parecia falha de seleção de tool, não um problema de arquitetura de memória.</mark>

---

## 🛠️ A correção

| Antes ❌ | Depois ✅ |
|---|---|
| Histórico acumulado de sessão vivendo no contexto ativo | Puxado para **storage externo** (banco de dados) |
| Toda a história injetada a cada sessão | Só o **subconjunto relevante** injetado no início da sessão |

> 💬 A correção em si foi um refactor de **uma hora** — mas <mark>sob pressão de produção, levou significativamente mais tempo do que levaria na fase de design.</mark> A camada de storage, a lógica de retrieval e a gestão de sessão exigiam decisões que deveriam ter sido tomadas **antes** do primeiro deploy.

```mermaid
flowchart LR
    A["🗄️ Camada de storage"] --> D["✅ Refactor completo"]
    B["🔍 Lógica de retrieval"] --> D
    C["📋 Gestão de sessão"] --> D

    style A fill:#e0e7ff,color:#000000
    style B fill:#e0e7ff,color:#000000
    style C fill:#e0e7ff,color:#000000
    style D fill:#dcfce7,color:#000000
```

---

## 🚨 O que observar

> <mark>Desenvolvimento usou uma única sessão longa. Produção usou muitas sessões curtas com estado acumulado. Essas são formas diferentes — e a memória in-context lida com cada uma de um jeito diferente.</mark>

✅ **Regra prática:** meça o tamanho esperado do estado por sessão (histórico + system prompt + schemas de tools) contra o limite de contexto **antes** de escolher in-context como o padrão.

### 📋 Checklist de prevenção

- [ ] O formato dos testes de desenvolvimento reflete o formato real de uso em produção (sessões curtas e numerosas vs. uma sessão longa)?
- [ ] Medi o tamanho do histórico acumulado **entre sessões**, não só dentro de uma sessão?
- [ ] Somei system prompt + schemas de tools + histórico injetado para saber o custo real **antes** do primeiro turno produtivo?
- [ ] Se notar resultados incompletos ao longo de várias sessões, minha primeira suspeita é a janela de contexto — antes de investigar seleção de tool?
- [ ] A decisão de escopo de memória (in-context vs. external storage vs. summarized) foi tomada na fase de **design**, não vai virar um refactor de emergência depois?
