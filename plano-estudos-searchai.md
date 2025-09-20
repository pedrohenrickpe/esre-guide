
# Módulo 0 — Ambiente, visão geral e metas

**Tempo:** 0,5 semana

**Objetivos**

* Preparar o ambiente (Elastic Cloud/Serverless ou auto-gerenciado).
* Entender onde ficam as peças (Elasticsearch, Kibana, Inference, Playground, APIs).
* Definir metas mensuráveis (recall\@k, p95, custo/consulta).

**Entregáveis**

* Cluster criado, usuário e API keys.
* Índice “sandbox” para testes + acesso ao Kibana/Dev Tools e Search Profiler.

**Leituras**

* “Get started” e visão geral do data store. ([Elastic][1])
* APIs do Elasticsearch (base). ([Elastic][2])

**Práticas guiadas**

* Rodar a primeira busca e inspecionar no Kibana Dev Tools. ([Elastic][3])
* Abrir o Search Profiler para entender plano de execução. ([Elastic][4])

---

# Módulo 1 — Fundamentos de busca (full-text)

**Tempo:** 1 semana

**Objetivos**

* Dominar índice invertido, análise de texto e similaridade BM25/TF-IDF.
* Entender mapeamentos e parâmetros de análise (index vs search time).

**Entregáveis**

* Prova de conceito com `text` fields, diferentes analisadores e uma query de relevância.

**Leituras**

* Conceitos de análise de texto, anatomia de analyzer, `analyzer`/`search_analyzer`. ([Elastic][5])
* Como o full-text funciona + BM25 (default). ([Elastic][6])
* Similarity & BM25 + opções em `text`. ([Elastic][7])

**Práticas guiadas**

* Criar índices com analisadores diferentes e usar `_analyze` para inspecionar tokens. ([Elastic][8])
* Testar impacto de `search_analyzer` e sinônimos; comparar scores BM25.

**Checklist de avaliação**

* **NDCG\@10** e **Precision\@k** em 5–10 consultas de teste via `_rank_eval`. ([Elastic][9])

---

# Módulo 2 — Ingestão & pipelines, mapeamentos e tuning

**Tempo:** 1 semana

**Objetivos**

* Construir pipelines de ingestão (normalização, enrich, nested), observar custo/perf.
* Fixar boas práticas de mapeamento e `index_options`.

**Entregáveis**

* Pipeline com 3+ processors (ex.: `set`, `gsub`, `enrich`), simulado com `ingest simulate`.

**Leituras**

* Ingest pipelines + referência de processors. ([Elastic][10])
* `index_options`, `doc_values`, NRT e segmentos. ([Elastic][11])

**Práticas guiadas**

* Criar 2 índices (um com normalização agressiva e outro minimalista) e comparar latência/score.
* Usar `index sorting` quando fizer sentido (ex.: por timestamp). ([Elastic][12])

---

# Módulo 3 — Fundamentos de Vector Database no Elasticsearch

**Tempo:** 1 semana

**Objetivos**

* Entender arquitetura como Vector DB integrada, diferenças vs Pinecone/Milvus.
* Escolher entre `dense_vector`, `sparse_vector` (ELSER) e `semantic_text`.

**Entregáveis**

* Mini-índice com `dense_vector` (“bring your own vectors”) e busca kNN.
* Outro mini-índice com `sparse_vector` (ELSER) para comparar.

**Leituras**

* Vector search overview + dense/sparse. ([Elastic][13])
* “Bring your own vectors”. ([Elastic][14])
* AI-powered search (visão geral). ([Elastic][15])

**Práticas guiadas**

* Indexar embeddings (ex.: modelo E5/Bedrock/OpenAI) via Inference API; testar `knn`. ([Elastic][16])

---

# Módulo 4 — ANN, métricas e compressão (BBQ), filtros ACORN

**Tempo:** 1–1,5 semanas

**Objetivos**

* Dominar HNSW, métricas (cosine, dot, L2) e `num_candidates`/`k`.
* Aplicar **Better Binary Quantization (BBQ)** e entender trade-offs.
* Usar **ACORN** para busca vetorial **com filtro** ultrarrápida (9.1).

