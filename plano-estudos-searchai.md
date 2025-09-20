# Roteiro avançado enriquecido — Elasticsearch Search AI (versão 9.1.x)

## Módulo 0 — Visão geral & definição de metas (≈ 0,5 semana)

* **Objetivo**: definir exatamente o escopo do projeto que você vai construir (por exemplo: motor de busca para suporte interno, RAG para FAQ + chat, etc.), identificar dados, casos de uso, métricas.
* **Leituras recomendadas**:

  * “Elastic 9.1 / 8.19: BBQ by default, ES|QL with CCS GA …” — novidades do 9.1. ([Elastic][1])
  * Overview de *semantic search workflows* — Elastic Docs. ([Elastic][2])
* **Entregável**: Documento com

  1. definição de caso de uso
  2. coleção de dados (tipo, volume)
  3. usuários-alvo
  4. métricas desejadas (relevância, recall, latência, custo)

---

## Módulo 1 — Fundamentos de busca textual clássica & ingestion (1 semana)

* **Tópicos**:

  * Índice invertido, analisadores (tokenização, lowercase, stemming, stop words, sinônimos).
  * Modelos de relevância clássicos: BM25, TF-IDF, scoring across shards, consistência de pontuação.
  * Pipeline de ingestão: *bulk*, ingest node pipelines, agentes/connectores.
  * Gerenciamento de shards, réplicas, refresh / merge, uso de filtros e prefix queries.
* **Leituras/documentações**:

  * Documentação geral de *text search*, match / bool queries. (documentação oficial Elastic)
  * **Semantic search overview** (para entender como o texto puro se conecta com semântica). ([Elastic][2])
* **Exercícios práticos**:

  1. Criar índices com diferentes analisadores (português, multilíngue), comparar resultados de buscas exatas vs. fuzzy.
  2. Medir latência / throughput com diferentes configurações de shards/réplicas.

---

## Módulo 2 — Vetores densos e esparsos; mapeamento & storage (1,5 semana)

* **Tópicos**:

  * Campo `dense_vector`: limitações (dimensão, tipo de métrica), uso com HNSW, efeitos de cardinalidade.
  * Campo `sparse_vector` / uso com ELSER (expansão semântica esparsa), tradeoffs de custo vs recall.
  * Qual campo usar quando: `semantic_text` vs dense\_vector vs sparse\_vector. ([Elastic][3])
  * Armazenamento / compressão: **BBQ (Better Binary Quantization)** — como funciona, ganhos, situações ideais. ([Elastic][4])
  * Filtragem vetorial: melhoria com ACORN (vector search + filter juntas). ([Elastic][1])

* **Leituras/documentações**:

  * “Better Binary Quantization (BBQ)” — Elastic reference. ([Elastic][4])
  * ACORN info via o blog de lançamento 9.1. ([Business Wire][5])
  * Documentação de semantic\_text field type. ([Elastic][6])

* **Hands-on / exercícios**:

  1. Construir dois índices: um com dense\_vector + BBQ, outro sem quantização (float32), comparar latência, uso de memória, qualidade de ranking para top-k queries.
  2. Um experimento com sparse vectors (ELSER) numa base multilíngue, medindo recall e precisão vs. dense.
  3. Testar ACORN filtrado: criar filtros realistas (por categoria, data, entidade etc.) e medir performance.

---

## Módulo 3 — `semantic_text` & Inference API (1,5 semana)

* **Tópicos**:

  * O que é `semantic_text`: campo de mapeamento que automaticamente gera embeddings via endpoint de inferência, chunking automático, inferência tanto na indexação quanto na consulta. ([Elastic][6])
  * Parâmetros de `semantic_text`: `inference_id`, `search_inference_id`, configurações de *chunking\_settings*, index\_options. ([Elastic][6])
  * Inferência customizada: criar endpoints inferência via Inference API, escolha de modelo, latency de endpoint, escalonamento.
  * Destaques GA do `semantic_text`: mudança na representação em `_source` para menos verbosidade, real integração com highlighting. ([Elastic][7])

* **Leituras/documentações**:

  * *Semantic text field type* — Elastic Reference. ([Elastic][6])
  * Tutorial “Semantic search with semantic\_text”. ([Elastic][8])
  * Blog “Semantic text in Elasticsearch: Simpler, better, leaner, stronger”. ([Elastic][7])

* **Exercícios práticos**:

  1. Criar índice com `semantic_text` para um corpus específico, sem fornecer `inference_id` (usar padrão) e com `inference_id` customizado, comparar diferenças.
  2. Explorar como chunking settings afetam recuperação de contexto longo.
  3. Usar highlighting semântico para extrair os trechos mais relevantes do texto (útil para RAG).

---

## Módulo 4 — Hybrid Search & RRF (1 semana)

