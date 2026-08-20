# 🔬 Post-mortem: o Seam que Ninguém Marcou como Fronteira

> 🎯 **Setup:** você conectou componentes que cada um passou nos próprios testes. As partes já estavam checadas, e conectar partes verificadas parece seguro. Cada uma era confiável isoladamente. A lacuna: um seam entre duas partes confiáveis não é automaticamente confiável ele mesmo.

---

## 💬 A sessão

```mermaid
sequenceDiagram
    participant A as 👨‍💻 Dev A
    participant B as 👩‍💻 Dev B

    A->>B: "Os três componentes passam nos próprios testes.<br/>Só conectei eles."
    B->>A: "Para onde a tarefa do Claude Code manda<br/>o que buscou?"
    A->>B: "Direto pra próxima chamada, como parte do prompt.<br/>É só o conteúdo que a gente puxou da página do cliente."
    B->>A: "Esse conteúdo é NÃO-CONFIÁVEL. Se carrega<br/>instruções, o próximo componente as roda,<br/>porque a gente NUNCA marcou esse seam<br/>como uma fronteira."
    A->>B: "Mas cada componente era confiável sozinho."
    B->>A: "Certo, e o seam entre eles não era. Esse é o que<br/>ninguém tratou como fronteira — então conteúdo<br/>buscado cruza como instrução."
```

---

## 🕳️ Onde estava a falha

```mermaid
flowchart TD
    A["✅ Componente 1<br/>passa nos testes"] --> S["🔗 SEAM"]
    B["✅ Componente 2<br/>passa nos testes"] --> S
    S --> C{"Seam marcado<br/>como fronteira?"}
    C -->|"❌ Não"| D["🔴 Conteúdo buscado cruza<br/>como INSTRUÇÃO, não dado"]
    C -->|"✅ Sim"| E["🟢 Conteúdo tratado<br/>como dado"]

    style A fill:#dcfce7,color:#000000
    style B fill:#dcfce7,color:#000000
    style S fill:#fff7cd,color:#000000
    style C fill:#fef3c7,color:#000000
    style D fill:#fee2e2,color:#000000
    style E fill:#dcfce7,color:#000000
```

> <mark>Cada componente ter passado nos próprios testes não dizia nada sobre o seam entre eles.</mark> O conteúdo buscado ficou não-confiável no momento em que saiu da tarefa do Claude Code. Chegou de um componente que funcionava isoladamente, e foi passado para a próxima chamada como se fosse instrução confiável. A fronteira existia no fluxo de dados — só não estava **marcada**, então nenhum controle a checou.

> ⚠️ Um componente que passa nos próprios testes **não tem** controles de nível de seam.

---

## 🚨 O que observar

> ✅ Um componente confiável isoladamente **não** torna automaticamente confiável o seam que sai dele. Marque todo lugar onde dado ou instrução cruza de um ambiente de deployment para outro como uma **fronteira**. Coloque ali um controle que trate conteúdo buscado como dado, em vez de instruções — exatamente como o trabalho de segurança do módulo anterior ensinou.

> <mark>O seam que ninguém identifica é o que uma ação direcionada cruza.</mark>

---

# 🧪 Checkpoint 7: complete a configuração de fronteira multi-componente

> 🎯 A aplicação abaixo está conectada, com duas lacunas. Arraste o controle certo para o seam que recebe conteúdo buscado não-confiável, e a identidade certa para o componente mais privilegiado.

## 🧩 A aplicação parcial

```python
# componentes conectados: API -> Claude Code task -> MCP server

fetched = code_task.run(fetch_url=customer_page)

# BLANK 1: controle no seam que recebe conteúdo buscado não-confiável
next_call(input=___(fetched))

# MCP server alcança o sistema do cliente (componente mais privilegiado)
mcp_server = MCPServer(
    system=customer_db,
    scope=___,  # BLANK 2: escopo de identidade
)
```

## 🎯 Bank de tokens (dois são distratores)

`treat_as_data` · `least_privilege_read_only` · `run_as_instructions` · `full_access`

## ✅ Respostas

| Blank | Resposta correta | Distrator correspondente |
|---|---|---|
| **BLANK 1** | `treat_as_data` | ❌ `run_as_instructions` — é exatamente o antipadrão do post-mortem |
| **BLANK 2** | `least_privilege_read_only` | ❌ `full_access` — o MCP server só precisa ler o sistema do cliente, não ter acesso total |

```python
next_call(input=treat_as_data(fetched))

mcp_server = MCPServer(
    system=customer_db,
    scope=least_privilege_read_only,
)
```

> <mark>Os dois distratores representam exatamente os dois erros que os post-mortems deste módulo ilustraram: tratar conteúdo buscado como instrução (Trust Boundaries) e dar acesso mais amplo do que a tarefa precisa (Menor Privilégio).</mark>
