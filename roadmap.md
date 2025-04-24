### 0 · Planejamento & Pesquisa

- [x] **Criar repositório Git** (GitHub/GitLab)
- [x] Definir nome de projeto e domínio de pacote npm
  - Definido como "huu-code" no package.json
- [ ] Escrever README preliminar com visão, escopo e inspirações
- [x] Criar documento de requisitos (funcionais e não-funcionais)
  - [x] Funcionais: pesquisa web, memória, RAG, ferramentas de código, CLI
    - Detalhado em overview.md, memory.md e web-search.md
  - [x] Não-funcionais: segurança shell, extensibilidade, testes, licença
    - Abordado em overview.md com inspiração no Claude Code
- [x] Pesquisar licenças compatíveis (MIT, Apache-2.0, GPL, etc.)
  - Escolhida licença MIT conforme package.json
- [x] Levantar modelos LLM disponíveis (OpenAI, Anthropic, Gemini, etc.)
  - Documentado em overview.md com foco em Claude/GPT-4
- [x] Levantar opções de APIs de busca (Google, Bing, SerpAPI, Brave)
  - Documentado em web-search.md com detalhes de custos e recursos
  - Google: 100 consultas gratuitas/dia, depois US$5/1000 consultas
  - Bing: 1000 consultas gratuitas/mês, depois ~US$25/1000
  - SerpAPI: 100 buscas gratuitas/mês, planos pagos ~US$75/mês para 5000 buscas
  - Brave: 2000 consultas gratuitas/mês, depois US$3/1000
- [x] Levantar bancos vetoriais (Pinecone, Qdrant, Chroma, Weaviate)
  - Documentado em memory.md com análise de opções para memória vetorial
- [x] Escolher linguagem principal (TypeScript vs. JavaScript puro)
  - Escolhido TypeScript conforme configuração em package.json
- [ ] Definir ferramentas de qualidade (ESLint, Prettier, Husky, Commitlint)

---

### 1 · Configuração do Ambiente

- [x] Instalar Node.js LTS (>= 20)
  - Ambiente já está configurado para execução do projeto
- [x] Inicializar `npm init -y` no diretório raiz
  - package.json já existe com configurações básicas
- [ ] Adicionar TypeScript (`npm i -D typescript @types/node`)
  - TypeScript definido como linguagem principal mas dependências ainda não instaladas
- [ ] Escolher e configurar o loader de TypeScript
  - [ ] Opção 1: `tsx` - Mais rápido, baseado em esbuild, zero config
    - Instalar: `npm i -D tsx`
    - Configurar script: `"dev": "tsx src/index.ts"`
  - [ ] Opção 2: `ts-node` - Mais maduro, suporte a sourcemaps
    - Instalar: `npm i -D ts-node`
    - Configurar script: `"dev": "ts-node src/index.ts"`
- [ ] Criar `tsconfig.json` base
  - Configurações essenciais:
    - `"target": "ES2022"`
    - `"module": "NodeNext"`
    - `"moduleResolution": "NodeNext"`
    - `"esModuleInterop": true`
    - `"strict": true`
- [ ] Adicionar ferramentas de lint/format
  - [ ] Instalar ESLint: `npm i -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin`
  - [ ] Criar arquivo `.eslintrc.json` com regras básicas
  - [ ] Instalar Prettier: `npm i -D prettier eslint-config-prettier eslint-plugin-prettier`
  - [ ] Criar arquivo `.prettierrc.json` com configurações de formato
  - [ ] Adicionar scripts: `"lint": "eslint src/", "format": "prettier --write src/"`
- [ ] Criar `.gitignore` completo
  - [ ] Arquivos de sistema: `.DS_Store`, `Thumbs.db`
  - [ ] Dependências: `node_modules/`, `npm-debug.log`, `yarn-error.log`, `package-lock.json` (opcional)
  - [ ] Compilados: `dist/`, `build/`, `*.tsbuildinfo`
  - [ ] Ambiente: `.env`, `.env.local`, `.env.*.local`
  - [ ] Logs: `logs/`, `*.log`
  - [ ] Cachê: `.npm/`, `.cache/`
  - [ ] Arquivos de IDE: `.idea/`, `.vscode/` (opcional)
- [ ] Configurar **Husky** + **Lint-staged** para pre-commit
  - [ ] Instalar: `npm i -D husky lint-staged`
  - [ ] Inicializar husky: `npx husky install` e `npm set-script prepare "husky install"`
  - [ ] Criar hook para pre-commit: `npx husky add .husky/pre-commit "npx lint-staged"`
  - [ ] Configurar lint-staged em package.json: `"lint-staged": { "*.ts": ["eslint --fix", "prettier --write"] }`
- [ ] Configurar **Jest** ou **Vitest** para testes unitários
  - [ ] Opção 1: Jest - Padrão da indústria
    - Instalar: `npm i -D jest ts-jest @types/jest`
    - Criar `jest.config.js` com preset para TypeScript
  - [ ] Opção 2: Vitest - Mais rápido, integração melhor com ESM
    - Instalar: `npm i -D vitest`
    - Configurar script: `"test": "vitest run", "test:watch": "vitest"`
- [x] Configurar **ESM** (ou `ts-node`/`tsx`) para execução TS no CLI
  - Configurado com "type": "module" no package.json

---

### 2 · Estrutura de Pastas

- [x] `src/` – diretório principal do código-fonte
  - Já criado com arquivo index.ts básico