**Entregáveis**

* Benchmark com/sem BBQ e com/sem filtros (atributos/category/date), comparando p95 e recall\@k.
* Tabela de custos: memória vs recall/latência.

**Leituras**

* BBQ: blog técnico, “vs PQ”, e referência de configuração. ([Elastic][17])
* O que há de novo no 9.1: **BBQ por padrão** e **ACORN**. ([Elastic][18])
* ACORN (filtered HNSW) e filtragem eficiente. ([Elastic][19])

**Práticas guiadas**

* Reindexar um conjunto com vetores float32, ativar BBQ e comparar NDCG/latência.
* Testar filtros (por exemplo, `brand`, `locale`, `published_at`) no kNN com ACORN e medir speedup.

**Checklist de avaliação**

* **Recall\@k** ≥ meta (ex.: 0,9 em k=20) com BBQ habilitado.
* **Latência p95** antes/depois (objetivar ≥2–5× ganho com BBQ/ACORN, conforme release). ([Elastic][18])

---

# Módulo 5 — Busca semântica avançada com `semantic_text`

**Tempo:** 1–1,5 semanas

**Objetivos**

* Explorar `semantic_text`: chunking automático, embeddings no ingest, highlighting semântico.
* Usar `inference_id`/endpoints e integrar provedores externos (OpenAI, Anthropic, Bedrock, HF).

**Entregáveis**

* Índice com `semantic_text` + highlights semânticos e explicabilidade (mostrar trechos).
* POC com **dois** provedores de embeddings via Inference API.

**Leituras**

* `semantic_text` (mapeamento, chunking, fields como “chunks”, highlight). ([Elastic][20])
* Tutorial de semantic search com `semantic_text`. ([Elastic][21])
* Inference API (integrações: OpenAI/Bedrock/HF/Anthropic etc.). ([Elastic][16])

**Práticas guiadas**

* Comparar `semantic_text` vs `dense_vector` (embeddings BYO) em NDCG\@10 e tempo de ingest.
* Configurar **highlighting semântico** no `_search` e validar fragmentos retornados. ([Elastic][22])

---

# Módulo 6 — Hybrid Search (BM25 + Vetorial/Semântica)

**Tempo:** 1 semana

**Objetivos**

* Combinar BM25 e vetorial com **RRF**; entender normalização e trade-offs de latência.
* Explorar tutoriais com `semantic_text` + full-text.

**Entregáveis**

* Pipeline híbrido usando **RRF retriever** e/ou `rank.rrf` no `_search`.
* Comparativo: somente BM25, somente vetorial, híbrido (melhor NDCG/MRR).

**Leituras**

* Hybrid search (overview e tutorial com `semantic_text`). ([Elastic][23])
* RRF: referência e retriever. ([Elastic][24])
* Retrieval: retrievers overview. ([Elastic][25])

**Práticas guiadas**

* Testar pesos implícitos do RRF e limites de **num\_candidates** da perna vetorial.
* Avaliar **MRR** e **Precision\@k** vs latência p95.

---

# Módulo 7 — RAG (Retrieval-Augmented Generation) com Elastic

**Tempo:** 1–2 semanas

**Objetivos**

* Construir pipelines de RAG com Elasticsearch como Vector Store.
* Controlar chunking, top-k, citações de fonte e mitigação de alucinações.

**Entregáveis**

* POC no **RAG Playground** com citações e guardrails.
* Variante programática (API) com retrievers + re-ranking.

**Leituras**

* RAG (overview) + **Playground** (chat + ver/editar queries, otimizar contexto). ([Elastic][26])

**Práticas guiadas**

* No Playground, ajustar top-k, filtros, campos consultados, e comparar qualidade/fonte.
* Exportar o código gerado pelo Playground para app própria. ([Elastic][27])

---

# Módulo 8 — Ranking & Re-ranking (Elastic Rerank, LTR via Eland)

**Tempo:** 1–1,5 semanas

**Objetivos**

