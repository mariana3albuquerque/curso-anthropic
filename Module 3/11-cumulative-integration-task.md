# 🧪 Tarefa Cumulativa de Integração: os Três Bugs

> 🎯 A integração abaixo tem **três bugs** plantados através das camadas cobertas neste módulo: um na camada de configuração do Claude Code, um na camada de plugin/empacotamento, e um na camada de MCP/autenticação.

---

## 📁 Os três arquivos com defeito

### 1️⃣ `.claude/settings.json`

```json
{ "permissions": { "defaultMode": "bypassPermissions", "deny": ["Read(.env.production)"] } }
```

### 2️⃣ `.claude/skills/migration-validate/SKILL.md`

```yaml
---
name: migration-validate
description: Validates migration scripts before they run against production.
---
## Steps
1. Run: /Users/priya/scripts/validate-migration.sh
2. Report validation results.
```

### 3️⃣ `.mcp.json`

```json
{
  "mcpServers": {
    "data-warehouse": {
      "type": "http",
      "url": "https://warehouse.internal/mcp",
      "headers": {
        "Authorization": "Bearer sk-prod-warehouse-abc123"
      }
    }
  }
}
```

---

## 🔍 Diagnóstico

```mermaid
flowchart TD
    F1["📄 File 1: settings.json"] --> B1["🐛 defaultMode:<br/>bypassPermissions"]
    F2["📄 File 2: SKILL.md"] --> B2["🐛 Path absoluto:<br/>/Users/priya/..."]
    F3["📄 File 3: .mcp.json"] --> B3["🐛 API key inline<br/>no header"]

    B1 --> I1["Remove TODO prompt de<br/>confirmação numa workstation<br/>de produção, incluindo pra<br/>operações destrutivas"]
    B2 --> I2["Path existe só na máquina<br/>do autor — não resolve na<br/>máquina de nenhum colega<br/>após clonar"]
    B3 --> I3["Key entra no histórico do<br/>repo, onde não pode ser<br/>removida sobrescrevendo o<br/>arquivo depois — deve ser<br/>tratada como comprometida"]

    style F1 fill:#e0e7ff,color:#000000
    style F2 fill:#fef3c7,color:#000000
    style F3 fill:#fed7aa,color:#000000
    style B1 fill:#fee2e2,color:#000000
    style B2 fill:#fee2e2,color:#000000
    style B3 fill:#fee2e2,color:#000000
    style I1 fill:#fecaca,color:#000000
    style I2 fill:#fecaca,color:#000000
    style I3 fill:#fecaca,color:#000000
```

| Arquivo | 🐛 Bug | 💬 O que faz/falha em fazer em runtime |
|---|---|---|
| **File 1** (`settings.json`) | `defaultMode` é `bypassPermissions` | Remove toda confirmação numa workstation de produção, incluindo para operações destrutivas. A regra `deny` para `.env.production` está correta — só o modo está errado |
| **File 2** (`SKILL.md`) | Passo 1 usa path absoluto `/Users/priya/scripts/validate-migration.sh` | Esse path existe só na máquina do autor e não vai resolver na máquina de nenhum colega depois que eles clonarem o projeto |
| **File 3** (`.mcp.json`) | API key `sk-prod-warehouse-abc123` committed inline no header `Authorization` | Entra no histórico do repositório onde não pode ser removida sobrescrevendo o arquivo num commit posterior — deve ser tratada como comprometida |

---

# ✅ A Montagem: Versão Corrigida dos Três Arquivos

## 1️⃣ `settings.json` (corrigido)

```json
{ "permissions": { "defaultMode": "acceptEdits", "deny": ["Read(.env.production)"] } }
```

## 2️⃣ `SKILL.md` (corrigido)

```yaml
---
name: migration-validate
description: Validates migration scripts before they run against production.
---
## Steps
1. Run: $CLAUDE_PROJECT_DIR/scripts/validate-migration.sh
2. Report validation results.
```

## 3️⃣ `.mcp.json` (corrigido)

```json
{
  "mcpServers": {
    "data-warehouse": {
      "type": "http",
      "url": "https://warehouse.internal/mcp",
      "headers": {
        "Authorization": "Bearer ${WAREHOUSE_MCP_TOKEN}"
      }
    }
  }
}
```

---

## 🎯 Por que cada correção funciona

| Arquivo | 🛠️ Correção | 💬 Por quê |
|---|---|---|
| `settings.json` | `defaultMode` → `acceptEdits` | Auto-aprova edições de arquivo e comandos comuns de filesystem, mas ainda trava comandos de shell destrutivos — o tradeoff certo para uma workstation de migração de produção |
| `SKILL.md` | Path → `$CLAUDE_PROJECT_DIR/scripts/validate-migration.sh` | Resolve a partir da raiz do projeto em qualquer máquina depois de clonar |
| `.mcp.json` | Header → `Bearer ${WAREHOUSE_MCP_TOKEN}` | Referencia a credencial como variável de ambiente, nunca committed no histórico do repositório |

---

## ✅ Checklist de auto-avaliação

- [ ] Identifiquei os três bugs corretamente (permissão, path, secret)?
- [ ] Entendi por que `bypassPermissions` é especificamente perigoso numa **workstation de produção**, não só "em geral"?
- [ ] Sei que a correção do path precisa ser uma variável relativa ao projeto, não outro path absoluto (nem mesmo `~/`)?
- [ ] Sei que rotacionar a key **depois** de commitá-la não é suficiente sozinho — o valor no histórico continua sendo um problema até a rotação acontecer?
