
---

# Sumário executivo (5–10 linhas)

Você vai construir, avaliar e operar um pipeline de **RAG de produção** com Elasticsearch como **Vector DB**. O programa equilibra fundamentos (LLMs, embeddings e chunking), prática de **vector search kNN** e **busca híbrida (BM25+vetorial via RRF)**, ingestão de PDFs com pipelines e **mascaração de PII**, um **aplicativo FastAPI** (Python) com instrumentação (APM/Logs/Metrics), e **avaliação** contínua (RAGAS/TruLens). O Elasticsearch será configurado com `dense_vector` (similaridade adequada ao seu embedding), **kNN (HNSW)** e filtros; você também aprenderá **reranking semântico** para melhorar precisão. Encerramos com um **capstone** com critérios de aceitação, **checklist de go-live**, **ILM**, **snapshot/restore** e RBAC/API Keys. ([Elastic][1])

---

# Parâmetros (defaults assumidos e documentados)

* **Nível atual:** intermediário em Elastic; iniciante em LLM
* **Carga:** 8 semanas • 8h/semana
* **Stack:** Python + FastAPI (LangChain opcional)
* **Dados:** PDFs técnicos/manuais internos
* **Requisitos:** PT-BR primeiro; execução local/on-prem; sem serviços externos obrigatórios (pode usar modelos locais de embedding)

---

# Cronograma sugerido (8 semanas)

* **S1. Fundamentos + Setup local & segurança básica**
* **S2. Embeddings & chunking (laboratórios)**
* **S3. Elasticsearch como Vector DB: mapping, kNN & filtros**
* **S4. Ingestão de PDFs, pipelines & PII masking**
* **S5. Busca híbrida (BM25+vetor) + reranking**
* **S6. App RAG (FastAPI) + citações + caching**
* **S7. Avaliação (offline/online) + tuning + custos**
* **S8. Observabilidade (APM/Logs/Metrics), ILM, backup & go-live**

---

# Módulos (objetivos, conteúdo, leituras, labs, métricas, entregáveis, tempo)

## Módulo 1 — Fundamentos, setup e segurança (8h)

**Objetivos**: entender LLMs, contexto, RAG; subir ES+Kibana; configurar autenticação.
**Conteúdo**: tokenização, janelas de contexto, alucinações; visão geral kNN HNSW; **RBAC & API Keys**. ([Elastic][2])
**Leituras oficiais**: instalar com Docker/Compose; RBAC; API keys. ([Elastic][3])
**Lab**:

1. **Subir** ES+Kibana via Docker Compose (single-node) e testar `/_cluster/health`. ([Elastic][3])
2. **Criar** um **API key** de acesso para a aplicação. ([Elastic][4])
   **Métricas**: cluster “green”; latência do `/_search` < 50ms (coleção pequena).
   **Entregáveis**: compose, script de bootstrap, API key rotacionável.

---

## Módulo 2 — Embeddings & Chunking (8h)

**Objetivos**: escolher embedding, definir dimensão/similaridade, estratégias de chunking.
**Conteúdo**: **cosine vs dot_product vs l2_norm** e pré-requisitos (ex.: **dot_product** requer vetores normalizados a unidade); tamanho/overlap dos chunks; títulos; tabelas/figuras. ([Elastic][5])
**Leituras oficiais**: `dense_vector` (parâmetros, similaridade). ([Elastic][1])
**Lab**: gerar embeddings (modelo local), normalizar L2 quando for usar `dot_product`, salvar JSONL (texto, metadados e vetor).
**Métricas**: cobertura de chunking (>95% dos tokens originais cobertos), taxa média de perda semântica via avaliação qualitativa.

---

## Módulo 3 — Elasticsearch como Vector DB (8h)

**Objetivos**: mapear `dense_vector`, indexar e consultar com **kNN** (com filtros).
**Conteúdo**: mapping com `dims` e `similarity`; **kNN (HNSW)**; parâmetros **`k`** e **`num_candidates`**; **filtered kNN**; *trade-off* latência/recall; noções de **quantização** (int8/int4/BBQ). ([Elastic][2])
**Leituras oficiais**: `dense_vector` + kNN + tuning; filtered kNN. ([Elastic][1])
**Lab** (exemplos mínimos):

* **Mapping** (cosine):

  ```json
  PUT docs
  {
    "mappings": {
      "properties": {
        "text": { "type": "text" },
        "vec":  { "type": "dense_vector", "dims": 384, "similarity": "cosine" }
      }
    }
  }
  ```
* **Busca kNN** com filtro:

  ```json
  POST docs/_search
  {
    "knn": { "field": "vec", "query_vector": [/*...*/], "k": 10, "num_candidates": 200 },
    "query": { "term": { "file_type": "pdf" } }
  }
  ```

