# 🔬 Post-mortem: o CLAUDE.md que Cresceu até as Regras Pararem de "Pegar"

> 🎯 **Setup:** seu `CLAUDE.md` foi crescendo porque toda nova regra parecia valer a pena adicionar. Cada adição era individualmente razoável, e o arquivo parecia o lugar certo para cada regra. Mas ao longo de algumas semanas, o arquivo cresceu para **mais de 800 linhas**.

---

## 📊 O trace da sessão

Excerto de um projeto com `CLAUDE.md` acumulado ao longo de dois meses de adições do time.

```mermaid
flowchart TD
    A["📄 CLAUDE.md: 847 linhas"] --> B["Preferências de framework<br/>(linhas 1–40)"]
    A --> C["Convenções de teste<br/>(linhas 41–90)"]
    A --> D["Guia de estilo<br/>(linhas 91–210)"]
    A --> E["Regras de dependência<br/>(linhas 211–320)"]
    A --> F["Restrições de path<br/>(linhas 321–360)"]
    A --> G["Log de decisões históricas<br/>(linhas 361–700)"]
    A --> H["Notas arquivadas<br/>(linhas 701–847)"]

    style A fill:#fee2e2,color:#000000
    style B fill:#e0e7ff,color:#000000
    style C fill:#e0e7ff,color:#000000
    style D fill:#e0e7ff,color:#000000
    style E fill:#e0e7ff,color:#000000
    style F fill:#fed7aa,color:#000000
    style G fill:#fecaca,color:#000000
    style H fill:#fecaca,color:#000000
```

### 💬 O que aconteceu

```mermaid
sequenceDiagram
    participant U as 👤 Usuário
    participant CC as 🤖 Claude Code

    U->>CC: "Refatore o módulo de auth para usar o novo<br/>token service. Não modifique /legacy/tokens/."
    CC->>CC: [Lendo módulo de auth ...]
    CC->>CC: [Editando auth/service.ts ...]
    CC->>CC: [Lendo /legacy/tokens/store.ts como referência ...]
    CC->>CC: [⚠️ Editando /legacy/tokens/store.ts para atualizar interface de token ...]
    Note over CC: Restrição de path na linha 347 do CLAUDE.md:<br/>"Não modifique arquivos em /legacy/tokens/"
    U->>CC: "Você acabou de editar /legacy/tokens/store.ts.<br/>Eu disse pra não tocar nesse diretório."
    CC->>U: "Peço desculpas. A restrição está nas<br/>instruções do projeto. A edição aconteceu no<br/>curso da atualização da interface de token.<br/>Não sinalizei o conflito antes de prosseguir."
```

---

## 🕳️ Onde estava a falha

> <mark>A regra estava no arquivo; o agente tinha acesso a ela. Mas a falha foi diluição: as outras 846 linhas reduziram o peso efetivo da única instrução que importava.</mark>

```mermaid
flowchart LR
    A["📏 847 linhas totais"] --> B["1️⃣ regra crítica<br/>(linha 347)"]
    A --> C["846 outras linhas<br/>(log histórico, notas arquivadas, etc.)"]
    C -->|"dilui o peso de"| B
    B --> D["🔴 Regra não 'pegou'<br/>na hora certa"]

    style A fill:#fef3c7,color:#000000
    style B fill:#dcfce7,color:#000000
    style C fill:#fecaca,color:#000000
    style D fill:#fee2e2,color:#000000
```

> ⚠️ O log de decisões históricas e as notas arquivadas deveriam estar anotados em algum lugar — mas **não** no `CLAUDE.md`.

---

## 🚨 O que observar

> ✅ **Regra prática:** `CLAUDE.md` é um conjunto de trabalho de regras que mudam comportamento na sessão atual — **não** um log de append que só cresce.

| Tipo de regra | Onde deveria estar |
|---|---|
| 🎯 Específica de path | Rules file |
| 📜 Contexto histórico | Documento de referência separado, lido sob demanda |
| 🔒 A regra que você **não pode** se dar ao luxo de diluir | O caminho mais curto até virar um **hook** |

> <mark>Quando seu CLAUDE.md passar de algumas centenas de linhas, audite: identifique quais regras são de fato críticas para a sessão e mova o resto.</mark>

---

# 🧪 Checkpoint 2: arraste o valor correto

> 🎯 **Cenário:** você está configurando um hook que aplica uma restrição de path. A configuração abaixo tem duas lacunas.

```json
{ "hooks": {
    "________": [
      {
        "matcher": "Read",
        "hooks": [{ "type": "command", "command": "________" }]
      }
    ]
  }
}
```

## 🕳️ Blank 1 — o evento de ciclo de vida

| Opção | Correta? |
|---|---|
| **PreToolUse** | ✅ **Sim** |
| PostToolUse | ❌ |
| UserPromptSubmit | ❌ |
| SessionStart | ❌ |

> 💬 Precisa ser `PreToolUse` porque é o **único** evento que roda **antes** da tool call executar — e portanto o único capaz de **bloquear** a leitura antes que ela aconteça.

## 🕳️ Blank 2 — o comando

| Opção | Correta? |
|---|---|
| **Um script que lê a tool call do stdin, checa o path do arquivo, e sai com código 2 quando o path é `.env.production` (escrevendo o motivo em stderr)** | ✅ **Sim** |
| Um script que loga a tool call num arquivo de auditoria e sai com 0 | ❌ |
| Um script que imprime um aviso e sai com 0 incondicionalmente | ❌ |

> <mark>Só sair com código 2 efetivamente bloqueia a operação — sair com 0 deixa a leitura passar, não importa o que o script imprima.</mark>
