# 🏁 Sete Conclusões-Chave — Uma por Seção

> 🎯 Uma conclusão por seção, amarrando o módulo inteiro.

---

## 1️⃣ Modo de permissão é uma decisão de risco, não de velocidade

> 💬 O Claude Code oferece modos de "prompt-antes-de-tudo" a "prompt-para-nada". O modo de permissão deve bater com o **perfil de risco** do trabalho e do ambiente, não com a preferência por menos prompts.

```mermaid
flowchart LR
    A["🔴 Bypass numa workstation<br/>contra codebase ao vivo"] --> A1["Remove TODO checkpoint<br/>entre o agente e seus arquivos"]
    B["✅ Deny rule no path<br/>que não pode ser tocado"] --> B1["Cobre a lacuna que<br/>um modo sozinho não cobre"]

    style A fill:#fee2e2,color:#000000
    style A1 fill:#fecaca,color:#000000
    style B fill:#dcfce7,color:#000000
    style B1 fill:#d1fae5,color:#000000
```

---

## 2️⃣ Uma revisão de código de IA te dá um conjunto de achados para triar, não um veredito para aplicar

> ✅ Confie nos achados que o revisor consegue **provar a partir do diff** na sua frente, como uma checagem null faltando ou um recurso não fechado — e confirme nas linhas citadas. <mark>Trate qualquer alegação sobre comportamento em runtime ou outro sistema como uma hipótese a testar</mark>, porque o revisor fez essa alegação sem a evidência que a provaria.

> 🙋 Coloque o gate humano no ponto onde um achado vira uma ação difícil de reverter, e aumente a precisão do revisor dando a ele as convenções que, do contrário, teria que adivinhar.

---

## 3️⃣ Uma skill é portátil, mas "roda em todo lugar" é algo que você precisa projetar

```mermaid
flowchart LR
    A["📄 Mesmo SKILL.md"] --> B["💻 Claude Code<br/>descoberta via filesystem"]
    A --> C["🌐 Messages API<br/>beta headers + container"]
    A --> D["📦 Agent SDK<br/>settingSources"]

    style A fill:#e0e7ff,color:#000000
    style B fill:#fef3c7,color:#000000
    style C fill:#fed7aa,color:#000000
    style D fill:#dbeafe,color:#000000
```

> <mark>Uma skill escopada a uma descrição clara e livre de suposições de ambiente local porta de forma limpa; uma que assume o terminal onde foi escrita, não.</mark> Em todo runtime, subagentes começam limpos — não pré-carregam skills automaticamente.

---

## 4️⃣ Contexto durável exige o mecanismo certo para cada preocupação

| Mecanismo | Papel |
|---|---|
| 📄 **CLAUDE.md** | Memória de projeto persistente por sessão, mas **dilui com tamanho** |
| 🎯 **Rules files** | Escopam orientação a onde ela se aplica |
| 🪝 **Hooks** | Aplicam guardrails **deterministicamente**, não probabilisticamente |
| 🤖 **Subagents** | Mantêm trabalho de exploração fora do contexto principal |

> ⚠️ <mark>Esses quatro mecanismos resolvem problemas diferentes — forçar todos dentro do CLAUDE.md produz um único arquivo mais difícil de manter e mais fácil de ignorar.</mark>

---

## 5️⃣ Um setup compartilhável exige componentes portáteis

> 💬 Um plugin que referencia um path absoluto para o diretório home do autor vai instalar numa máquina e falhar em todas as outras.

✅ **Regra prática:** skills, hooks e componentes de plugin que serão compartilhados devem referenciar paths **relativos à raiz do projeto**, e toda exigência de variável de ambiente deve ser documentada ou validada no install. <mark>Teste o install numa máquina limpa antes de distribuir.</mark>

---

## 6️⃣ Transporte e escopo são decisões independentes com consequências dependentes

```mermaid
flowchart TD
    A["🖥️ stdio"] --> A1["Servidores na SUA máquina"]
    B["🌐 HTTP"] --> B1["Qualquer coisa hospedada<br/>remotamente ou acessada<br/>por múltiplos devs"]

    C["🔒 Local"] --> C1["Servidor pessoal"]
    D["📁 Project"] --> D1["Compartilha com o repo<br/>via .mcp.json"]

    style A fill:#e0e7ff,color:#000000
    style B fill:#fef3c7,color:#000000
    style C fill:#dbeafe,color:#000000
    style D fill:#dcfce7,color:#000000
```

> <mark>A combinação precisa bater com a intenção de deployment: um servidor de time compartilhado exige transporte HTTP e escopo project ou enterprise. Um servidor stdio no `.mcp.json` é uma configuração que parece compartilhável mas não é.</mark>

---

## 7️⃣ Integração enterprise exige identificar os requisitos de segurança antes do deployment

> 💬 Um cliente regulado pergunta sobre identidade, residência de dados, logging de acesso e controle de configuração. As respostas vêm de:

| Requisito | Resposta |
|---|---|
| 🔐 Identidade de usuário | OAuth |
| 🔑 Identidade de serviço | Variáveis de ambiente |
| 📋 Logging de acesso | Hooks `PostToolUse` |
| 🔒 Trava de configuração | Enterprise managed settings |

> <mark>Nenhum desses é difícil de implementar, mas todos são difíceis de retrofit depois que um deployment de produção falhou numa revisão de segurança.</mark>

---

## 🗺️ Mapa geral das sete conclusões

```mermaid
flowchart TD
    T1["1️⃣ Modo de permissão =<br/>decisão de risco"] --> T2["2️⃣ Revisão de IA = achados<br/>para triar, não veredito"]
    T2 --> T3["3️⃣ Skill portátil exige<br/>design deliberado"]
    T3 --> T4["4️⃣ Contexto durável precisa<br/>do mecanismo certo por caso"]
    T4 --> T5["5️⃣ Setup compartilhável exige<br/>componentes portáteis"]
    T5 --> T6["6️⃣ Transporte + escopo<br/>= decisões que interagem"]
    T6 --> T7["7️⃣ Enterprise exige requisitos<br/>de segurança ANTES do deploy"]

    style T1 fill:#e0e7ff,color:#000000
    style T2 fill:#dbeafe,color:#000000
    style T3 fill:#fef3c7,color:#000000
    style T4 fill:#fed7aa,color:#000000
    style T5 fill:#fecaca,color:#000000
    style T6 fill:#fde68a,color:#000000
    style T7 fill:#bbf7d0,color:#000000
```

---

> 🔮 **O que vem a seguir:** o Módulo 4 cobre engenharia de produção, avaliações e segurança — como medir se suas integrações Claude Code funcionam corretamente em escala, como construir harnesses de avaliação, e como projetar guardrails de segurança de nível de produção. Os modos de permissão, hooks e padrões de autenticação deste módulo são a fundação contra a qual essas avaliações testam.
