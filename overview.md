Perfeito. Vou pesquisar como funciona o Claude Code da Anthropic, incluindo sua arquitetura geral, recursos como
execução de código, integração com terminal, e sistema de memória. Também vou identificar se já existem soluções open
source similares — especialmente aquelas baseadas em LangChain.js ou agentes MCP (multi-component pipelines). Em
seguida, criarei um roadmap técnico para recriar algo semelhante usando Node.js e LangChain.js.

Já volto com os resultados dessa pesquisa.

# Introdução ao Claude Code

Claude Code é uma ferramenta de codificação «agentic» de linha de comando desenvolvida pela Anthropic. Ela atua
diretamente no terminal do desenvolvedor e compreende a base de código do projeto, permitindo executar tarefas via
linguagem natural
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Claude%20Code%20is%20an%20agentic,additional%20servers%20or%20complex%20setup))
([Claude Code Best Practices \ Anthropic](https://www.anthropic.com/engineering/claude-code-best-practices#:~:text=Claude%20Code%20is%20intentionally%20low,develop%20their%20own%20best%20practices)).
Entre suas capacidades estão edição de arquivos, correção de bugs, execução de testes, operações Git (commits, PRs,
histórico), e resposta a perguntas sobre a arquitetura do código
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Claude%20Code%E2%80%99s%20key%20capabilities%20include%3A))
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Your%20code%E2%80%99s%20security%20is%20paramount,Claude%20Code%E2%80%99s%20architecture%20ensures)).
Em vez de depender de servidores intermediários, Claude Code conecta-se diretamente à API da Anthropic (modelo Claude
3.7 Sonnet) a partir do seu ambiente local
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Your%20code%E2%80%99s%20security%20is%20paramount,Claude%20Code%E2%80%99s%20architecture%20ensures))
([Claude Code Best Practices \ Anthropic](https://www.anthropic.com/engineering/claude-code-best-practices#:~:text=Claude%20Code%20is%20intentionally%20low,develop%20their%20own%20best%20practices)).
A filosofia de design enfatiza flexibilidade e poder “puro” de modelo, sem impor fluxos rígidos: Claude Code é um
utilitário de baixo nível, personalizável, scriptável e seguro
([Claude Code Best Practices \ Anthropic](https://www.anthropic.com/engineering/claude-code-best-practices#:~:text=Claude%20Code%20is%20intentionally%20low,develop%20their%20own%20best%20practices))
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Your%20code%E2%80%99s%20security%20is%20paramount,Claude%20Code%E2%80%99s%20architecture%20ensures)).

# Arquitetura e design geral do Claude Code

A **arquitetura** do Claude Code consiste em um cliente CLI local (Node.js) que conversa diretamente com o modelo de
linguagem na nuvem. Segundo a documentação, _“sua consulta vai direto à API da Anthropic sem servidores intermediários”_
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Your%20code%E2%80%99s%20security%20is%20paramount,Claude%20Code%E2%80%99s%20architecture%20ensures)).
Funciona onde você trabalha – diretamente no terminal – e mantém consciência de toda a estrutura do seu projeto
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Your%20code%E2%80%99s%20security%20is%20paramount,Claude%20Code%E2%80%99s%20architecture%20ensures)).
Por exemplo, ao iniciar uma sessão com `claude`, ele escaneia o repositório (arquivos e diretórios) conforme necessário,
incluindo os arquivos de configuração de memória do projeto (como `CLAUDE.md`) no contexto
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Your%20code%E2%80%99s%20security%20is%20paramount,Claude%20Code%E2%80%99s%20architecture%20ensures))
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Memory%20Type%20Location%20Purpose%20Use,styling%20preferences%2C%20personal%20tooling%20shortcuts)).
Essa análise dinâmica do projeto permite que Claude Code “explore” o código sem que o usuário precise adicionar
manualmente cada arquivo ao contexto.