- [ ] Criar estrutura completa de diretórios
  - [ ] `src/cli/` – entrada CLI e interface de terminal
    - [ ] `src/cli/index.ts` - Ponto de entrada principal
    - [ ] `src/cli/commands/` - Subcomandos implementados (exec, chat, commit)
    - [ ] `src/cli/ui/` - Componentes de interface do terminal (spinners, prompts)
    - [ ] `src/cli/repl.ts` - Loop interativo de leitura-avaliação-impressão
  - [ ] `src/agent/` – lógica do agente LangChain
    - [ ] `src/agent/index.ts` - Exportações públicas do módulo
    - [ ] `src/agent/model.ts` - Wrapper para instanciação dos modelos LLM
    - [ ] `src/agent/prompt-templates/` - Templates de prompts para diferentes casos
    - [ ] `src/agent/executor.ts` - Executor de ações do agente
    - [ ] `src/agent/chains/` - Cadeias de raciocínio especializadas
  - [ ] `src/tools/` – ferramentas customizadas (shell, file, git…)
    - [ ] `src/tools/index.ts` - Registro e exportação de todas as ferramentas
    - [ ] `src/tools/base.ts` - Classe base para todas as ferramentas
    - [ ] `src/tools/fs/` - Ferramentas de sistema de arquivos
      - `read.ts`, `write.ts`, `list.ts`, `glob.ts`, `grep.ts`
    - [ ] `src/tools/shell/` - Ferramentas de linha de comando
      - `bash.ts`, `exec.ts`, `sandbox.ts`
    - [ ] `src/tools/git/` - Ferramentas de controle de versão
      - `status.ts`, `diff.ts`, `commit.ts`, `pull-request.ts`
    - [ ] `src/tools/code/` - Ferramentas de análise e edição de código
      - `parse.ts`, `format.ts`, `refactor.ts`
  - [ ] `src/memory/` – implementações de memória
    - [ ] `src/memory/index.ts` - Interface unificada de memória
    - [ ] `src/memory/short-term/` - Memória de curto prazo (buffer)
      - `buffer.ts`, `window.ts`
    - [ ] `src/memory/long-term/` - Memória de longo prazo (vetorial)
      - `vector-store.ts`, `embeddings.ts`
    - [ ] `src/memory/symbolic/` - Memória simbólica (SQLite/Graph)
      - `sqlite.ts`, `graph.ts`
    - [ ] `src/memory/rag/` - Implementação de RAG
      - `retriever.ts`, `context-injector.ts`
    - [ ] `src/memory/summarization/` - Compressão e sumarização
      - `summary.ts`, `compression.ts`
  - [ ] `src/search/` – módulos de busca web
    - [ ] `src/search/index.ts` - Interface comum de busca
    - [ ] `src/search/providers/` - Implementações de provedores
      - `google.ts`, `bing.ts`, `serpapi.ts`, `brave.ts`
    - [ ] `src/search/strategies/` - Estratégias de busca
      - `fallback.ts`, `language-detection.ts`, `query-enhancer.ts`
    - [ ] `src/search/formatter.ts` - Normalização de resultados
  - [ ] `src/config/` – carregamento de configurações
    - [ ] `src/config/index.ts` - API pública de configuração
    - [ ] `src/config/env.ts` - Carregamento de variáveis de ambiente
    - [ ] `src/config/yaml.ts` - Parser de configuração YAML
    - [ ] `src/config/schema.ts` - Validação de esquema de configuração
    - [ ] `src/config/merge.ts` - Lógica de mesclagem de configurações
  - [ ] `src/utils/` – utilitários diversos
    - [ ] `src/utils/logger.ts` - Sistema de logging com Pino
    - [ ] `src/utils/errors.ts` - Classes de erro personalizadas
    - [ ] `src/utils/telemetry.ts` - Coleta de métricas (opcional)
    - [ ] `src/utils/security.ts` - Verificações de segurança e sandbox
    - [ ] `src/utils/tokens.ts` - Contagem e estimativa de tokens
- [ ] `test/` – testes
  - [ ] `test/unit/` - Testes unitários 
    - Organizar espelhando a estrutura do `src/`
  - [ ] `test/integration/` - Testes de integração
  - [ ] `test/fixtures/` - Arquivos de dados para testes
  - [ ] `test/mocks/` - Mocks e stubs para APIs externas
- [ ] `examples/` – demonstrações de uso
  - [ ] `examples/basic/` - Exemplos básicos de uso
  - [ ] `examples/advanced/` - Cenários avançados
  - [ ] `examples/integrations/` - Integrações com outros sistemas

---

### 3 · Carregamento de Configuração

- [ ] Instalar `dotenv` + `dotenv-expand`
  - Necessário para gerenciar variáveis de ambiente
- [ ] Ler chaves das APIs (LLM, busca, vector DB) de `.env`
  - Chaves para serviços como:
    - OpenAI/Anthropic (conforme overview.md)
    - Google/Bing/SerpAPI/Brave (conforme web-search.md)
    - Pinecone/Qdrant/outros vector DBs (conforme memory.md)
- [ ] Suportar arquivo de config YAML (`huu-code.yaml`) por projeto
  - Seguindo inspiração do Claude Code conforme overview.md
  - Similar ao conceito de `CLAUDE.md` mas em formato estruturado YAML
- [ ] Suportar config global em `~/.huu-code/config.yaml`
  - Para configurações persistentes do usuário
- [ ] Função de merge (global → projeto → flag CLI)
  - Permitir que configurações locais sobreescrevam globais
  - Flags de CLI têm prioridade sobre arquivos de configuração

---

### 4 · CLI (Interface de Linha de Comando)

- [ ] Escolher framework CLI baseado em benchmarks e requisitos
  - [ ] Opção 1: **Commander.js** - Mais popular e completo
    - Instalar: `npm i commander`
    - Vantagens: API intuitiva, documentação extensa, suporte a subcomandos
    - Desvantagens: Ligeiramente mais pesado que alternativas
  - [ ] Opção 2: **Yargs** - Poderoso para parsing de argumentos
    - Instalar: `npm i yargs`
    - Vantagens: Parsing flexível, validação de argumentos, comandos aninhados
    - Desvantagens: API mais verbosa
  - [ ] Opção 3: **Oclif** - Framework completo para CLIs complexas
    - Instalar: `npm i oclif`
    - Vantagens: Geração de código, plugins, empacotamento incluído
    - Desvantagens: Curva de aprendizado mais elevada, mais pesado
- [ ] Implementar estrutura base da CLI
  - [ ] Criar arquivo principal `src/cli/index.ts`
  - [ ] Configurar bin no package.json: `"bin": { "huu": "./dist/cli/index.js" }`
  - [ ] Adicionar shebang: `#!/usr/bin/env node`
  - [ ] Implementar parseamento de argumentos
  - [ ] Configurar help automático e versão
- [ ] Implementar comando principal `huu` (nome escolhido como prefixo de "huu-code")
  - [ ] Opção interactiva padrão (`huu`)
    - Similar ao modo interativo do Claude Code
    - Iniciar REPL ou modo interativo para continuar o diálogo
  - [ ] Opção `--prompt "..."` para execução única
    - Para uso em scripts ou CI/CD
    - Processar a consulta e encerrar sem modo interativo
  - [ ] Flag `--print` para saída pura (sem cores)
    - Para integração com outras ferramentas
    - Saída sem formatação, estilo JSON ou texto puro
  - [ ] Flag `--debug` para modo verboso
    - Mostrar logs adicionais para debug
  - [ ] Flag `--config <path>` para configurações customizadas
    - Carregar configuração de arquivo específico
