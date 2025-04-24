Perfeito. Vou pesquisar em profundidade as melhores ferramentas (pagas e gratuitas) para permitir que agentes em Node.js
façam buscas na internet, incluindo APIs como Google, Bing, SerpAPI, entre outras. Também vou buscar as melhores
práticas e técnicas de pesquisa, com foco em como uma IA pode:

- Escolher onde pesquisar dependendo do tipo de pergunta
- Construir consultas eficazes
- Filtrar resultados e iterar quando não encontrar a resposta ideal

Incluirei tudo isso em português na resposta final, com instruções claras para que um agente saiba como pesquisar
sozinho, inclusive quando usar inglês ou outro idioma.

Já volto com esse material completo.

# Ferramentas e APIs de busca programática

([File:Node.js logo.svg - Wikipedia](https://en.wikipedia.org/wiki/File:Node.js_logo.svg))Para que um agente em Node.js
realize buscas online, existem várias APIs e bibliotecas oficiais. Por exemplo, o cliente **Google APIs para Node.js**
(`googleapis`) permite usar a [Custom Search JSON API](https://developers.google.com/custom-search/v1/overview) do
Google. A API do Google oferece **100 consultas gratuitas por dia**
([Custom Search JSON API  |  Programmable Search Engine  |  Google for Developers](https://developers.google.com/custom-search/v1/overview#:~:text=Custom%20Search%20JSON%20API%20provides,to%2010k%20queries%20per%20day));
acima disso cobra cerca de **US\$5 por 1000 queries**
([Custom Search JSON API  |  Programmable Search Engine  |  Google for Developers](https://developers.google.com/custom-search/v1/overview#:~:text=Custom%20Search%20JSON%20API%20provides,to%2010k%20queries%20per%20day)).
É necessário criar um _Programmable Search Engine_ e usar a chave de API. Para **Bing Web Search** (Azure Cognitive
Services), há o pacote `azure-cognitiveservices-websearch` disponível
([Bing Web Search JavaScript client library quickstart - Bing Search Services | Microsoft Learn](https://learn.microsoft.com/en-us/bing/search-apis/bing-web-search/quickstarts/sdk/web-search-client-library-javascript#:~:text=const%20CognitiveServicesCredentials%20%3D%20require%28%27ms,websearch)).
A camada gratuita do Azure permite **1000 transações grátis por mês**
([Bing Search API Pricing | Microsoft Bing](https://www.microsoft.com/en-us/bing/apis/pricing#:~:text=1%2C000%20transactions%20free%20per%20month,for%20all%20markets%20S1%20250)).
Após isso, planos pagos (por exemplo o S1) custam em torno de **\$25 por 1000 consultas**
([Bing Search API Pricing | Microsoft Bing](https://www.microsoft.com/en-us/bing/apis/pricing#:~:text=%2425%20per%201%2C000%20transactions)).
Ambas as APIs (Google e Bing) devolvem resultados no formato JSON com informações de títulos, trechos, URLs, etc.

- **Google Custom Search API:** integra-se com Node.js via REST ou pacote oficial. Documentação e uso: basta instalar
  `@googleapis/customsearch` ou usar diretamente `googleapis`. Fornece resultados de busca web (e imagem) com boa
  relevância. Exemplo de uso em Node: atribuir chave de API e chamar o método de pesquisa
  ([Custom Search JSON API  |  Programmable Search Engine  |  Google for Developers](https://developers.google.com/custom-search/v1/overview#:~:text=Custom%20Search%20JSON%20API%20provides,to%2010k%20queries%20per%20day)).

- **Bing Web Search API (Azure):** acessível via pacote Node (`azure-cognitiveservices-websearch`)
  ([Bing Web Search JavaScript client library quickstart - Bing Search Services | Microsoft Learn](https://learn.microsoft.com/en-us/bing/search-apis/bing-web-search/quickstarts/sdk/web-search-client-library-javascript#:~:text=const%20CognitiveServicesCredentials%20%3D%20require%28%27ms,websearch))
  ou requisição HTTP com chave Azure. Além de pesquisar a web, inclui resposta embutida de notícias, vídeos e
  verificação ortográfica. A camada gratuita cobre até 1.000 consultas/mês
  ([Bing Search API Pricing | Microsoft Bing](https://www.microsoft.com/en-us/bing/apis/pricing#:~:text=1%2C000%20transactions%20free%20per%20month,for%20all%20markets%20S1%20250));
  planos pagos sobem a partir de cerca de \$25/1000. A documentação da Microsoft (Quickstart) mostra exemplo em Node.js
  de como enviar consultas e receber JSON.

- ([SerpApi: Google Search API](https://serpapi.com/))**SerpApi:** serviço comercial que faz scraping de buscadores
  (Google, Bing, DuckDuckGo, etc.) e retorna JSON estruturado. Oferece um SDK oficial para Node.js
  (`google-search-results-nodejs`)
  ([google-search-results-nodejs - npm](https://www.npmjs.com/package/google-search-results-nodejs#:~:text=const%20SerpApi%20%3D%20require%28%27google,%7B%20console.log%28result%29)),
  facilitando consultas como:

```js
const SerpApi = require('google-search-results-nodejs');
const search = new SerpApi.GoogleSearch('YOUR_API_KEY');
search.json({ q: 'consulta', location: 'Austin, TX' }, (result) => {
	console.log(result);
});
```

SerpApi conta com **plano gratuito de 100 buscas/mês** ([SerpApi: Google Search API](https://serpapi.com/#:~:text=));
planos pagos começam em ~\$75/mês para 5.000 buscas ([SerpApi: Google Search API](https://serpapi.com/#:~:text=))
([SerpApi: Google Search API](https://serpapi.com/#:~:text=)). Suas vantagens incluem bypass de CAPTCHAs e dados extra
(por exemplo, preços, avaliações) que não estão disponíveis nas APIs oficiais. Em contrapartida, é pago e depende da
infraestrutura da SerpApi.

- ([File:Brave search logo.png - Wikimedia Commons](https://commons.wikimedia.org/wiki/File:Brave_search_logo.png))**Brave
  Search API:** API REST do mecanismo de busca _Brave_ (focado em privacidade). Permite consultar bilhões de páginas via
  requisição HTTP simples (usando header `X-Subscription-Token`). Há plano **gratuito de até 2.000 consultas/mês**
  ([Brave Search API | Brave](https://brave.com/search/api/#:~:text=)) (1 QPS); planos pagos começam em \$3 CPM por
  1.000 consultas (20 QPS, até 20M/mês) ([Brave Search API | Brave](https://brave.com/search/api/#:~:text=)). O Brave
  Search devolve resultados “independentes”, cobrindo bem notícias e eventos recentes
  ([Brave Search API | Brave](https://brave.com/search/api/#:~:text=Image%3A%20currency%20icon)). Pode ser usado em
  Node.js com qualquer biblioteca HTTP (por exemplo `axios` ou `fetch`). A documentação oficial fornece parâmetros
  (query, localização, idioma, etc.) para customizar a busca
  ([Brave Search - API](https://api-dashboard.search.brave.com/app/documentation/web-search/get-started#:~:text=curl%20,Token%3A%20%3CYOUR_API_KEY)).

## Técnicas avançadas de pesquisa

- **Construção de queries eficazes:** adapte a query ao tipo de problema. Para erros de código, inclua a mensagem
  completa de erro e contexto (linguagem, biblioteca). Exemplo **ruim**: `erro servidor node conexao` — muito genérico.
  **Bom**: `"Error: Cannot read property 'xyz' of undefined" Node.js Express site:stackoverflow.com` — usa o trecho
  exato do erro em aspas e restringe a pesquisa ao StackOverflow. Para buscar uma biblioteca desconhecida, inclua nome +
  “npm” ou “GitHub” (ex.: `biblioteca-XYZ npm site:github.com`). Para algoritmos ou técnicas, mencione o nome específico
  e linguagem (ex.: `implementação mergesort C++ exemplo`). Para artigos acadêmicos ou normas (ABNT, lei), use termos
  precisos e inclua filtros de formato (ex.: `filetype:pdf` ou site oficial).

- **Operadores e refinamento:** use operadores booleanos e filtros para refinar. O Google/Bing aceita:

  - **aspas (`" "`):** busca frase exata (ex.: `"hello world"`).
  - **coringa `*`:** substitui palavras (ex.: `"hello * world"` retornará “hello to the world”, etc.)
    ([http - How to construct complex Google Web Search query? - Stack Overflow](https://stackoverflow.com/questions/15852238/how-to-construct-complex-google-web-search-query#:~:text=Moreover%2C%20if%20you%20want%20to,operator)).
  - **OR e parênteses:** combine termos alternativos (`cat OR dog`) e aninhe em parênteses para lógica complexa
    ([http - How to construct complex Google Web Search query? - Stack Overflow](https://stackoverflow.com/questions/15852238/how-to-construct-complex-google-web-search-query#:~:text=To%20create%20complex%20,OR)).
  - **exclusão (-):** use `-palavra` para excluir termos (ex.: `jaguar -carro`).
  - **site:** restrinja domínio (ex.: `site:stackoverflow.com function` para pesquisar apenas no StackOverflow)
    ([http - How to construct complex Google Web Search query? - Stack Overflow](https://stackoverflow.com/questions/15852238/how-to-construct-complex-google-web-search-query#:~:text=However%2C%20if%20you%20want%20to,g)).
    Inversamente, para excluir um site: use `-site:exemplo.com`
    ([http - How to construct complex Google Web Search query? - Stack Overflow](https://stackoverflow.com/questions/15852238/how-to-construct-complex-google-web-search-query#:~:text=However%2C%20if%20you%20want%20to,g)).
  - **filetype:** busque apenas em tipo de arquivo (ex.: `filetype:pdf Leis trabalhistas` retorna PDFs sobre leis)
    ([http - How to construct complex Google Web Search query? - Stack Overflow](https://stackoverflow.com/questions/15852238/how-to-construct-complex-google-web-search-query#:~:text=To%20get%20for%20a%20specific,as_filetype)).
  - **intitle: / inurl:** filtre por termo no título ou na URL (ex.: `intitle:download linux iso`)
    ([http - How to construct complex Google Web Search query? - Stack Overflow](https://stackoverflow.com/questions/15852238/how-to-construct-complex-google-web-search-query#:~:text=,allinurl)).  
    Inicie com uma query ampla e vá refinando: observe resultados iniciais e adicione termos ou filtre conforme
    necessidade. A cada iteração, ajuste termos chave ou operadores para eliminar ruído e focar na informação desejada.

- **Fontes ideais:** escolha fontes de acordo com o conteúdo. Para dúvidas de programação, _Stack Overflow_, _GitHub_ e
  documentação oficial das linguagens/bibliotecas são preferíveis (procure por issues, repositórios ou docs
  específicos). Para tutoriais ou explicações, _Medium_, blogs e livros podem ajudar, mas verifique confiabilidade. Para
  pesquisa acadêmica, use _Google Scholar_, _ArXiv_, Sci-Hub ou buscadores de artigos (ex.: consultas com
  `site:arxiv.org`). Para normas técnicas e leis brasileiras, pesquise em português e utilize sites governamentais ou
  institucionais (ex.: “NBR 6023 site:abnt.org.br”).

- **Exemplos práticos:**
  - Ruim: `erro python lista` – genérico. Bom: `"IndexError: list index out of range" Python site:stackoverflow.com`
    (mensagem exata + site)
    ([http - How to construct complex Google Web Search query? - Stack Overflow](https://stackoverflow.com/questions/15852238/how-to-construct-complex-google-web-search-query#:~:text=However%2C%20if%20you%20want%20to,g)).
  - Ruim: `implementacao quicksort` – genérico. Bom: `"quick sort algorithm" implementation C++ tutorial` (termos
    exatos + idioma)
    ([http - How to construct complex Google Web Search query? - Stack Overflow](https://stackoverflow.com/questions/15852238/how-to-construct-complex-google-web-search-query#:~:text=To%20create%20complex%20,OR)).
  - Ruim: `normas ABNT` – genérico. Bom: `filetype:pdf NBR ABNT normas Brazilian` (filtra PDF e termo específico).

## Estratégia de idioma

- **Idioma padrão (inglês):** consulte em inglês inicialmente, pois há maior volume de conteúdo relevante em tecnologia
  e ciência. Mesmo que você seja hispanofalante ou lusófono, pesquisar em inglês amplia fontes de soluções, códigos de
  exemplo e literatura. Ferramentas como Google permitem filtrar idioma via parâmetro (ex.: `lr=lang_en`)
  ([http - How to construct complex Google Web Search query? - Stack Overflow](https://stackoverflow.com/questions/15852238/how-to-construct-complex-google-web-search-query#:~:text=For%20the%20language%20of%20the,query%20string%20becomes%20as%20follow))
  ou pelo domínio regional (google.com, google.com.br, etc.).

- **Contexto regional:** se o problema é específico ao contexto nacional ou idioma local (leis do Brasil, documentação
  em português, padrões ABNT etc.), então pesquise em português. Ajuste a consulta para o português (ex.:
  `normas NBR-6023 referência bibliográfica`) ou use o Google Brasil. No caso de APIs nacionais ou empresas brasileiras,
  procure por documentação local (ex.: `Expressões regulares JavaScript MDN Português`). Usar o idioma apropriado
  garante encontrar documentos oficiais e discussões locais que não aparecem em inglês.

**Fontes:** os conteúdos acima combinam informações de documentação oficial das APIs e guias de busca
([Custom Search JSON API  |  Programmable Search Engine  |  Google for Developers](https://developers.google.com/custom-search/v1/overview#:~:text=Custom%20Search%20JSON%20API%20provides,to%2010k%20queries%20per%20day))
([Brave Search API | Brave](https://brave.com/search/api/#:~:text=Image%3A%20currency%20icon))
([http - How to construct complex Google Web Search query? - Stack Overflow](https://stackoverflow.com/questions/15852238/how-to-construct-complex-google-web-search-query#:~:text=However%2C%20if%20you%20want%20to,g))
([http - How to construct complex Google Web Search query? - Stack Overflow](https://stackoverflow.com/questions/15852238/how-to-construct-complex-google-web-search-query#:~:text=To%20create%20complex%20,OR)).
Cada técnica foi ilustrada com exemplos práticos e referências para facilitar implementações em Node.js.