Internamente, o Claude Code expõe **tools** (ferramentas) ao modelo, implementadas em Node.js. As principais ferramentas
incluem leitura/escrita de arquivos, comandos de shell, busca por padrões (grep), listagem de diretórios, edição de
código, e sub-agentes para tarefas complexas
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Tool%20Description%20Permission%20Required%20AgentTool,Creates%20or%20overwrites%20files%20Yes))
([claude-code-mcp/README.md at devin/1741367190-claude-code-mcp-setup · auchenberg/claude-code-mcp · GitHub](https://github.com/auchenberg/claude-code-mcp/blob/devin/1741367190-claude-code-mcp-setup/README.md#:~:text=,edit%20files%20with%20specified%20content)).
Cada ferramenta tem regras de permissão para equilibrar poder e segurança (por exemplo, é solicitada autorização ao
usuário para execução de comandos de shell)
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Claude%20Code%20uses%20a%20tiered,to%20balance%20power%20and%20safety)).
A comunicação entre o cliente (Claude Code) e possíveis servidores especializados segue o protocolo MCP (Model Context
Protocol). Na prática, Claude Code pode acionar servidores locais ou remotos (por exemplo, integrados ao IDE ou outras
ferramentas) que seguem o padrão MCP, permitindo acesso a dados externos e serviços de forma padronizada
([Claude Code tutorials - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/tutorials#:~:text=,connect%20to%20multiple%20specialized%20servers))
([Docker's Founder Builds an Open Source Alternative to Claude Code, Kind Of | AIM Media House](https://analyticsindiamag.com/ai-news-updates/dockers-founder-builds-an-open-source-alternative-to-claude-code-kind-of/#:~:text=Hykes%20emphasised%20that%20the%20system,Model%20Context%20Protocol)).

Além disso, Claude Code incorpora **memória contextual e de longo prazo**. Ele carrega automaticamente arquivos de
memória predefinidos (`./CLAUDE.md`, `./CLAUDE.local.md`, `~/.claude/CLAUDE.md`) na abertura de cada sessão
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Memory%20Type%20Location%20Purpose%20Use,styling%20preferences%2C%20personal%20tooling%20shortcuts)).
Esses arquivos permitem definir preferências de estilo, comandos comuns, arquitetura do projeto etc., de modo que Claude
“lembre” das convenções da equipe ou do desenvolvedor.
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Memory%20Type%20Location%20Purpose%20Use,styling%20preferences%2C%20personal%20tooling%20shortcuts)).

# Execução de código e uso de terminal

Uma característica chave é a capacidade de executar comandos de terminal em tempo real. Claude Code fornece um
_BashTool_ que permite executar comandos Unix (bash) com restrições de segurança
([claude-code-mcp/README.md at devin/1741367190-claude-code-mcp-setup · auchenberg/claude-code-mcp · GitHub](https://github.com/auchenberg/claude-code-mcp/blob/devin/1741367190-claude-code-mcp-setup/README.md#:~:text=,max%20600000%29)).
Por exemplo, ele pode rodar testes (`npm test`), instalar dependências, compilar código, ou até controlar git (através
do Git CLI instalado)
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Your%20code%E2%80%99s%20security%20is%20paramount,Claude%20Code%E2%80%99s%20architecture%20ensures))
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Tool%20Description%20Permission%20Required%20AgentTool,Creates%20or%20overwrites%20files%20Yes)).
O design obriga permissão explícita do usuário para comandos potencialmente perigosos, e restringe certos comandos (como
`curl`, `wget`, entre outros) para evitar abusos
([claude-code-mcp/README.md at devin/1741367190-claude-code-mcp-setup · auchenberg/claude-code-mcp · GitHub](https://github.com/auchenberg/claude-code-mcp/blob/devin/1741367190-claude-code-mcp-setup/README.md#:~:text=The%20bash%20tool%20includes%20security,and%20others))
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Claude%20Code%20uses%20a%20tiered,to%20balance%20power%20and%20safety)).

Ao executar, Claude Code intercala entrada de texto e saída do terminal. Ele “lê” o que acontece no terminal e “escreve”
comandos conforme instruído. Por exemplo, ao pedir para “executar testes do módulo de autenticação”, o agente pode
emitir `npm test` e, se houver falhas, pedir ao modelo para sugerir correções no código. Essa interação é fluida: o
usuário digita uma tarefa em linguagem natural (`claude "corrija o erro de tipo no módulo de auth"`), e Claude Code
traduz para operações reais (rodar testes, localizar o erro, editar o arquivo, rodar novamente, etc.)
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Your%20code%E2%80%99s%20security%20is%20paramount,Claude%20Code%E2%80%99s%20architecture%20ensures))
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Tool%20Description%20Permission%20Required%20AgentTool,Creates%20or%20overwrites%20files%20Yes)).
Todo o processo ocorre localmente no terminal do desenvolvedor, sem janelas adicionais.