**Métricas**: P50 < 150ms (coleção ~100k), **recall@10** vs brute-force; *sweep* de `num_candidates`. ([Elastic][6])
**Entregáveis**: índice versionado, scripts de ingest, consultas kNN reprodutíveis.

---

## Módulo 4 — Ingestão de PDFs & PII (8h)

**Objetivos**: extrair texto de PDFs e **mascarar PII** via ingest pipelines.
**Conteúdo**: **Attachment processor (Tika)**; **gsub** para regex; **script processor** (Painless); ordem de processadores; testes. ([Elastic][7])
**Leituras oficiais**: catálogo de **ingest processors**. ([Elastic][8])
**Lab**:

* Pipeline `pdf_ingest` com `attachment` → `gsub` (mascarar e-mails/CPFs) → `set` de metadados. ([Elastic][7])
  **Métricas**: 0 vazamentos em amostra manual; tempo médio de ingestão por doc; % de erros de parsing.

---

## Módulo 5 — **Busca híbrida** (BM25 + Vetorial) e **Reranking** (8h)

**Objetivos**: combinar BM25 e vetorial com **RRF** e aplicar **reranking semântico**.
**Conteúdo**: **RRF** (Reciprocal Rank Fusion) para fundir resultados; **retrievers** (kNN, RRF, linear, rescorer); **text_similarity_reranker** (cross-encoder). ([Elastic][9])
**Leituras oficiais**: ranking & reranking; RRF retriever. ([Elastic][10])
**Lab**:

* Montar consulta com **RRF** (BM25 + kNN) e, opcionalmente, **rerank** top-100. ([Elastic][11])
  **Métricas**: nDCG@10, sucesso de resposta humana; impacto de reranking vs baseline híbrido.

---

## Módulo 6 — App **RAG** (FastAPI) com citações (8h)

**Objetivos**: construir API de pergunta-resposta com **citações** e streaming.
**Conteúdo**: *retriever* (híbrido), formatação de **prompts** com trechos; deduplicação; **caching** de embeddings/consultas; integração opcional com **LangChain** (Elasticsearch retriever/vector store). ([LangChain][12])
**Lab**:

* FastAPI com rotas `/ask` → pipeline: (retrieve → opcional rerank → montar contexto → chamar LLM local) + retorno de **citações** (IDs/URLs do ES).
  **Métricas**: latência P50/P95; taxa de respostas com citação; custo por 1k requisições.

---

## Módulo 7 — Avaliação & Tuning (8h)

**Objetivos**: criar **golden set**; avaliação offline (RAGAS/TruLens) e online (AB).
**Conteúdo**: geração sintética de perguntas; métricas (precisão, **nDCG@k**, **faithfulness**), **recall@k**; *sweeps* de `k`, `num_candidates`, pesos do híbrido; checklist de regressão. ([Elastic][6])
**Lab**: pipeline de avaliação (job noturno) + dashboard de métricas.

---

## Módulo 8 — Observabilidade, ILM, Backup & Go-Live (8h)

**Objetivos**: instrumentar APM/Logs/Metrics; configurar **ILM** e **snapshots**.
**Conteúdo**: **Elastic APM** (traces, métricas, logs), painéis e alertas; **ILM** (fases hot/warm/cold…); **snapshot & restore** (backup S3/NFS); RBAC granular e rotação de **API Keys**. ([Elastic][13])
**Lab**:

* Instrumentar o app (OpenTelemetry/Agent), criar painéis mínimos (latência, erro, custo estimado), **ILM** para índices de chunks e logs, rotina SLM de snapshots. ([Elastic][14])
  **Métricas**: SLO latência (ex.: P95 < 1200ms), erro HTTP < 1%; tempo de **restore** validado.

---

# Projeto Integrador (Capstone)

**Tema**: Assistente de suporte técnico (PT-BR) para base de PDFs internos.
**Funcionais**: consulta híbrida + citações; filtros por produto/versão; painel de feedback.
**Não-funcionais**: P95 < 1200ms; precisão (human-rated) ≥ 0,7; *error budget* mensal ≤ 1%.
**Critérios de aceite**: suite de avaliação (≥200 Q/A), rollback de índice, restore testado.
**Plano de testes**: testes unit/integration para ingest, retrieval, reranking e geração; canário.
**Checklist de produção**: RBAC + API key por serviço; APM ativo; ILM aplicado; snapshot recente; DR runbook.

---

# Guia rápido de comandos (trechos ilustrativos)