* Aplicar **Elastic Rerank** (cross-encoder) e comparar com Cohere/OpenAI Rerank via Inference.
* Treinar pipeline **Learning to Rank (LTR)** com **Eland** (XGBoost/LightGBM) e usar como rescorer.

**Entregáveis**

* Re-rank do top-k híbrido com `text_similarity_reranker`.
* Notebook de treino LTR com Eland e implantação do modelo no cluster.

**Leituras**

* Guia de **ranking & reranking** + semântico. ([Elastic][28])
* **Elastic Rerank** e API de inference de rerank. ([Elastic][29])
* **LTR** overview + treino/deploy com **Eland**. ([Elastic][30])

**Práticas guiadas**

* Medir ganhos de **NDCG\@10** com Rerank (20→200 candidatos).
* Treinar um LTR com features (BM25, frescor, popularidade via `rank_feature`) e usar `rescore`. ([Elastic][31])

**Checklist de avaliação**

* **NDCG\@10** ↑ vs baseline híbrido, sem p95 degradar além de X%.
* Documentar custo/consulta ao ativar re-ranking.

---

# Módulo 9 — MCP (Model Context Protocol) + agentes/LLMs

**Tempo:** 0,5–1 semana

**Objetivos**

* Entender MCP e conectar agentes (Claude Desktop, OpenAI Agents, etc.) ao Elasticsearch.
* Medir impacto em latência e throughput quando a orquestração é feita por agentes.

**Entregáveis**

* MCP server do Elasticsearch ligado ao seu cluster e testado com um cliente MCP.
* Exemplo de “ferramenta” MCP que consulta índices e cita fontes.

**Leituras**

* **Elasticsearch MCP server** (docs oficiais) + posts de arquitetura. ([Elastic][32])
* O que é MCP (site oficial, Anthropic/OpenAI/LangChain). ([Model Context Protocol][33])

**Práticas guiadas**

* Configurar servidor MCP com FastMCP (exemplo) conectado ao ES e executar queries naturais. ([Elastic][34])
* Medir overhead end-to-end (RAG + agente) em relação ao pipeline direto.

---

# Módulo 10 — Observabilidade, métricas e SLOs (recall/latência/custo)

**Tempo:** 1 semana

**Objetivos**

* Monitorar **recall e precisão** (offline) e **latência p95/throughput** (online).
* Construir dashboards Kibana e alertas (ex.: latência acima do SLO).

**Entregáveis**

* Dashboard com p50/p95/p99 e throughput de consultas.
* Regra de alerta de latência no Kibana (ex.: p95 > 1,5s por 5 min).

**Leituras/recursos**

* Ranking evaluation API (`_rank_eval`) para NDCG/MRR/Precision. ([Elastic][9])
* Search Profiler (debug de queries). ([Elastic][4])
* Criar alerta de latência (APM → threshold rule). ([Elastic][35])

**Práticas guiadas**

* Suite offline com `_rank_eval` rodando diariamente (job) e comparação temporal. ([Elastic][36])
* Adicionar logs de custo/consulta (dimensão: modelo de embedding, k, num\_candidates).
* Investigar outliers com Profile API. ([Elastic][37])

---

# Módulo 11 — Novidades do Elasticsearch 9.1.x (BBQ por padrão, ACORN, token pruning)

**Tempo:** 0,5 semana

**Objetivos**

* Atualizar o stack e habilitar ganhos de performance de 9.1.
* Entender **token pruning** para `sparse_vector`/ELSER e otimizações de `semantic_text`.

**Entregáveis**

* Plano de upgrade + teste A/B (8.x/9.x ou 9.0→9.1) com KPIs.

**Leituras**

* “What’s new 9.1”: **BBQ por padrão**, **ACORN**, **token pruning**. ([Elastic][18])
* Anúncio ACORN + BBQ por padrão (release/IR). ([ir.elastic.co][38])
* Blog 9.1 (BBQ & ACORN). ([Elastic][39])

**Práticas guiadas**

* Medir “antes/depois” em filtros complexos (ACORN) e em memória/latência com BBQ default.

---

# Projeto Final — Search AI de ponta a ponta

**Tempo:** 1–2 semanas