# Memória contextual e persistente

Claude Code mantém contexto de duas formas: **conversação** e **memória de longo prazo**. A cada sessão, o histórico da
conversa (com prompts e respostas) é usado no modelo para manter o estado corrente. Além disso, o sistema de _memória_
carregado por arquivos permite persistir informações entre sessões. Há três níveis de memória: a nível de projeto
compartilhado (`./CLAUDE.md` com convenções gerais), a nível local de projeto (`./CLAUDE.local.md` com preferências
pessoais) e a nível de usuário (`~/.claude/CLAUDE.md` com preferências globais)
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Memory%20Type%20Location%20Purpose%20Use,styling%20preferences%2C%20personal%20tooling%20shortcuts)).
Esses arquivos são lidos recursivamente a partir do diretório de trabalho, garantindo que informações relevantes sempre
estejam disponíveis
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Memory%20Type%20Location%20Purpose%20Use,styling%20preferences%2C%20personal%20tooling%20shortcuts)).
Por exemplo, você pode instruir o Claude a lembrar que usa identação de 2 espaços ou preferências de lint via mensagens
iniciadas com `#` – e o sistema adiciona essas linhas no arquivo apropriado
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Claude%20Code%20can%20remember%20your,common%20commands%20in%20your%20workflow))
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Memory%20Type%20Location%20Purpose%20Use,styling%20preferences%2C%20personal%20tooling%20shortcuts)).