* **Tópicos**:

  * Combinação de buscas lexical + semântica: razão, trade-offs.
  * Uso de `retriever` com **RRF (Reciprocal Rank Fusion)**: como configurar, balancear pesos, como escolher número de retrievers. ([Elastic][9])
  * Estratégias de boosting, ponderação, fallback (por exemplo quando semântica não é confiável).
  * Latência de consultas híbridas, custo adicional de embeddings + combinação.

* **Leituras/documentações**:

  * Tutorial “Hybrid search with semantic\_text”. ([Elastic][9])
  * Documentação de workflows de busca híbrida em *semantic search overview*. ([Elastic][2])

* **Exercícios**:

  1. Construir índice híbrido: campo `semantic_text` + campo `text` tradicional, com `copy_to`, etc., como no tutorial.
  2. Implementar consulta híbrida (DSL ou ES|QL) usando RRF, comparar resultados vs apenas semântico vs apenas lexical, medir latência / precisão.
  3. Ajustar pesos entre componentes lexical/semântico para seu caso de uso escolhido.

---

## Módulo 5 — Retrievers multi-estágio, pipelines & RAG (1–1,5 semanas)

* **Tópicos**:

  * Estrutura de pipeline de recuperação: estágios, thresholds, *top-k* inicial, reranking.
  * RAG (“Retrieval Augmented Generation”): seleção de contexto, chunking, passage selection, garantir não enviesar, evitar alucinações.
  * Uso do RAG Playground no ES 9.1 — como configurar, boas práticas.
  * Integração com modelos LLM externos (se aplicável) ou uso das inferências internas para geração de texto.

* **Leituras/documentações**:

  * Documentação RAG no Elastic (“Elastic RAG”) — tutoriais oficiais.
  * Blog posts / Search Labs sobre chunking e seleção semântica de contexto.
  * Documentação de semantic highlighting para extrair trechos para contexto. (como parte de `semantic_text` melhorias) ([Elastic][7])

* **Exercícios**:

  1. Implementar pipeline RAG com dados que você possui, com geração de resposta sustentada por contexto recuperado, além de atribuir fonte.
  2. Comparar diferentes tamanhos de contexto (número de chunks) e observar trade-off entre completude/contexto vs latência + uso de tokens.
  3. Testar alucinação (respostas sem base), verificar mecanismos de mitigação — confiança, filtros.

---

## Módulo 6 — Ranking & Re-ranking & LTR (1,5–2 semanas)

* **Tópicos**:

  * Elastic Rerank (cross-encoder) vs LTR: quando usar cada um, custo computacional, pipeline de treinamento, exemplos.
  * Rank\_vectors (campo para interação tardia / late interaction) — modelos como ColBERT, re-ranking de saída do dense/hybrido. Verá nas release notes de 9.0+ como recurso experimental e melhorias. ([Elastic][10])
  * Avaliação de ranking: NDCG, MRR, Precision\@k, Recall\@k; curva de trade-off.
  * Feature engineering para LTR: quais features extrair (relevância lexical, semântica, metadados, fresh vs older docs etc.).

* **Leituras/documentações**:

  * Documentação Elastic de Learning to Rank / re-ranker.
  * Release notes de Elasticsearch 9.0 para rank\_vectors experimental. ([Elastic][10])
  * Tutoriais de avaliação de ranking (benchmarks internos ou conjuntos públicos, como BEIR).

* **Exercícios**:

  1. Treinar um modelo LTR com seu conjunto de dados (pré-julgamentos), usar esse modelo para re-rankar resultados híbridos.
  2. Implementar `rank_vectors` late interaction em experimento, comparar com reranking e ver custo de latência.
  3. Medir métricas de ranking antes/depois (baseline vs LTR vs reranker).

---

## Módulo 7 — MCP (Model Context Protocol) + Search Applications / Templates (1 semana)

* **Tópicos**:

  * O que é MCP, como servidor MCP permite agentes conversar com índice / dados em linguagem natural.
  * Search Applications: construção de aplicações de busca, dashboards de UI, templates de busca, parametrização.
  * Search Templates & Mustache ou uso de variáveis — permitir queries parametrizadas sem reinventar DSL.
* **Leituras/documentações**:

  * Elastic Docs: MCP Solution page.
  * Documentação de Search Applications & Search Templates.
* **Exercícios**:

  1. Implantar um agente simples via MCP que responde perguntas baseadas no índice que você construiu.
  2. Criar algumas *search templates* e integrá-las a uma interface ou via API simulada.

---

## Módulo 8 — Avaliação, Observabilidade & todas métricas relevantes (1 semana)

* **Tópicos**:

  * Métricas de relevância: NDCG, MRR, Recall\@k / Precision\@k, Success\@k.
  * Métricas de eficiência: latência por etapa (indexação, inferência, consulta híbrida, reranking), throughput, uso de memória, custo computacional.
  * Monitoramento & logs: quero ver queries lentas, queries com baixa relevância, padrões de uso, erros de inferência.
  * Definição de SLOs (Service Level Objectives) para busca: p.ex., 95% consultas < X ms, recall mínimo, etc.