**Escopo**

1. **Ingestão**: pipeline (limpeza + enrich), mapeamentos para `text`, `semantic_text`, e um campo `dense_vector` BYO para comparação. ([Elastic][10])
2. **Busca híbrida**: combinar BM25 + semântica via **RRF** (+ filtros) e **ACORN** em queries filtradas. ([Elastic][23])
3. **RAG**: montar RAG no **Playground**, ativar citações e exportar o código; top-k e chunking ajustados. ([Elastic][40])
4. **Re-ranking**: aplicar **Elastic Rerank** para o top-k; treinar um **LTR** simples com Eland e comparar. ([Elastic][29])
5. **MCP**: expor o índice a um cliente MCP (Claude/OpenAI Agents) para NLQ com citações. ([Elastic][32])
6. **Métricas & SLOs**:

   * **Offline**: `_rank_eval` com **NDCG\@10, MRR, Precision\@k**. ([Elastic][9])
   * **Online**: **p95**, throughput, custo/consulta (por estratégia), com alerta de latência no Kibana. ([Elastic][35])

**Critérios de aceite**

* **NDCG\@10** do híbrido ≥ 10–20% sobre apenas BM25.
* **p95** do híbrido+rerank dentro do SLO acordado (ex.: ≤ 1200 ms).
* **Custo/consulta** reportado por caminho (BM25, vetorial, híbrido, +rerank).
* Dashboard Kibana + documentação de arquitetura e resultados.

---

## Exercícios rápidos por módulo (exemplos)

* **M1**: test-drive de analisadores em 3 idiomas; comparar score BM25 em `match` vs `combined_fields`. ([Elastic][41])
* **M3**: “Bring your own vectors” com E5 e OpenAI (`text-embedding-3-large`), variar dimensão. ([Elastic][14])
* **M4**: Habilitar **BBQ** e medir memória/latência; ativar **rescore\_vector** para oversampling de quantizados (quando aplicável). ([Elastic][42])
* **M5**: `semantic_text` com highlight semântico; recuperar “chunks” indexados para debug. ([Elastic][20])
* **M6**: Híbrido com **RRF retriever** + filtros; normalizar pesos e comparar “blends”. ([Elastic][43])
* **M7**: Playground — ajustar campos consultados no “View query” e observar efeito no recall. ([Elastic][44])
* **M8**: Rodar **Elastic Rerank** em 100 candidatos e avaliar **NDCG/MRR**; treinar LTR com Eland (XGBoost) e comparar com o cross-encoder. ([Elastic][29])
* **M9**: Conectar Claude Desktop ao **Elasticsearch MCP server** e perguntar “mostre as 5 fontes mais relevantes com trechos”. ([Elastic][32])
* **M10**: Criar regra de alerta p95>1,5s (5 min); investigar outliers com Search Profiler. ([Elastic][35])

---

## Referências oficiais (base mínima obrigatória)

* **Elastic AI-powered search (overview)**. ([Elastic][15])
* **Vector database fundamentals / Vector search**. ([Elastic][13])
* **Semantic search** (+ `semantic_text`). ([Elastic][45])
* **Hybrid search (RRF)**. ([Elastic][23])
* **RAG** + **RAG Playground**. ([Elastic][26])
* **Ranking** (Elastic Rerank, LTR/Eland). ([Elastic][28])
* **MCP** (docs gerais + Elasticsearch MCP server). ([Model Context Protocol][33])
* **Release notes 9.1.x** (BBQ por padrão, ACORN, token pruning). ([Elastic][18])

---

## Dicas de especialista (ao longo do estudo)

