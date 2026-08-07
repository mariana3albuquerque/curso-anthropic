# 📚 Termos-chave deste Módulo

> 🔤 Em ordem alfabética.

### 🤖 Claude Agent SDK

Uma interface programável que expõe o **mesmo loop de agente** que o Claude Code roda no terminal. Permite que desenvolvedores invoquem o loop a partir de código, definam o modo de permissão e as tools disponíveis, e rodem tarefas sem sessão interativa. <mark>O mesmo modelo de permissão e regras deny que se aplicam no terminal se aplicam no SDK.</mark>

### 📄 CLAUDE.md

Arquivo Markdown colocado na raiz de um projeto Claude Code. Seu conteúdo é prependado à janela de contexto no início de toda sessão. Guarda as restrições universais de projeto, convenções e comandos que devem se aplicar incondicionalmente em todas as sessões. <mark>Arquivos que crescem além de aproximadamente 200-300 linhas correm risco de diluir regras críticas pelo peso do conteúdo.</mark>

### 🪝 Hook

Um comando vinculado a um evento de ciclo de vida na execução do Claude Code (`PreToolUse`, `PostToolUse`, `UserPromptSubmit`, `Stop`). Diferente de instruções no CLAUDE.md, hooks rodam **deterministicamente** no evento configurado, independente do que o modelo decide. Um hook `PreToolUse` pode sair com código 2 para bloquear uma tool call antes dela rodar.

### 🌐 MCP (Model Context Protocol)

Uma camada de comunicação aberta que permite que um cliente MCP como o Claude Code se conecte a um servidor MCP que expõe tools, resources e prompts. O protocolo define como o cliente descobre e chama as tools do servidor. <mark>Usar MCP move a definição e manutenção de tools para fora do código de aplicações individuais e para dentro de um servidor reusável</mark> ao qual qualquer cliente MCP pode se conectar.

### 🚄 MCP transport

O canal de comunicação entre um cliente MCP e um servidor MCP. **stdio** roda o servidor como subprocesso local na mesma máquina do cliente. **HTTP** se conecta a um servidor hospedado remotamente pela rede. A escolha do transporte determina onde o servidor pode rodar e quem pode se conectar a ele.

### 🎚️ Permission mode

Uma configuração no Claude Code que controla com que frequência o agente pausa para pedir confirmação antes de executar tool calls. Modos vão de `default` (pergunta antes de quase toda ação) a modos bypass (nenhum prompt). <mark>Regras deny sobrescrevem qualquer modo</mark>; uma regra deny no nível de settings enterprise não pode ser contornada por nenhuma configuração individual.

### 🔌 Plugin

Um bundle versionado de componentes do Claude Code (skills, hooks, subagents e configurações de servidor MCP) distribuído via marketplace. Instalar um plugin dá ao destinatário o mesmo setup do autor num único passo. Administradores enterprise podem implantar plugins organization-wide via managed settings.

### 🎯 Rules instruction file

Um arquivo que escopa orientação a um path ou condição específica no Claude Code. Diferente do CLAUDE.md, que carrega em toda sessão incondicionalmente, uma rules file ativa só quando o Claude Code está trabalhando no diretório que ela supervisiona. Usado para manter orientação específica de path fora do arquivo principal de memória do projeto.

### 🤖 Subagent

Um contexto de execução separado, lançado pelo Claude Code para lidar com uma tarefa delegada. Um subagente **não herda** o contexto da conversa principal nem os arquivos acumulados — começa limpo, executa a tarefa, e retorna só um resumo. Usar subagentes para trabalho exploratório/investigativo mantém o contexto da sessão principal livre de conteúdo que não será reusado.