* **Mapping** `dense_vector` (384-d, cosine): ver Módulo 3. ([Elastic][1])
* **kNN** com filtro: ver Módulo 3 (observando `k` e `num_candidates`). ([Elastic][15])
* **RRF** (BM25 + kNN) e **retrievers**: usar **RRF retriever** (ou `rank` RRF). ([Elastic][11])
* **Attachment & PII**: `attachment` + `gsub`/`script` em pipeline de ingest. ([Elastic][7])
* **APM & ILM & Snapshots**: docs oficiais. ([Elastic][13])

---

# Plano de avaliação (RAG offline/online)

* **Offline**: RAGAS/TruLens com *golden set*; medir **faithfulness**, **answer quality**, **context precision/recall**, **nDCG@10**; comparar: BM25 vs vetorial vs híbrido vs híbrido+rerank. ([Elastic][10])
* **Online**: AB de *prompt* e pesos do fusion; logging de feedback; **APM** para latência/erros. ([Elastic][13])

---

# Segurança & PII (resumo prático)

* **RBAC/Users/Roles**: criar papéis mínimos (reader, ingester, app). ([Elastic][16])
* **API Keys** rotacionáveis por serviço. ([Elastic][17])
* **Mascarar PII** na ingestão com `gsub`/`script` e auditoria em logs. ([Elastic][18])

---

# Observabilidade (mínimos)

* **APM**: traces das rotas (`/ask`), *spans* para retrieve/rerank/LLM, **metrics** (latência, throughput, erro). ([Elastic][13])
* **Logs**: prompt/response **com máscara**; cardinalidade de erros; alerts. ([Elastic][19])

---

# Tabela — Riscos & Mitigações

| Risco                 | Impacto              | Sinal                            | Mitigação                                                                            |
| --------------------- | -------------------- | -------------------------------- | ------------------------------------------------------------------------------------ |
| *Recall* baixo no kNN | Respostas pobres     | nDCG/recall caem                 | ↑ `num_candidates`, filtros precisos, rerank top-N. ([Elastic][15])                  |
| Alucinação do LLM     | Informação incorreta | Baixa **faithfulness**           | Forçar citações, prompts com *grounding*, rejeitar sem fonte.                        |
| Vazamento de PII      | Multa/reputação      | Logs/índices com dados sensíveis | **gsub/script** em ingest, RBAC estrito, mascaramento nas respostas. ([Elastic][18]) |
| Custos/latência altos | SLO/SLA              | P95 ↑, throughput ↓              | Híbrido + `num_candidates` tuning; cache; ILM; quantização opcional. ([Elastic][6])  |
| Perda de dados        | Indisponibilidade    | Falha/erro humano                | **Snapshot/Restore** agendados e testados (SLM). ([Elastic][20])                     |

---

# Checklist — **Go-Live RAG com Elasticsearch**

* [ ] Índice com `dense_vector` correto (dims/similarity) e *mappings* versionados. ([Elastic][1])
* [ ] **kNN** calibrado (`k`, `num_candidates`) e filtros funcionais. ([Elastic][15])
* [ ] **RRF** entre BM25 e vetorial; **reranker** validado (se usado). ([Elastic][9])
* [ ] Pipelines de ingestão com **attachment** e **PII masking**; testes OK. ([Elastic][7])
* [ ] **RBAC** & **API Keys** revisados; princípio do menor privilégio. ([Elastic][16])
* [ ] **APM/Logs/Metrics** com dashboards e alertas mínimos. ([Elastic][13])
* [ ] **ILM** aplicado; **SLM** com último snapshot **testado** (restore). ([Elastic][21])

---

# Apêndice — Referências oficiais (Elastic 9.x)

* **Dense vectors, similaridade & quantização**. ([Elastic][1])
* **kNN search** (API + filtros) & **tuning** `k`/`num_candidates`. ([Elastic][2])
* **Híbrido & RRF** (docs e referência). ([Elastic][10])
* **Retrievers** (kNN, RRF, reranker). ([Elastic][22])
* **Ingest: attachment, gsub, script**. ([Elastic][7])
* **RBAC / API Keys**. ([Elastic][16])
* **Observability (APM/Logs/Metrics)**. ([Elastic][13])
* **ILM & Snapshot/Restore**. ([Elastic][21])
* **LangChain: ES retriever/vector store** (opcional). ([LangChain][12])

---

# JSON final (metadados do plano)