- [ ] Adicionar biblioteca de cores para terminal
  - [ ] Opção 1: **Chalk** - Mais popular e completo
    - Instalar: `npm i chalk@4` (v4 para compatibilidade CommonJS) ou `npm i chalk` (v5+ para ESM)
    - Implementar helpers para formatação consistente
  - [ ] Opção 2: **Kleur** - Mais leve e rápido
    - Instalar: `npm i kleur`
    - Vantagens: Menor overhead, compatível com ESM e CommonJS
- [ ] Adicionar spinner e feedback visual
  - [ ] Opção principal: **Ora** - Spinner elegante para terminal
    - Instalar: `npm i ora`
    - Implementar para operações de longa duração
    - Criar wrapper para uso consistente em toda a aplicação
  - [ ] Implementar diferentes tipos de feedback:
    - Loading: Para operações de rede e LLM
    - Sucesso/erro: Indicadores de resultado
    - Progresso: Barras para operações com progresso mensurável
- [ ] Tratar sinais e saídas do processo
  - [ ] Implementar handler para SIGINT (Ctrl-C)
    - Função para limpar recursos e encerrar graciosamente
  - [ ] Implementar handler para SIGTERM
    - Salvar estado para possível recuperação
  - [ ] Implementar mensagens de despedida
  - [ ] Garantir cleanup de recursos (sockets, archivos temporários)
- [ ] Implementar sistema de prompt interativo
  - [ ] Integrar biblioteca como `inquirer` ou `prompts`
    - Instalar: `npm i inquirer` ou `npm i prompts`
  - [ ] Criar interface para perguntas de confirmação
  - [ ] Implementar histórico de comandos
  - [ ] Adicionar autocompletion quando possível

---

### 5 · Integração com LLM via LangChain.js

- [ ] Instalar `@langchain/core` + `@langchain/openai` (+ outros providers)
  - Suporte para múltiplos provedores conforme overview.md:
    - OpenAI (GPT-4), Anthropic (Claude), e outros
- [ ] Criar wrapper `src/agent/model.ts` para instanciar Chat LLM
  - [ ] Expor temperatura, top_p, etc. via config
    - Permitir ajuste fino de parâmetros do modelo
  - [ ] Implementar fallback entre modelos (caso um falhe)
    - Possibilidade de tentar outro provedor se o principal falhar
- [ ] Implementar função de chamada com token de streaming para CLI
  - Exibição incremental de respostas como no Claude Code
  - Aproveitamento eficiente do streaming dos modelos
- [ ] Adicionar callback handler para saída incremental no terminal
  - UI responsiva conforme tokens são gerados
- [ ] Implementar política de “rate-limit retry” (exponencial)

---

### 6 · Sistema de Memória

#### 6.1 · Memória de Curto Prazo

- [ ] Implementar `ConversationBufferMemory` (LangChain)
  - Conforme detalhado em memory.md para contexto imediato
  - Armazenamento de mensagens recentes na janela de contexto ativa
- [ ] Ajustar limite de tokens dinâmico conforme janela do modelo
  - Adaptar truncamento baseado no tamanho máximo de contexto do LLM
  - Suporte a modelos com diferentes limites (32k tokens, etc.)
- [ ] Criar teste que verifica truncamento correto
  - Testar se mensagens muito antigas são removidas adequadamente

#### 6.2 · Memória de Longo Prazo Persistente

- [ ] Escolher vector DB (ex.: Qdrant local via Docker)
  - Baseado na análise comparativa de memory.md (Pinecone, Qdrant, Chroma, etc.)
  - Considerar opções locais vs. serviços gerenciados na nuvem
- [ ] Instalar SDK (`@qdrant/js-client-rest`)
  - Ou equivalente para o banco vetorial escolhido
- [ ] Função `initVectorStore()` para criar coleção se não existir
  - Inicialização automática da estrutura necessária
  - Criação de índices e configurações de similaridade
- [ ] Implementar serializer de mensagens → embeddings
  - Conversão de textos para vetores de alta dimensão
  - Uso da API de embeddings do modelo escolhido (ex: OpenAI, etc.)
- [ ] Armazenar metadados (userId, timestamp, tipo, tags)
  - Manter informações adicionais sobre cada memória
  - Permitir filtragem e busca por metadados
- [ ] Criar busca por similaridade K-NN (top k)
  - Recuperar as k memórias mais similares ao contexto atual
  - Implementar métricas de similaridade (cosseno, distância euclidiana)
- [ ] Criar filtros por data, tag e relevância
  - Combinar busca semântica com filtros por metadados
  - Priorizar memórias recentes ou marcadas como importantes

#### 6.3 · Memória Simbólica

- [ ] Usar SQLite para fatos estruturados (opcional Neo4j)
  - Conforme detalhado em memory.md sobre armazenamento estruturado
  - SQLite para uso local, Neo4j para grafos mais complexos
- [ ] Definir schema: `memories(id, key, value, created_at)`
  - Estrutura para armazenar fatos concretos extraídos de conversas
  - Suporte a relações entre entidades e propriedades
- [ ] API CRUD para gravar/leitura
  - Interface uniforme para manipulação de fatos
  - Abstrair detalhes de implementação do banco de dados
- [ ] Indexar campos para busca rápida
  - Otimizar consultas por chaves, valores e tempo
  - Garantir performance mesmo com grande volume de dados

#### 6.4 · RAG (Retrieval-Augmented Generation)

- [ ] Fluxo: embed → retrieve (vector DB) → compose prompt
  - Implementar pipeline completo conforme memory.md
  - Seguir arquitetura de RAG para melhorar respostas do LLM
- [ ] Implementar `retrieveRelevantMemories(query)`
  - Função para buscar memórias relacionadas à consulta atual
  - Retornar contexto relevante do banco vetorial
- [ ] Criar prompt-template que injeta “contexto recuperado”
- [ ] Testar queda de alucinação com/sem RAG

#### 6.5 · Sumarização e Compressão