Para implementar memória vetorial/persistente em uma versão própria, pode-se usar bancos de vetores externos.
LangChain.js suporta integrações com vetores stores como Pinecone, Qdrant, Chroma etc.
([Vector stores | ️ Langchain](https://js.langchain.com/docs/integrations/vectorstores/#:~:text=LangChain,out%20a%20full%20list%20below)).
Com isso, fragmentos de conversação ou de documentação poderiam ser embutidos (via embeddings) e armazenados para busca
por similaridade posterior
([Zep Open Source Memory | ️ Langchain](https://js.langchain.com/docs/integrations/memory/zep_memory#:~:text=%3E%20Zep%20is%20a%20long,reducing%20hallucinations%2C%20latency%2C%20and%20cost))
([Vector stores | ️ Langchain](https://js.langchain.com/docs/integrations/vectorstores/#:~:text=LangChain,out%20a%20full%20list%20below)).
Plataformas como o _Zep_ (open-source) fazem esse trabalho de guardar e recuperar histórico de chat, incluindo
sumarizações automáticas
([Zep Open Source Memory | ️ Langchain](https://js.langchain.com/docs/integrations/memory/zep_memory#:~:text=%3E%20Zep%20is%20a%20long,reducing%20hallucinations%2C%20latency%2C%20and%20cost)).
Em suma, além da memória baseada em arquivos, é possível estender o agente com um vetorDB para lembrar conversas
passadas ou detalhes do projeto de forma escalável.

# Integração com ferramentas externas

Claude Code se destaca pela integração com o ecossistema de desenvolvimento. Graças ao protocolo MCP, ele pode
conectar-se a servidores especializados: por exemplo, a Claude Desktop (ou outros clientes MCP) pode buscar dados de um
servidor de IDE, repositório, ou qualquer fonte externa que siga o padrão MCP
([Claude Code tutorials - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/tutorials#:~:text=,connect%20to%20multiple%20specialized%20servers))
([Docker's Founder Builds an Open Source Alternative to Claude Code, Kind Of | AIM Media House](https://analyticsindiamag.com/ai-news-updates/dockers-founder-builds-an-open-source-alternative-to-claude-code-kind-of/#:~:text=Hykes%20emphasised%20that%20the%20system,Model%20Context%20Protocol)).
Isso abre caminho para integrações transparentes – imagine um servidor MCP que acessa a API do GitHub, do Jira ou até
uma base de dados local. Como exemplo de extensão, Solomon Hykes (criador do Docker) mostrou um projeto que usa o
_Dagger_ (runtime open-source) para executar agentes com qualquer modelo LLM e suporte nativo a MCP
([Docker's Founder Builds an Open Source Alternative to Claude Code, Kind Of | AIM Media House](https://analyticsindiamag.com/ai-news-updates/dockers-founder-builds-an-open-source-alternative-to-claude-code-kind-of/#:~:text=Hykes%20emphasised%20that%20the%20system,Model%20Context%20Protocol)).
Embora voltado a fluxos de CI/CD (Kubernetes, CLI do GitHub, etc.), esse projeto demonstra como frameworks modulares
podem replicar capacidades similares ao Claude Code, servindo de referência.

Além disso, Claude Code oferece comandos diretos para ações comuns: criar commits e PRs com uma linha (`claude commit`),
resolver conflitos de merge, e navegar pelo histórico Git são exemplos que já vêm integrados
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Your%20code%E2%80%99s%20security%20is%20paramount,Claude%20Code%E2%80%99s%20architecture%20ensures))
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Tool%20Description%20Permission%20Required%20AgentTool,Creates%20or%20overwrites%20files%20Yes)).
Internamente, isso pode usar o CLI do Git. Em uma implementação caseira, poderíamos usar bibliotecas Node de Git (como
`simple-git`) ou simplesmente invocar comandos shell via `git`. Da mesma forma, ferramentas de busca de texto no projeto
utilizam utilitários como `rg` (ripgrep) se disponível, ou implementações próprias em JS.

# Projetos open-source similares

Há diversas iniciativas comunitárias que espelham parte das capacidades do Claude Code. Destacam-se:

- **ask.sh (Rust)**: um assistente de terminal por IA que _lê e escreve diretamente no seu shell_. O _ask.sh_ suporta
  múltiplos provedores LLM (OpenAI, Anthropic etc.) e permite conversas multilink com contexto do terminal
  ([GitHub - combinatrix-ai/ask.sh: ask.sh: AI terminal assistant that can read and write your terminal directly!](https://github.com/combinatrix-ai/ask.sh#:~:text=ask,write%20to%20your%20terminal)).
  Ele incorpora memória básica e pode interpretar sua arquitetura de sistema (OS, CPU, shell). Seu diferencial é
  exatamente executar comandos Unix em tempo real sem alternar de janela, mostrando resultados à medida que conversa com
  o modelo
  ([GitHub - combinatrix-ai/ask.sh: ask.sh: AI terminal assistant that can read and write your terminal directly!](https://github.com/combinatrix-ai/ask.sh#:~:text=ask,write%20to%20your%20terminal)).

- **MyCoder (Node.js)**: ferramenta CLI open-source inspirada no Claude Code. MyCoder integra vários LLMs (Anthropic,
  OpenAI, Ollama) e apresenta um sistema modular de _tools_ extensíveis
  ([GitHub - drivecore/mycoder: Simple to install, powerful command-line based AI agent system for coding.](https://github.com/drivecore/mycoder#:~:text=%2A%20AI,to%20build%20its%20own%20context)).
  Suporta execução paralela (sub-agentes) e foi autodesenvolvido (o próprio código foi gerado pelo agente)
  ([GitHub - drivecore/mycoder: Simple to install, powerful command-line based AI agent system for coding.](https://github.com/drivecore/mycoder#:~:text=%2A%20AI,to%20build%20its%20own%20context)).
  MyCoder usa arquivos `README.md` e do projeto, além de comandos de shell, para montar o contexto, semelhante ao Claude
  Code
  ([GitHub - drivecore/mycoder: Simple to install, powerful command-line based AI agent system for coding.](https://github.com/drivecore/mycoder#:~:text=%2A%20Smart%20Logging%3A%20Hierarchical%2C%20color,running%20agents)).
  Ele também lida automaticamente com o contexto (compactação de mensagens longas) e tem integração com GitHub para
  issues e PRs
  ([GitHub - drivecore/mycoder: Simple to install, powerful command-line based AI agent system for coding.](https://github.com/drivecore/mycoder#:~:text=%2A%20Smart%20Logging%3A%20Hierarchical%2C%20color,running%20agents)).
  Seu código-fonte é uma referência direta de como montar um CLI-agent em Node.js.

- **claude-code-mcp (TypeScript)**: este é um servidor MCP que reprojeta a funcionalidade do Claude Code em código
  aberto
  ([claude-code-mcp/README.md at devin/1741367190-claude-code-mcp-setup · auchenberg/claude-code-mcp · GitHub](https://github.com/auchenberg/claude-code-mcp/blob/devin/1741367190-claude-code-mcp-setup/README.md#:~:text=By%20implementing%20Claude%20Code%20as,for%20greater%20interoperability%20and%20flexibility))
  ([claude-code-mcp/README.md at devin/1741367190-claude-code-mcp-setup · auchenberg/claude-code-mcp · GitHub](https://github.com/auchenberg/claude-code-mcp/blob/devin/1741367190-claude-code-mcp-setup/README.md#:~:text=,edit%20files%20with%20specified%20content)).
  Ele implementa ferramentas como _bash_ (execução de shell com segurança), _readFile_, _listFiles_, _searchGlob_,
  _grep_, _codeReview_ (análise de código) e _editFile_
  ([claude-code-mcp/README.md at devin/1741367190-claude-code-mcp-setup · auchenberg/claude-code-mcp · GitHub](https://github.com/auchenberg/claude-code-mcp/blob/devin/1741367190-claude-code-mcp-setup/README.md#:~:text=,edit%20files%20with%20specified%20content)).
  Serve como exemplo de quais ferramentas e recursos integrar: por exemplo, o _bash_ possui restrições embutidas que
  bloqueiam comandos perigosos
  ([claude-code-mcp/README.md at devin/1741367190-claude-code-mcp-setup · auchenberg/claude-code-mcp · GitHub](https://github.com/auchenberg/claude-code-mcp/blob/devin/1741367190-claude-code-mcp-setup/README.md#:~:text=,max%20600000%29)).
  Esse projeto permite usar os comandos do Claude Code padrão via qualquer cliente MCP, ilustrando o uso prático do
  protocolo.

- **Dagger (Solomon Hykes)**: embora não seja especificamente uma “ferramenta de codificação”, o Dagger é um framework
  open-source de _workflows compostáveis_ que agora inclui módulos para construir agentes baseados em IA em qualquer
  linguagem. Ele suporta qualquer modelo LLM e já demonstrou recursos similares (criar clusters Kubernetes, triagem de
  issues via GitHub CLI) usando linguagem natural no terminal
  ([Docker's Founder Builds an Open Source Alternative to Claude Code, Kind Of | AIM Media House](https://analyticsindiamag.com/ai-news-updates/dockers-founder-builds-an-open-source-alternative-to-claude-code-kind-of/#:~:text=Hykes%20emphasised%20that%20the%20system,Model%20Context%20Protocol)).
  Sua arquitetura incorpora nativamente MCP, o que o torna um paralelo interessante: um sistema open-source de pipelines
  IA que espelha parte do que Claude Code oferece, especialmente na composição de agentes com múltiplas ferramentas.

Além desses, mencionam-se plataformas como **Composio/SweKit** (ferramentas open-source para criação de agentes de
engenharia de software) que, embora baseadas em Python, fornecem inspiração em termos de indexação de código e
integração com APIs externas. Entretanto, para a pilha Node.js e LangChain.js, os exemplos acima (ask.sh, MyCoder,
claude-code-mcp) são mais diretamente relevantes, pois alinham-se à execução local, suporte a múltiplas ferramentas e
memória contextual.

# Roadmap técnico (Node.js + LangChain.js)

Para **recriar um sistema no estilo Claude Code** usando Node.js e LangChain.js, propõe-se o seguinte roteiro detalhado:

1. **Seleção do LLM e uso via LangChain.js**

   - _Modelo:_ Como Claude 3.7 não é publicamente acessível, pode-se usar GPT-4 (via API OpenAI) como substituto de alta
     capacidade. LangChain.js fornece o adaptador `ChatOpenAI` para chat em Node.js. Alternativamente, há wrappers do
     Claude API (por exemplo, `@anthropic-ai/sdk` para Node), mas caso prefira não depender da Anthropic, fique com
     OpenAI ou outros (como GPT-4o, Gemini, etc.). Configurar temperatura baixa (~0.2) garante respostas mais estáveis.
   - _LangChain.js:_ Instalar `@langchain/core` e adaptadores necessários (`@langchain/openai`, `@langchain/anthropic`
     se for usar Claude). Criar uma cadeia de chat usando `new ChatOpenAI({...})` e `new ConversationalAgent(...)`.
     Ajustar prompt-template inicial para explicar o papel do agente (e.g. “Você é um assistente de terminal de
     programação”).
   - _Advanced:_ Considere usar _function calling_ (chamada de funções) do GPT-4 para acionar ferramentas (por exemplo,
     funções definidas para cada ferramenta customizada). Porém, LangChain tradicionalmente usa o modelo de _tools +
     agent_, que também se adequa.

2. **Definição de ferramentas (Agents e Toolkits)**

   - Usar o conceito de _Tools_ do LangChain. Cada ferramenta é uma função ou classe que executa uma ação externa. Por
     exemplo:
     - **ShellTool:** executa comandos de shell. Pode implementar usando `child_process.exec` ou `spawn`. Este deve
       receber uma string de comando e retornar stdout/stderr. Para segurança, implementar check-lists ou sandbox (ver
       próximo item).
     - **FileReadTool / FileWriteTool:** lê e grava arquivos. Usar `fs.promises.readFile` e `writeFile` de Node.
       Importante limitar tamanho de leitura para não estourar o contexto (ler partes do arquivo). Consulte [32] e [62]
       para exemplos de políticas (p.ex. somente leitura de arquivos pequenos sem permissão extra).
     - **ListDirTool (LSTool):** lista arquivos em diretórios (como `ls`). Usar `fs.promises.readdir` ou invocar `ls`.
     - **Grep/SearchTool:** busca texto em arquivos. Pode usar `child_process.exec` com `grep` ou implementar leitura de
       arquivos e pesquisa em JS.
     - **GlobTool:** busca arquivos por padrão (como `glob` ou `rg`). Pode usar pacotes npm como `glob` ou `fast-glob`.
     - **GitTool:** interage com repositório Git. Por exemplo, invocar `git` via shell (para status, diff, commit, etc.)
       ou usar bibliotecas como `simple-git`. Exponha comandos como “criar commit”, “listar branches” etc.
     - **AgentTool:** para subtarefas complexas, criar um sub-agente recursivamente. Em LangChain, isso pode ser
       simplesmente chamar o próprio agente internamente.
   - Cada ferramenta deve seguir a assinatura esperada pelo agente (normalmente um objeto com `name`, `description`, e
     um método `run`). Consulte [62†L476-L484] para a lista de ferramentas do Claude Code; elas servem como guia.

3. **Controle e segurança na execução de comandos shell**

   - **Sandboxing:** idealmente, execute comandos em um ambiente isolado (container Docker ou chroot) para segurança
     máxima. Para um protótipo, pode usar `child_process.exec` com cuidado.
   - **Blocklist / Allowlist:** como referência, Claude Code bloqueia comandos perigosos (e exige permissão para shell)
     ([claude-code-mcp/README.md at devin/1741367190-claude-code-mcp-setup · auchenberg/claude-code-mcp · GitHub](https://github.com/auchenberg/claude-code-mcp/blob/devin/1741367190-claude-code-mcp-setup/README.md#:~:text=The%20bash%20tool%20includes%20security,and%20others))
     ([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Claude%20Code%20uses%20a%20tiered,to%20balance%20power%20and%20safety)).
     Implemente verificações prévias: negar comandos de rede (`curl`, `wget`), destruição de arquivos (`rm -rf /`), etc.
     Perguntar confirmação do usuário quando uma ação sensível for detectada.
   - **Timeouts:** definir tempo máximo para cada execução (p.ex. 60s) para evitar travamento. A biblioteca
     `child_process.exec` permite um timeout.
   - **Output handling:** capture a saída (stdout/stderr) e formate a resposta do agente. Se o comando falhar (exit code
     != 0), passe a mensagem de erro de volta ao LLM para análise do erro.

4. **Gerenciamento de memória e contexto**

   - **Memória de sessão:** use as classes de memória do LangChain. Por exemplo, `ConversationBufferMemory` ou
     `ChatMessageHistory` mantém o histórico de chat atual. Para um histórico persistente, integre um vector store: use
     `VectorStoreRetrieverMemory` (ou `ChatVectorDBQA` com embeddings) para armazenar trechos importantes das conversas
     e recuperá-los em sessões futuras
     ([Zep Open Source Memory | ️ Langchain](https://js.langchain.com/docs/integrations/memory/zep_memory#:~:text=%3E%20Zep%20is%20a%20long,reducing%20hallucinations%2C%20latency%2C%20and%20cost))
     ([Vector stores | ️ Langchain](https://js.langchain.com/docs/integrations/vectorstores/#:~:text=LangChain,out%20a%20full%20list%20below)).
   - **Memória de projeto:** imite os arquivos `CLAUDE.md`. Por exemplo, permita ao usuário criar um arquivo de
     configuração (JSON ou MD) que o agente carrega no início. Esse arquivo pode conter instruções sobre padrões de
     codificação ou dados do projeto. Lê-lo ao iniciar e incluí-lo no prompt inicial.
   - **Vetorização de código/documentação:** para dar contexto à base de código, pré-processar o repositório criando
     embeddings dos comentários principais ou documentação e armazená-los em um vetor DB (Chroma, Qdrant, Pinecone).
     Durante a conversa, use RAG (Retrieval-Augmented Generation) para encontrar trechos relevantes a partir de
     perguntas do usuário, usando o vector store integrado com LangChain
     ([Zep Open Source Memory | ️ Langchain](https://js.langchain.com/docs/integrations/memory/zep_memory#:~:text=%3E%20Zep%20is%20a%20long,reducing%20hallucinations%2C%20latency%2C%20and%20cost))
     ([Vector stores | ️ Langchain](https://js.langchain.com/docs/integrations/vectorstores/#:~:text=LangChain,out%20a%20full%20list%20below)).
     Isso substitui parcialmente a função de “explorar arquivos conforme necessário” descrita no Claude Code.
   - **Compactação de contexto:** implemente truncamento ou sumarização de contexto se o histórico ficar grande. O
     LangChain.js não possui built-in de compactação automática, mas pode-se manualmente limpar o histórico removendo
     entradas menos relevantes ou usando resumos curtos.

5. **Interface de usuário**

   - **CLI (linha de comando):** Crie um programa Node executável (por exemplo, usando
     [Commander.js](https://www.npmjs.com/package/commander) ou [Yargs](https://www.npmjs.com/package/yargs)). O usuário
     deve executar um comando customizado (ex.: `claude-code-node` ou outro nome) dentro da pasta do projeto. Esse CLI
     inicializa o agente e mantém um loop interativo: lê instruções do usuário, envia para o LLM, executa tools conforme
     resposta e exibe resultados.
   - **Modo não-interativo:** como Claude Code, inclua flags para modo “headless” (`--print`) que apenas executam um
     comando único e retornam o texto final (útil em scripts ou CI).
   - **Outras interfaces (opcional):** Embora não estritamente necessário, você pode eventualmente criar uma interface
     web (por exemplo, com Electron ou um app React) para visualização gráfica do output e integração com IDE. Porém, a
     prioridade deve ser a CLI, que alinha-se à proposta original.

6. **Uso de LangChain.js e recursos relevantes**

   - Utilize as **bibliotecas oficiais** do LangChain.js. Por exemplo, instale
     `npm install @langchain/core @langchain/openai @langchain/chromadb` (ou outro vector store escolhido)
     ([Vector stores | ️ Langchain](https://js.langchain.com/docs/integrations/vectorstores/#:~:text=,pnpm)).
   - Consulte os exemplos de LangChain.js para agentes e memória. A documentação oficial em **LangChain.js**
     (https://js.langchain.com) apresenta tutoriais de _agents_ e de _memory_.
   - Para vetores, LangChain.js oferece integrações com **Pinecone, Qdrant, Chroma, HNSWLib** etc. Por exemplo, para
     Qdrant:
     ```js
     import { QdrantClient, QdrantMemoryVectorStore } from '@langchain/qdrant';
     const qdrant = new QdrantClient({ url: process.env.QDRANT_URL });
     const vectorStore = await QdrantMemoryVectorStore.fromTexts(['texto de exemplo'], embeddings, {
     	url: process.env.QDRANT_URL,
     	collectionName: 'claudecode',
     });
     ```
     Isso permite armazenar e recuperar vetores para memória duradoura
     ([Vector stores | ️ Langchain](https://js.langchain.com/docs/integrations/vectorstores/#:~:text=npm%20i%20%40langchain%2Fqdrant)).

7. **Ferramentas auxiliares e referências**
   - Para manipulação de shell e arquivos em Node, use `node:child_process` e `fs`. Pacotes úteis:
     [`shelljs`](https://www.npmjs.com/package/shelljs) (execução shell), [`glob`](https://www.npmjs.com/package/glob)
     (busca de arquivos), [`simple-git`](https://www.npmjs.com/package/simple-git) (Git CLI),
     [`inquirer`](https://www.npmjs.com/package/inquirer) (prompts interativos).
   - Para embeddings, use uma biblioteca como
     [`@huggingface/tokenizers`](https://www.npmjs.com/package/@huggingface/tokenizers) ou uma API de embeddings
     (OpenAI, Cohere). LangChain.js abstrai isso via adaptadores de modelo.
   - Considere olhar implementações similares: o código aberto do **MyCoder** (em TypeScript) mostra diversos padrões,
     assim como o **claude-code-mcp**
     ([claude-code-mcp/README.md at devin/1741367190-claude-code-mcp-setup · auchenberg/claude-code-mcp · GitHub](https://github.com/auchenberg/claude-code-mcp/blob/devin/1741367190-claude-code-mcp-setup/README.md#:~:text=,edit%20files%20with%20specified%20content))
     ([GitHub - drivecore/mycoder: Simple to install, powerful command-line based AI agent system for coding.](https://github.com/drivecore/mycoder#:~:text=%2A%20AI,to%20build%20its%20own%20context)).
   - Siga boas práticas de segurança: inspecione comandos antes de executar e mantenha logs de auditoria (como o Claude
     Code sugere permissões “permanentes por projeto e comando” para shell)
     ([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Claude%20Code%20uses%20a%20tiered,to%20balance%20power%20and%20safety)).

Em resumo, o sistema reconstruído seria um _agent_ de chat integrado ao terminal, construído com LangChain.js, que usa
um LLM forte (ex: GPT-4) via API para interpretar comandos em linguagem natural. Ferramentas personalizadas em Node
permitem ao agente explorar e modificar o código local, executar shell de forma controlada, e operar o Git. A memória do
agente combina arquivos de configuração locais e um banco de vetores para lembrar informações do projeto. Interfaces CLI
completam a solução, similar ao Claude Code original.

**Fontes:** Documentação oficial do Claude Code
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Your%20code%E2%80%99s%20security%20is%20paramount,Claude%20Code%E2%80%99s%20architecture%20ensures))
([Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview#:~:text=Memory%20Type%20Location%20Purpose%20Use,styling%20preferences%2C%20personal%20tooling%20shortcuts))
e projetos de código aberto relevantes (ask.sh
([GitHub - combinatrix-ai/ask.sh: ask.sh: AI terminal assistant that can read and write your terminal directly!](https://github.com/combinatrix-ai/ask.sh#:~:text=ask,write%20to%20your%20terminal)),
MyCoder
([GitHub - drivecore/mycoder: Simple to install, powerful command-line based AI agent system for coding.](https://github.com/drivecore/mycoder#:~:text=%2A%20AI,to%20build%20its%20own%20context)),
repositório Claude-Code-MCP
([claude-code-mcp/README.md at devin/1741367190-claude-code-mcp-setup · auchenberg/claude-code-mcp · GitHub](https://github.com/auchenberg/claude-code-mcp/blob/devin/1741367190-claude-code-mcp-setup/README.md#:~:text=,edit%20files%20with%20specified%20content))).
Esses recursos orientam a escolha de arquitetura, ferramentas e práticas de implementação.