```json
{
  "timeline": {
    "weeks": 8,
    "hours_per_week": 8
  },
  "modules": [
    "Fundamentos & Setup & Segurança",
    "Embeddings & Chunking",
    "Vector DB: Mapping kNN & Filtros",
    "Ingestão de PDFs & PII",
    "Busca Híbrida (RRF) & Reranking",
    "App RAG (FastAPI) + Citações",
    "Avaliação (RAGAS/TruLens) & Tuning",
    "Observabilidade, ILM, Snapshots & Go-Live"
  ],
  "labs": [
    "Docker Compose + API Key",
    "Geração e normalização de embeddings",
    "Mapping + kNN com filtro",
    "Pipeline de ingest (attachment + PII masking)",
    "RRF + reranking semântico",
    "FastAPI RAG com citações",
    "Harness de avaliação e dashboards",
    "APM + ILM + Snapshot/Restore + Checklist"
  ],
  "deliverables": [
    "Índices e pipelines versionados",
    "Serviço RAG com citações",
    "Suite de avaliação e painéis",
    "Runbooks de segurança/backup"
  ],
  "metrics": {
    "latency_p95_ms": 1200,
    "ndcg_at_10": ">= baseline BM25",
    "faithfulness": ">= 0.7",
    "error_budget_monthly": "<= 1%"
  },
  "dashboards": [
    "APM (latência/erro)",
    "Uso de kNN (qps, num_candidates, k)",
    "Custo estimado por requisição"
  ],
  "risks": [
    "Recall baixo",
    "Alucinação",
    "Vazamento de PII",
    "Custos/latência elevados",
    "Perda de dados"
  ]
}
```


[1]: https://www.elastic.co/docs/reference/elasticsearch/mapping-reference/dense-vector "Dense vector field type | Reference"
[2]: https://www.elastic.co/docs/api/doc/elasticsearch/v8/operation/operation-knn-search?utm_source=chatgpt.com "Run a knn search | Elasticsearch API documentation (v8)"
[3]: https://www.elastic.co/docs/deploy-manage/deploy/self-managed/install-elasticsearch-with-docker?utm_source=chatgpt.com "Install Elasticsearch with Docker"
[4]: https://www.elastic.co/docs/api/doc/elasticsearch/operation/operation-security-create-api-key?utm_source=chatgpt.com "Create an API key | Elasticsearch API documentation"
[5]: https://www.elastic.org.cn/docs/8.1/www.elastic.co/guide/en/elasticsearch/reference/current/dense-vector.html?utm_source=chatgpt.com "Dense vector field type"
[6]: https://www.elastic.co/search-labs/blog/elasticsearch-knn-and-num-candidates-strategies?utm_source=chatgpt.com "How to choose the best k and num_candidates for kNN ..."
[7]: https://www.elastic.co/docs/reference/enrich-processor/attachment?utm_source=chatgpt.com "Attachment processor | Reference"
[8]: https://www.elastic.co/docs/reference/enrich-processor?utm_source=chatgpt.com "Ingest processor reference"
[9]: https://www.elastic.co/docs/reference/elasticsearch/rest-apis/reciprocal-rank-fusion?utm_source=chatgpt.com "Reciprocal rank fusion | Reference"
[10]: https://www.elastic.co/docs/solutions/search/ranking?utm_source=chatgpt.com "Ranking and reranking | Elastic Docs"
[11]: https://www.elastic.co/docs/reference/elasticsearch/rest-apis/retrievers/rrf-retriever?utm_source=chatgpt.com "RRF retriever | Reference"
[12]: https://python.langchain.com/docs/integrations/retrievers/elasticsearch_retriever/?utm_source=chatgpt.com "ElasticsearchRetriever"
[13]: https://www.elastic.co/docs/solutions/observability/apm/metrics?utm_source=chatgpt.com "Metrics in Elastic APM | Elastic Docs"
[14]: https://www.elastic.co/docs/solutions/observability/apm/get-started?utm_source=chatgpt.com "Get started with traces and APM"
[15]: https://www.elastic.co/docs/solutions/search/vector/knn?utm_source=chatgpt.com "kNN search in Elasticsearch"
[16]: https://www.elastic.co/docs/deploy-manage/users-roles/cluster-or-deployment-auth/user-roles?utm_source=chatgpt.com "User roles | Elastic Docs"
[17]: https://www.elastic.co/docs/deploy-manage/api-keys/elasticsearch-api-keys?utm_source=chatgpt.com "Elasticsearch API keys"
[18]: https://www.elastic.co/docs/reference/enrich-processor/gsub-processor?utm_source=chatgpt.com "Gsub processor | Reference"
[19]: https://www.elastic.co/docs/solutions/observability/logs?utm_source=chatgpt.com "Log monitoring | Elastic Docs"
[20]: https://www.elastic.co/docs/deploy-manage/tools/snapshot-and-restore?utm_source=chatgpt.com "Snapshot and restore | Elastic Docs"
[21]: https://www.elastic.co/docs/manage-data/lifecycle/index-lifecycle-management/index-lifecycle?utm_source=chatgpt.com "Index lifecycle | Elastic Docs"
[22]: https://www.elastic.co/docs/solutions/search/retrievers-overview?utm_source=chatgpt.com "Retrievers | Elastic Docs"