- [ ] Integrar `ConversationSummaryMemory`
  - Utilizar LLM para criar resumos de conversas longas
  - Implementar conforme técnicas descritas em memory.md
- [ ] Agendar sumarização a cada N mensagens ou tokens
  - Definir limites para quando acionar resumo automático
  - Balancear frequência vs. custo computacional
- [ ] Persistir sumário como memória vetorial (tag `summary`)
  - Armazenar resumos com metadados específicos
  - Permitir recuperação de resumos em futuras consultas

---

### 7 · Ferramentas (Tools)

#### 7.1 · File System

- [ ] `readFile(path)` – limitar a 20 kB padrão
- [ ] `writeFile(path, content, mode)` – pedir confirmação se overwrite
- [ ] `listFiles(dir, glob?)` – opcional filtro glob
- [ ] `searchGlob(pattern)` – combinar com `fast-glob`

#### 7.2 · Grep/Search

- [ ] Implementar `grep(pattern, dir, flags)` via `ripgrep` se instalado
- [ ] Fallback em JS puro com streams

#### 7.3 · Shell/Bash

- [ ] Usar `child_process.spawn` em pseudo-terminal
- [ ] Sandbox: bloquear comandos perigosos (`rm -rf /`, `curl http`)
- [ ] Timeout configurável (default 60 s)
- [ ] Prompt de confirmação para comando não-idempotente
- [ ] Suporte a _dry-run_ (`--dry`)

#### 7.4 · Git

- [ ] Instalar `simple-git`
- [ ] `gitStatus()`, `gitDiff()`, `gitCommit(msg)`, `gitCheckout(branch)`
- [ ] Função `createPullRequest()` (GitHub CLI wrapper)

#### 7.5 · Pesquisa na Internet

- [ ] Implementar provider interface `search(query, opts)`
  - Interface comum para todos os adaptadores de busca
  - Padronização de parâmetros e resultados
- [ ] Adaptadores: Google CSE, Bing Search, Brave API, SerpAPI
  - Conforme detalhado em web-search.md:
    - Google CSE: 100 consultas gratuitas/dia, depois $5/1000
    - Bing: 1000 consultas gratuitas/mês, depois $25/1000
    - SerpAPI: 100 buscas gratuitas/mês, planos pagos ~$75/5000
    - Brave: 2000 consultas gratuitas/mês, depois $3/1000
- [ ] Estratégia de fallback (ex.: tentar grátis primeiro, pago depois)
  - Implementar escalonamento inteligente entre provedores
  - Balancear limites de APIs gratuitas e custo de serviços pagos
- [ ] Implementar heurística de idioma: padrão inglês, mas detectar idioma do query
  - Conforme estratégia de idioma em web-search.md
  - Adaptação automática ao idioma mais relevante para a consulta
- [ ] Normalizar resultado → {title, url, snippet}
  - Padronizar resposta de diferentes provedores
  - Facilitar uso por outros componentes do sistema
- [ ] Citar trechos (0-shot extraction) para prompt RAG externo
  - Extrair trechos relevantes de resultados de busca
  - Integração com o sistema de RAG para enriquecer contexto

---

### 8 · Agente LangChain

- [ ] Definir todas as `Tools[]` e registrar no agente
  - Integrar todas as ferramentas desenvolvidas anteriormente
  - Seguir padrão similar ao Claude Code conforme overview.md
- [ ] Usar `initializeAgentExecutorWithOptions` modo `openai-functions`
  - Configurar agente para usar chamadas de função nativas do modelo
  - Alternativa: explorar o modo "ReAct" para modelos sem suporte a funções
- [ ] Criar prefixo de prompt explicando regras (segurança, confirmação)
  - Definir instruções claras sobre limites e permissões
  - Incorporar regras para solicitar confirmação em ações sensíveis
- [ ] Logar cada decisão do agente (verbose mode)
  - Rastrear passos de raciocínio para depuração
  - Permitir análise da cadeia de pensamento do agente
- [ ] Teste: solicitar “build & run unit tests” e observar cadeia

---

### 9 · Segurança & Permissões

- [ ] Implementar camada de permissão por projeto:
  - [ ] Lista de comandos shell aprovados
    - Controle granular de quais comandos podem ser executados
    - Baseado nos modelos de segurança descritos em overview.md
  - [ ] Lista de arquivos/diretórios permitidos
    - Restrição de acesso a áreas sensíveis do sistema
    - Proteção contra manipulação de arquivos críticos
- [ ] Armazenar consentimento em `~/.huu-code/allowlist.json`
  - Persistência de permissões entre sessões
  - Equivalente à funcionalidade de segurança do Claude Code
- [ ] Encriptar dados sensíveis (chaves API) com `Keytar` ou `.gpg`
  - Proteção de credenciais e tokens de API
  - Evitar exposição de dados sensíveis em texto plano