* **Escolha de vetores**: use `semantic_text` para velocidade de entrega e chunking automático; troque para `dense_vector` BYO quando quiser controlar modelo/dimensão/normalização. ([Elastic][21])
* **Filtros em kNN**: em cenários com muitos filtros (catálogo, compliance, regionais), teste **ACORN** — 9.1 traz integração do filtro no grafo HNSW e habilita filtros definidos **após** a ingestão. ([Elastic][18])
* **Custo/latência**: quantização **BBQ** reduz memória (\~32×) e acelera consultas (2–5×) mantendo ranking quality em top-10, ótimo para produção. ([Elastic][17])
* **Avaliação contínua**: automatize `_rank_eval` (NDCG/MRR/Precision\@k) em dataset estável; em produção, acompanhe **p95/p99** e **throughput** por tipo de consulta. ([Elastic][9])
* **Re-ranking**: use **Elastic Rerank** (cross-encoder) quando precisar subir a precisão do top-10; para personalização, complemente com **LTR** e features de negócio. ([Elastic][29])

---


[1]: https://www.elastic.co/docs/get-started?utm_source=chatgpt.com "Get started | Elastic Docs"
[2]: https://www.elastic.co/docs/api/doc/elasticsearch/?utm_source=chatgpt.com "Elasticsearch API documentation"
[3]: https://www.elastic.co/docs/api/doc/elasticsearch/operation/operation-search?utm_source=chatgpt.com "Run a search | Elasticsearch API documentation"
[4]: https://www.elastic.co/docs/explore-analyze/query-filter/tools/search-profiler?utm_source=chatgpt.com "Search profiler | Elastic Docs"
[5]: https://www.elastic.co/docs/manage-data/data-store/text-analysis/concepts?utm_source=chatgpt.com "Concepts | Elastic Docs"
[6]: https://www.elastic.co/docs/solutions/search/full-text/how-full-text-works?utm_source=chatgpt.com "How full-text search works | Elastic Docs"
[7]: https://www.elastic.co/docs/reference/elasticsearch/index-settings/similarity?utm_source=chatgpt.com "Similarity settings | Reference"
[8]: https://www.elastic.co/docs/manage-data/data-store/text-analysis/test-an-analyzer?utm_source=chatgpt.com "Test an analyzer | Elastic Docs"
[9]: https://www.elastic.co/docs/reference/elasticsearch/rest-apis/search-rank-eval?utm_source=chatgpt.com "Ranking evaluation | Reference"
[10]: https://www.elastic.co/docs/manage-data/ingest/transform-enrich/ingest-pipelines?utm_source=chatgpt.com "Elasticsearch ingest pipelines"
[11]: https://www.elastic.co/docs/reference/elasticsearch/mapping-reference/index-options?utm_source=chatgpt.com "index_options | Reference"
[12]: https://www.elastic.co/docs/reference/elasticsearch/index-settings/sorting?utm_source=chatgpt.com "Index sorting settings | Reference"
[13]: https://www.elastic.co/docs/solutions/search/vector?utm_source=chatgpt.com "Vector search in Elasticsearch"
[14]: https://www.elastic.co/docs/solutions/search/vector/bring-own-vectors?utm_source=chatgpt.com "Bring your own dense vectors to Elasticsearch"
[15]: https://www.elastic.co/docs/solutions/search/ai-search/ai-search?utm_source=chatgpt.com "AI-powered search | Elastic Docs"
[16]: https://www.elastic.co/docs/api/doc/elasticsearch/operation/operation-inference-put?utm_source=chatgpt.com "Create an inference endpoint | Elasticsearch API ..."
[17]: https://www.elastic.co/search-labs/blog/better-binary-quantization-lucene-elasticsearch?utm_source=chatgpt.com "Better Binary Quantization in Lucene & Elasticsearch"
[18]: https://www.elastic.co/blog/whats-new-elastic-9-1-0?utm_source=chatgpt.com "Elastic 9.1/8.19: BBQ by default, ES|QL with CCS GA ..."
[19]: https://www.elastic.co/search-labs/blog/filtered-hnsw-knn-search?utm_source=chatgpt.com "Filtered HNSW search, fast mode - Elasticsearch Labs"
[20]: https://www.elastic.co/docs/reference/elasticsearch/mapping-reference/semantic-text?utm_source=chatgpt.com "Semantic text field type | Reference"
[21]: https://www.elastic.co/docs/solutions/search/semantic-search/semantic-search-semantic-text?utm_source=chatgpt.com "Semantic search with semantic_text | Elastic Docs"
[22]: https://www.elastic.co/docs/reference/elasticsearch/rest-apis/highlighting?utm_source=chatgpt.com "Highlighting | Reference"
[23]: https://www.elastic.co/docs/solutions/search/hybrid-search?utm_source=chatgpt.com "Hybrid search | Elastic Docs"
[24]: https://www.elastic.co/docs/reference/elasticsearch/rest-apis/reciprocal-rank-fusion?utm_source=chatgpt.com "Reciprocal rank fusion | Reference"
[25]: https://www.elastic.co/docs/solutions/search/retrievers-overview?utm_source=chatgpt.com "Retrievers | Elastic Docs"
[26]: https://www.elastic.co/docs/solutions/search/rag?utm_source=chatgpt.com "RAG | Elastic Docs"
[27]: https://www.elastic.co/docs/explore-analyze/query-filter/tools/playground?utm_source=chatgpt.com "Playground | Elastic Docs"
[28]: https://www.elastic.co/docs/solutions/search/ranking?utm_source=chatgpt.com "Ranking and reranking | Elastic Docs"
[29]: https://www.elastic.co/docs/explore-analyze/machine-learning/nlp/ml-nlp-rerank?utm_source=chatgpt.com "Elastic Rerank | Elastic Docs"
[30]: https://www.elastic.co/docs/solutions/search/ranking/learning-to-rank-ltr?utm_source=chatgpt.com "Learning To Rank (LTR) | Elastic Docs"
[31]: https://www.elastic.co/docs/reference/query-languages/query-dsl/query-dsl-rank-feature-query?utm_source=chatgpt.com "Rank feature query | Reference"
[32]: https://www.elastic.co/docs/solutions/search/mcp?utm_source=chatgpt.com "Elasticsearch Model Context Protocol (MCP) server"
[33]: https://modelcontextprotocol.io/?utm_source=chatgpt.com "What is the Model Context Protocol (MCP)? - Model Context ..."
[34]: https://www.elastic.co/search-labs/blog/how-to-build-mcp-server?utm_source=chatgpt.com "Building an MCP server with Elasticsearch for real health ..."
[35]: https://www.elastic.co/docs/solutions/observability/incident-management/create-latency-threshold-rule?utm_source=chatgpt.com "Create a latency threshold rule"
[36]: https://www.elastic.co/docs/api/doc/elasticsearch/operation/operation-rank-eval?utm_source=chatgpt.com "Evaluate ranked search results | Elasticsearch API ..."
[37]: https://www.elastic.co/docs/reference/elasticsearch/rest-apis/search-profile?utm_source=chatgpt.com "Profile search requests | Reference"
[38]: https://ir.elastic.co/news/news-details/2025/Elastic-Announces-Faster-Filtered-Vector-Search-with-ACORN-1-and-Default-Better-Binary-Quantization-Compression/default.aspx?utm_source=chatgpt.com "Elastic Announces Faster Filtered Vector Search with ..."
[39]: https://www.elastic.co/search-labs/blog/elasticsearch-9-1-bbq-acorn-vector-search?utm_source=chatgpt.com "Elasticsearch now with BBQ by default & ACORN for ..."
[40]: https://www.elastic.co/docs/solutions/search/rag/playground?utm_source=chatgpt.com "Playground | Elastic Docs"
[41]: https://www.elastic.co/docs/reference/query-languages/query-dsl/query-dsl-combined-fields-query?utm_source=chatgpt.com "Combined fields | Reference"
[42]: https://www.elastic.co/docs/reference/elasticsearch/index-settings/bbq?utm_source=chatgpt.com "Better Binary Quantization (BBQ) | Reference"
[43]: https://www.elastic.co/docs/reference/elasticsearch/rest-apis/retrievers/rrf-retriever?utm_source=chatgpt.com "RRF retriever | Reference"
[44]: https://www.elastic.co/docs/solutions/search/rag/playground-query?utm_source=chatgpt.com "View and modify queries | Elastic Docs"
[45]: https://www.elastic.co/docs/solutions/search/semantic-search?utm_source=chatgpt.com "Semantic search | Elastic Docs"