* **Leituras/documentações**:

  * Elastic observability / performance tuning / release notes de 9.1 com BBQ/ACORN implicando performance. ([Elastic][1])
  * Documentos de benchmarking e exemplos BEIR ou conjuntos de avaliação públicos.
* **Exercícios**:

  1. Criar um conjunto de consultas “canônicas” que representam cargas reais no seu caso de uso; rodar benchmark comparativo (baseline vs híbrido vs com reranking).
  2. Medir latência fim a fim; gerar dashboard com métricas principais.
  3. Fazer tune de configuração (ex: ajustar oversampling em BBQ, parâmetros de HNSW, filtro via ACORN, chunking) e observar impactos.

---

## Módulo 9 — Novidades & tuning em 9.1.x (0,5–1 semana)

* **Tópicos**:

  * BBQ por padrão: como ele está ativado, quando aplicar, limites (por dimensão do vetor, compatibilidade). ([Elastic][1])
  * ACORN-1: filtragem vetorial integrada no grafo HNSW, prós/contras, custo de ingest/query. ([Business Wire][5])
  * Token pruning para sparse vectors — habilitado por padrão. Efeitos nos vetores esparsos. ([Elastic][1])
  * Melhorias de semântica: representações mais leves em `_source`, destaque, integração com highlighting. ([Elastic][7])

* **Hands-on**:

  1. Habilitar / desabilitar BBQ, medir diferença de uso memória / latência / qualidade.
  2. Testar ACORN em consultas filtradas realistas.
  3. Verificar impacto do pruning de tokens em sparse vectors na relevância.

---

## Projeto final (1–2 semanas)

* **Entregável completo** contendo:

  1. Índice com campos apropriados (lexical, semântico, vetorial), ingestão pipeline configurada.
  2. Busca híbrida + RAG + re-ranking (ou LTR ou rank\_vectors).
  3. Integração via templates ou aplicativo / MCP para interface de uso.
  4. Painel de métricas com benchmarks pretos (baseline) e ajustes aplicados.
  5. Documentação do sistema: decisões tomadas, trade-offs, valores de parâmetro, sugestões de melhoria futura.

* **Critérios de avaliação**:

  * Relevância (ex: NDCG / MRR) com baseline bem definido
  * Latência (target definido)
  * Uso de recursos (memória, custo de inferência)
  * Robustez (resiliência a consultas adversas, diversidade de linguagem, cobertura de casos de borda)

---

## Cronograma sugerido (resumido com estimativas)

* Semana 1: Fundamentos + vetores básicos
* Semana 2: vetores avançados + `semantic_text`
* Semana 3: Hybrid search + RAG pipelines
* Semana 4: Ranking / re-ranking / LTR
* Semana 5: MCP / Search Apps / Templates + avaliação
* Semana 6: Novidades 9.1 + projeto final

---


[1]: https://www.elastic.co/blog/whats-new-elastic-9-1-0?utm_source=chatgpt.com "Elastic 9.1/8.19: BBQ by default, ES|QL with CCS GA ..."
[2]: https://www.elastic.co/docs/solutions/search/semantic-search?utm_source=chatgpt.com "Semantic search | Elastic Docs"
[3]: https://www.elastic.co/search-labs/blog/mapping-embeddings-to-elasticsearch-field-types?utm_source=chatgpt.com "Mapping embeddings to Elasticsearch field types"
[4]: https://www.elastic.co/docs/reference/elasticsearch/index-settings/bbq?utm_source=chatgpt.com "Better Binary Quantization (BBQ) | Reference"
[5]: https://www.businesswire.com/news/home/20250730532740/en/Elastic-Announces-Faster-Filtered-Vector-Search-with-ACORN-1-and-Default-Better-Binary-Quantization-Compression?utm_source=chatgpt.com "Elastic Announces Faster Filtered Vector Search with ..."
[6]: https://www.elastic.co/docs/reference/elasticsearch/mapping-reference/semantic-text?utm_source=chatgpt.com "Semantic text field type | Reference"
[7]: https://www.elastic.co/search-labs/blog/elasticsearch-semantic-text-ga?utm_source=chatgpt.com "Semantic text in Elasticsearch: Simpler, better, leaner, ..."
[8]: https://www.elastic.co/docs/solutions/search/semantic-search/semantic-search-semantic-text?utm_source=chatgpt.com "Semantic search with semantic_text | Elastic Docs"
[9]: https://www.elastic.co/docs/solutions/search/hybrid-semantic-text?utm_source=chatgpt.com "Hybrid search with semantic_text"
[10]: https://www.elastic.co/docs/release-notes/elasticsearch?utm_source=chatgpt.com "Elasticsearch release notes | Reference"