- [ ] Scanner de injeção de prompt (pattern “### unauthorized ###”)

---

### 10 · Logs, Telemetria e Observabilidade

- [ ] Implementar sistema de logging estruturado
  - [ ] Instalar e configurar `pino` como logger principal
    - `npm i pino pino-pretty`
    - Escolhido por ser rápido e com formato JSON nativo
  - [ ] Criar níveis de logging configuráveis
    - `debug`, `info`, `warn`, `error`, `fatal`
  - [ ] Implementar `src/utils/logger.ts` como singleton
    - Interface unificada para todo o projeto
    - Métodos para diferentes níveis de log
  - [ ] Adicionar formatação para ambiente de desenvolvimento
    - Usar `pino-pretty` para logs legíveis em desenvolvimento
  - [ ] Configurar rotação de arquivos de log
    - `npm i pino-roll` para rotação baseada em tamanho/tempo
- [ ] Detalhar logs essenciais
  - [ ] Eventos de chamada de LLM
    - Modelo usado, número de tokens, prompt truncado, etc.
  - [ ] Custos e uso de recursos
    - Estimativa de custo por requisição (baseado em tokens)
    - Total de tokens processados por sessão
  - [ ] Erros e exceções
    - Stack traces completos, contexto do erro
    - Mensagens de erro para usuário vs. logs técnicos
  - [ ] Execução de ferramentas (tools)
    - Entrada, saída e tempo de execução
- [ ] Sistema de métricas e telemetria
  - [ ] Instalar bibliotecas de métricas
    - `npm i prom-client` para métricas Prometheus
  - [ ] Implementar contadores principais
    - Requisições por modelo, latência média, taxa de erros
    - Uso de tokens (entrada/saída) por sessão e total
    - Chamadas por ferramenta (bash, file, search, etc.)
  - [ ] Criar dashboards (opcional)
    - Templates para Grafana ou interface simples interna
  - [ ] Métricas de performance
    - Tempo de processamento, uso de memória
    - Tempos de resposta p50, p95, p99
- [ ] Endpoint para métricas
  - [ ] Implementar servidor HTTP mínimo para `GET /metrics`
    - Usar `http` nativo ou express mínimo
  - [ ] Formatação compatível com Prometheus
  - [ ] Proteger endpoint com autenticação básica
  - [ ] Documentar métricas exportadas
- [ ] Telemetria anônima (opt-in)
  - [ ] Flag de CLI para ativar/desativar
    - `--telemetry=true/false`
  - [ ] Configuração em arquivo global/projeto
  - [ ] Implementar anonimização de dados
    - Remoção de identificadores, hashing
  - [ ] Documentar claramente quais dados são coletados
  - [ ] Implementar cache local antes de envio
    - Envio em lotes para reduzir overhead

---

### 11 · Testes

- [ ] Implementar estratégia de testes por camadas
  - [ ] Estruturar diretórios de testes
    - `test/unit/` - Testes de funções e classes isoladas
    - `test/integration/` - Testes de módulos integrados
    - `test/e2e/` - Testes de fluxos completos
    - `test/fixtures/` - Dados compartilhados para testes
  - [ ] Definir convenções de nomenclatura
    - `*.test.ts` ou `*.spec.ts` para testes unitários
    - `*.integration.test.ts` para testes de integração
    - `*.e2e.test.ts` para testes end-to-end
- [ ] Configurar framework de testes
  - [ ] Opção 1: Jest
    - Instalar `npm i -D jest ts-jest @types/jest`
    - Configurar `jest.config.js` para TypeScript
    - Definir matchers customizados para testes específicos
  - [ ] Opção 2: Vitest (para projetos ESM)
    - Instalar `npm i -D vitest`
    - Configurar `vitest.config.ts`
    - Aproveitar integração com TypeScript
- [ ] Implementar testes unitários para componentes críticos
  - [ ] Testes para cada ferramenta (tools)
    - Validar entradas, lógica e saídas
    - Simular erros e exceções
  - [ ] Testes para módulos de memória
    - Verificar armazenamento e recuperação
    - Testar truncamento e compressão
  - [ ] Testes para utilitários
    - Verificar funções helpers
    - Testar formatação e transformações
  - [ ] Testes para configuração
    - Validar carregamento e mesclagem
- [ ] Implementar testes de integração
  - [ ] Pipeline de RAG end-to-end
    - Testes de fluxo completo: query → retrieve → augment → generate
  - [ ] Integração de memória com vector DB
    - Verificar armazenamento e busca semântica
  - [ ] Combinação de ferramentas
    - Testar interações entre diferentes tools
- [ ] Implementar testes E2E
  - [ ] Criar repositório dummy para testes
    - Estrutura de arquivos predefinida para testes
  - [ ] Cenários completos via CLI
    - Testes que simulam interação do usuário
  - [ ] Testes de fluxos "reais"
    - Execução de comandos shell
    - Interação com sistemas de arquivos
- [ ] Configurar mocks e stubs para APIs externas
  - [ ] Instalar e configurar `nock` para APIs HTTP
    - `npm i -D nock`
    - Mock de respostas para serviços externos
  - [ ] Criar stubs para APIs de LLM
    - Respostas pré-definidas para diferentes prompts
  - [ ] Simular filesystem, processos e ambiente
    - Usar `memfs` ou similar para simular filesystem
    - `npm i -D memfs`
- [ ] Implementar testes de regressão
  - [ ] Identificar cenários críticos
    - Funcionalidades core que não podem falhar
  - [ ] Snapshots para verificação de saída
    - Garantir consistência em refatorações
- [ ] Monitorar cobertura de código
  - [ ] Configurar flags de cobertura
    - `--coverage` para relatórios detalhados
  - [ ] Definir meta de cobertura mínima (80%)
    - Por arquivo, função e branches
  - [ ] Integrar com ferramentas de CI
    - Gerar relatórios em cada build
  - [ ] Visualizar tendências de cobertura
    - Monitorar evolução ao longo do tempo

---

### 12 · Documentação

- [ ] Criar documentação de código
  - [ ] Adicionar comentários JSDoc/TSDoc em interfaces públicas
    - Documentar parâmetros, retornos e exceções
    - Incluir exemplos de uso em funções principais
  - [ ] Configurar e gerar documentação TypeDoc
    - Instalar: `npm i -D typedoc`
    - Criar arquivo de configuração `typedoc.json`
    - Definir script: `"docs:api": "typedoc --out docs/api src/"`
  - [ ] Manter documentação atualizada com o código
    - Garantir que mudanças de API sejam refletidas nos docs
- [ ] Criar documentação de usuário
  - [ ] Completar README.md principal
    - Badges de status (build, versão, licença)
    - Visão geral concisa do projeto
    - Requisitos de sistema
    - Guia de instalação rápida
    - Exemplos de uso básico
    - Links para documentação detalhada
  - [ ] Criar diretório `docs/` para documentação extensa
    - Estrutura hierárquica por tópicos
    - Navegação clara entre documentos
  - [ ] Desenvolver tutoriais passo-a-passo
    - Guia de início rápido
    - Configuração detalhada
    - Usos avançados
    - Troubleshooting comum
- [ ] Criar diagramas e visualizações
  - [ ] Diagrama de arquitetura geral (Mermaid)
    - Visão de alto nível dos componentes
    - Fluxo de dados entre módulos
  - [ ] Diagramas de sequência para fluxos principais
    - Interação entre componentes em casos de uso comuns
  - [ ] Diagramas de classe para estruturas importantes
    - Hierarquias e relações entre tipos
  - [ ] Incluir diagramas no código via comentários
    - Usar Mermaid ou similar embutido em markdown
- [ ] Implementar exemplos práticos
  - [ ] Diretório `examples/` com casos de uso comuns
    - Exemplos autocontidos e executáveis
  - [ ] Exemplos reais com cenários comuns:
    - Refatoração de código JavaScript
    - Correção de testes unitários que falham
    - Geração automática de commits e PRs
    - Pesquisa web e uso de resultados
    - Combinação de ferramentas para tarefas complexas
  - [ ] Incluir documentação em cada exemplo
    - Comentários explicativos
    - Resultados esperados
- [ ] Criar documentação de contribuição
  - [ ] Arquivo `CONTRIBUTING.md`
    - Guia para novos contribuidores
    - Convenções de código e commits
    - Processo de PR e revisão
  - [ ] Documentar processo de desenvolvimento
    - Setup de ambiente de desenvolvimento
    - Executar testes localmente
    - Guia de debugging
  - [ ] Descrever arquitetura para contribuidores
    - Pontos de extensão do sistema
    - Como adicionar novas ferramentas
    - Como integrar novos modelos LLM

---

### 13 · Empacotamento & Distribuição

- [ ] Preparar para distribuição via npm
  - [ ] Configurar `bin` no `package.json` para CLI global
    - Definir: `"bin": {"huu": "./dist/cli/index.js"}`
    - Garantir shebang adequado nos arquivos executáveis
  - [ ] Configurar build para distribuição
    - Script de build: `"build": "tsc -p tsconfig.build.json"`
    - Excluir arquivos de teste e desenvolvimento do build
    - Validar pacote com `npm pack` antes da publicação
  - [ ] Definir campos importantes no package.json
    - `"files"`: lista explícita de diretórios/arquivos a incluir
    - `"engines"`: requisitos de versão do Node.js
    - `"types"`: apontar para tipos TypeScript
    - `"publishConfig"`: configurações específicas para publicação
- [ ] Criar executável standalone
  - [ ] Opção 1: `pkg` para binários nativos
    - Instalar: `npm i -D pkg`
    - Configurar `pkg` no package.json
    - Gerar builds para Windows, macOS e Linux
  - [ ] Opção 2: `nexe` para alternativa mais leve
    - Instalar: `npm i -D nexe`
    - Configurar script de build específico
  - [ ] Lidar com dependências nativas
    - Estratégias para módulos que usam N-API/node-gyp
  - [ ] Testar executáveis em diferentes plataformas
    - Validar funcionamento em SO não usados para desenvolvimento
- [ ] Contêinerização com Docker
  - [ ] Criar Dockerfile para desenvolvimento
    - Base: `node:20-alpine` para imagem leve
    - Volume para código de trabalho: `~/workspace`
    - Configurações para desenvolvimento
  - [ ] Criar Dockerfile para produção
    - Usar multi-stage build para reduzir tamanho
    - Apenas instalar dependências de produção
    - Configurar usuário não-root para segurança
  - [ ] Criar docker-compose.yml (opcional)
    - Definir serviços auxiliares (banco vetorial, etc.)
  - [ ] Escrever instruções claras para uso dos contêineres
    - Comandos de construção e execução
    - Opções de configuração via variáveis de ambiente
- [ ] Publicar pacote no npm
  - [ ] Criar conta npm (caso não exista)
  - [ ] Configurar autenticação: `npm login`
  - [ ] Verificar configurações de escopo/acesso
    - Definir `"publishConfig": {"access": "public"}`
  - [ ] Realizar publicação
    - Comando: `npm publish`
    - Verificar presença no registro npm
  - [ ] Opcional: configurar publicação automática via CI
    - Usar GitHub Actions para publicar em tags
- [ ] Gestão de releases no GitHub
  - [ ] Definir estratégia de versionamento (SemVer)
    - Incrementos para mudanças incompatíveis, features, fixes
  - [ ] Criar processo de changelog
    - Usar conventional commits para gerar automaticamente
    - Instalar `npm i -D standard-version` ou similar
  - [ ] Automatizar criação de tags Git
    - Script para bump de versão e tag
  - [ ] Configurar GitHub Releases
    - Upload automático de artefatos
    - Descrição detalhada de mudanças
    - Marcar versões pré-release quando apropriado

---

### 14 · CI/CD

- [ ] Configurar GitHub Actions para automação
  - [ ] Criar workflow de validação para PRs e push
    - Criar arquivo `.github/workflows/validate.yml`
    - Executar em push para main e PRs
    - Matriz de Node.js (16.x, 18.x, 20.x)
    - Executar em diferentes SOs (ubuntu, windows, macos)
  - [ ] Configurar passos de verificação de código
    - Lint: `npm run lint`
    - Type checking: `npm run typecheck`
    - Testes unitários: `npm run test`
    - Verificar formato com Prettier: `npm run format:check`
    - Garantir cobertura mínima: `npm run test:coverage`
  - [ ] Configurar workflow de build
    - Criar arquivo `.github/workflows/build.yml`
    - Compilar TypeScript para JavaScript: `npm run build`
    - Verificar se build é bem-sucedido e gera artefatos esperados
    - Arquivar artefatos de build para uso posterior
  - [ ] Configurar workflow de publicação
    - Criar arquivo `.github/workflows/publish.yml`
    - Gatilho: tag com padrão `v*` (semântico)
    - Publicar pacote no npm: `npm publish`
    - Criar GitHub Release com notas
    - Incluir checksums dos artefatos
- [ ] Implementar fluxos específicos para Docker
  - [ ] Workflow de build/push de imagens Docker
    - Criar arquivo `.github/workflows/docker.yml`
    - Build multi-plataforma (amd64, arm64)
    - Push para GitHub Container Registry (GHCR)
    - Usar cache para builds mais rápidos
  - [ ] Configurar tags automáticas
    - Tag `latest` para versões estáveis
    - Tags específicas para cada versão (ex: `v1.2.3`)
    - Tag `edge` para branch principal
  - [ ] Configurar scan de segurança com Trivy
    - Verificar vulnerabilidades nas imagens
- [ ] Configurar ferramentas de qualidade contínua
  - [ ] Configurar Dependabot para atualizações seguras
    - Criar arquivo `.github/dependabot.yml`
    - Definir intervalo de verificação (diário/semanal)
    - Configurar limite de PRs simultâneos
    - Incluir npm e GitHub Actions
  - [ ] Implementar Codecov para análise de cobertura
    - Integrar com workflow de testes
    - Publicar relatórios de cobertura
    - Configurar limites de cobertura aceitáveis
  - [ ] Configurar CodeQL para análise estática
    - Análise de segurança automatizada
    - Executar em pushes para branch principal
- [ ] Implementar deployment contínuo (opcional)
  - [ ] Setup de ambiente de staging automatizado
    - Deploy para ambiente de teste após merge
    - Execução de testes de integração em staging
  - [ ] Configurar aprovações para prod
    - Usar GitHub Environments para proteções
    - Requer aprovação manual para releases
  - [ ] Implementar rollback automatizado
    - Detectar falhas pós-deploy
    - Restaurar versão anterior em caso de problema
- [ ] Monitoramento e feedback de CI
  - [ ] Configurar notificações
    - Alertas para falhas de build/teste
    - Integrações com Slack/Discord/Email
  - [ ] Implementar verificações de performance
    - Comparações de performance entre versões
    - Alerta para regressões significativas
  - [ ] Criar dashboards de status
    - Resumo visual do estado do projeto
    - Métricas de builds, testes e deploys

---

### 15 · Performance & Escalabilidade

- [ ] Otimizar sistemas de cache
  - [ ] Implementar caching de embeddings
    - Utilizar SQLite para persistência local
    - Implementar LRU (Least Recently Used) para memória em execução
    - `npm i lru-cache sqlite better-sqlite3`
    - Definir política de expiração (TTL) de cache
  - [ ] Criar cache para resultados de busca
    - Evitar refazer buscas recentes idênticas
    - Armazenar resultados com timestamp
    - Configurar limites de tamanho por consulta
  - [ ] Cachear respostas de API externas
    - Reduzir chamadas desnecessárias a APIs pagas
    - Implementar invalidação seletiva de cache
    - Suporte a modo offline com cache
- [ ] Otimizar operações de embedding
  - [ ] Implementar processamento em batch
    - Agrupar textos para reduzir chamadas a APIs
    - Configurar tamanho de batch dinâmico
    - Balancear entre latência e throughput
  - [ ] Investigar embeddings locais
    - Avaliar modelos leves (MiniLM, MPNet)
    - Integrar com `onnxruntime-node` para execução local
    - Comparar performance/qualidade vs. embeddings na nuvem
  - [ ] Compressão de embeddings
    - Técnicas como PCA ou quantização
    - Tradeoff entre espaço/precisão
- [ ] Paralelizar operações I/O-bound
  - [ ] Paralelizar downloads de documentos
    - Usar `Promise.all` para requisições simultâneas
    - Implementar limites de concorrência
    - Adaptar timeouts para conexões lentas
  - [ ] Paralelizar operações de arquivo
    - Leitura/escrita assíncrona para arquivos grandes
    - Streams para processamento de dados volumosos
  - [ ] Paralelizar consultas a banco de dados
    - Otimizar queries a bancos vetoriais
    - Agregar consultas quando possível
- [ ] Monitorar e otimizar uso de recursos
  - [ ] Tracking de uso de memória
    - Monitorar heap growth com `process.memoryUsage()`
    - Identificar e corrigir memory leaks
    - Implementar limites adaptativos
  - [ ] Otimizar uso de CPU
    - Usar `worker_threads` para tarefas pesadas
    - Evitar bloqueio do event loop
    - Estratégias de throttling para operações intensivas
  - [ ] Gerenciar limites de conexões
    - Pools de conexões para APIs externas
    - Retry/backoff baseado em disponibilidade
- [ ] Implementar benchmarks e métricas
  - [ ] Criar suite de benchmark automatizada
    - Testar performance em cenários realistas
    - Comparar versões para detectar regressões
  - [ ] Medir tempo médio de resposta
    - Percentis p50 (mediana), p95, p99
    - Tempo total vs. tempo de processamento
    - Latência de rede vs. computação local
  - [ ] Monitoramento de uso de tokens
    - Custo por operação/consulta
    - Optimização de prompts para redução de tokens
- [ ] Estratégias para escala
  - [ ] Implementar arquitetura de plugins
    - Carregar componentes sob demanda
    - Reduzir footprint inicial de memória
  - [ ] Preparar para multi-threading
    - Identificar pontos de paralelização
    - Design thread-safe para estruturas compartilhadas
  - [ ] Planejar modo cliente-servidor (futuro)
    - Separar CLI de backend para uso em equipe
    - API para integração com outros sistemas

---

### 16 · UX & Polimento

- [ ] Aprimorar experiência visual no terminal
  - [ ] Criar banners e identidade visual
    - Desenvolver logo ASCII art para inicialização
    - Permitir desabilitar com `--no-banner`
    - Adaptar para diferentes tamanhos de terminal
  - [ ] Implementar temas de cores customizáveis
    - Temas claro/escuro automáticos com base no terminal
    - Suporte a personalização via arquivo de config
    - Usar variáveis de cor para consistência
  - [ ] Aprimorar formatação de saída
    - Layout responsivo para diferentes larguras de terminal
    - Formatos especiais para diferentes tipos de conteúdo
    - Indicadores visuais para diferentes tipos de resposta
- [ ] Melhorar UX de linha de comando
  - [ ] Implementar auto-complete para CLI
    - Configurar shell completions (bash, zsh, fish)
    - Script: `huu --generate-completions > ~/.huu-completions`
    - Auto-complete inteligente de opções e caminhos
  - [ ] Criar aliases para comandos comuns
    - Atalhos para operações frequentes
    - Aliases customizáveis pelo usuário
  - [ ] Implementar histórico e navegação
    - Salvar histórico de comandos em `~/.huu_history`
    - Navegação com setas para acessar comandos anteriores
    - Busca no histórico com Ctrl+R
- [ ] Modo “quiet/JSON” para integração com outras ferramentas
- [ ] Integração opcional com VS Code via tasks ou extensão futura

---

### 17 · Auditoria de Segurança Final

- [ ] Auditar dependências e vulnerabilidades
  - [ ] Executar verificação de segurança com `npm audit`
    - Validar todas as dependências diretas e transitivas
    - Gerar relatório detalhado de vulnerabilidades
  - [ ] Aplicar correções com análise de impacto
    - `npm audit fix` para correções não-disruptivas
    - Avaliar `npm audit fix --force` apenas quando necessário
    - Testar aplicação após cada atualização de segurança
  - [ ] Implementar processo contínuo de atualização
    - Configurar alertas de segurança do GitHub/Dependabot
    - Automatizar PRs para atualizações de segurança
- [ ] Verificar conformidade de licenças
  - [ ] Executar `license-checker --summary`
    - Verificar compatibilidade de todas as licenças
    - Identificar licenças potencialmente problemáticas
  - [ ] Criar política de licenças aceitáveis
    - Documentar quais licenças são permitidas
    - Definir procedimento para exceções
  - [ ] Gerar NOTICE.md com atribuições
    - Listar todas as bibliotecas de terceiros
    - Incluir texto de licença quando necessário
- [ ] Realizar revisão de código de segurança
  - [ ] Verificar potenciais problemas de segurança
    - Injeção de comandos em ferramentas shell
    - Validação de entrada em todas as APIs
    - Path traversal em operações de arquivo
  - [ ] Revisar sandbox de comandos shell
    - Testar bypass de restrições
    - Verificar validação de comandos perigosos
  - [ ] Revisar gerenciamento de credenciais
    - Armazenamento seguro de tokens de API
    - Sanitização de logs para evitar exposição
- [ ] Conduzir auditoria de privacidade e dados
  - [ ] Revisar logs para dados sensíveis
    - Garantir que informações sensíveis não são logadas
    - Implementar mascaramento para tokens e chaves
  - [ ] Documentar fluxos de dados
    - Mapear onde dados de usuário são armazenados
    - Identificar dados enviados para serviços externos
  - [ ] Implementar política de retenção de dados
    - Definir períodos de armazenamento
    - Procedimento para limpeza de dados antigos

---

### 18 · Lançamento v1.0.0

- [ ] Preparação para lançamento estável
  - [ ] Congelar funcionalidades core (feature freeze)
    - Definir conjunto estável de recursos para v1.0
    - Criar branch de release (`release/1.0.0`)
    - Interromper adição de features novas
  - [ ] Eliminar bugs conhecidos
    - Resolver issues com prioridade "blocker" e "critical"
    - Executar ciclo final de testes em todas as plataformas
    - Verificar compatibilidade entre diferentes sistemas
  - [ ] Estabilizar APIs públicas
    - Documentar interfaces estáveis com garantia de compatibilidade
    - Marcar APIs experimentais ou sujeitas a mudanças
    - Verificar assinaturas e comportamentos públicos
- [ ] Documentação de lançamento
  - [ ] Gerar changelog completo baseado em histórico
    - Usar conventional commits para categorização
    - Agrupar mudanças por tipo (feat, fix, docs, etc.)
    - Destacar breaking changes e migrações necessárias
  - [ ] Criar release notes detalhadas
    - Resumo não-técnico de funcionalidades principais
    - Guia de instalação e primeiros passos
    - Exemplos de casos de uso mais importantes
    - Reconhecimento de contribuidores
  - [ ] Atualizar documentação para versão final
    - Revisar README.md e ajustar para v1.0
    - Verificar referências de versão em todos os docs
    - Garantir que a documentação reflete o comportamento final
- [ ] Processo de lançamento técnico
  - [ ] Executar build final de distribuição
    - Validar artefatos gerados (npm, binários, Docker)
    - Verificar checksums e assinaturas
    - Testar instalação em ambiente limpo
  - [ ] Publicar pacotes oficiais
    - Atualizar versão em package.json para 1.0.0
    - Publicar pacote no npm: `npm publish`
    - Enviar imagens Docker para registros
    - Criar tag git: `git tag -a v1.0.0 -m "v1.0.0"`
  - [ ] Configurar GitHub Release
    - Upload de binários e artefatos
    - Incluir release notes completas
    - Marcar como release estável (não pre-release)
- [ ] Anúncio e promoção
  - [ ] Preparar materiais de comunicação
    - Blog post explicando o projeto e suas capacidades
    - Screenshots/GIFs demonstrando funcionalidades
    - Exemplos de código prontos para cópia
  - [ ] Publicar anúncios nos canais relevantes
    - GitHub (release, discussions, topics)
    - Dev.to, Hashnode ou Medium
    - Reddit (r/javascript, r/node, r/programming)
    - Twitter/X, LinkedIn, Mastodon
    - Hacker News (Show HN)
  - [ ] Engajar com a comunidade
    - Responder a perguntas e comentários
    - Agradecer feedback inicial
    - Coletar sugestões para próximas versões

---

### 19 · Pós-Lançamento & Futuro

- [ ] Gestão contínua do projeto
  - [ ] Implementar processo de coleta de feedback
    - Categorizar issues por tipo (bug, feature, enhancement)
    - Priorizar baseado em impacto e esforço
    - Criar templates para diferentes tipos de contribuição
  - [ ] Estabelecer processo de manutenção
    - Definir janelas de release para patches e minor versions
    - Documentar processo de backporting para correções críticas
    - Configurar automação para tarefas repetitivas
  - [ ] Construir comunidade de contribuidores
    - Criar lista de "good first issues"
    - Documentar fluxo de contribuição
    - Reconhecer contribuições externas
- [ ] Planejar próximas versões
  - [ ] Criar roadmap para v1.1.x (atualizações menores)
    - Priorizar melhorias incrementais
    - Correções de bugs relevantes
    - Pequenas features com alto impacto
  - [ ] Delinear roadmap para v2.0 (próxima versão maior)
    - Identificar melhorias arquiteturais importantes
    - Listar funcionalidades transformadoras
    - Definir breaking changes necessárias
  - [ ] Definir timeline de releases
    - Estimativas de tempo para próximas versões
    - Calendário de manutenção para v1.x
- [ ] Explorar extensões técnicas
  - [ ] Sistema de plugins para ferramentas customizadas
    - Permitir que usuários criem suas próprias tools
    - Desenvolver API estável para extensão
    - Criar repositório de plugins da comunidade
  - [ ] Implementar suporte a embeddings locais (MiniLM, GGUF)
    - Integrar modelos leves para uso offline
    - Explorar modelos quantizados para eficiência
    - Comparar performance vs. embeddings baseados em API
  - [ ] Adicionar interface web opcional
    - Desenvolver UI baseada em navegador
    - Conectar à mesma engine do CLI
    - Suporte a histórico visual e navegação
- [ ] Otimizações avançadas
  - [ ] Investigar fine-tuning de LLM específico para comandos CLI
    - Treinar ou fine-tunar modelos para entender melhor comandos
    - Especializar em reconhecimento de padrões de desenvolvimento
    - Otimizar para baixa latência em contextos de terminal
- [ ] Avaliar migração para CPP/ Rust nos módulos de performance crítica

---
