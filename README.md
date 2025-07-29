# 📚 Roteiro de Estudos – Engenharia de Relevância no Elasticsearch (ESRE)

Este repositório contém um plano completo de estudos para se aprofundar em **Engenharia de Relevância com Elasticsearch (ESRE)**, com base na documentação oficial da Elastic e tópicos essenciais para desenvolvimento de aplicações de busca inteligente, GenAI e RAG.

---

## ✅ Fase 0 – Pré-Requisitos Técnicos

Antes de estudar ESRE, é necessário dominar os fundamentos do Elasticsearch e de busca.

### 🔹 Fundamentos do Elasticsearch
- Conceitos: índice, documento, mapping, shards, nodes, cluster.
- CRUD no Elasticsearch: `_index`, `_search`, `_update`, `_delete`.
- Query DSL: `match`, `term`, `bool`, `range`, `aggregations`.
- Ferramentas: Kibana Dev Tools.

**📚 Recomendado:**
- https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started.html
- https://www.elastic.co/guide/en/elasticsearch/guide/current/index.html

---

## 📘 Fase 1 – Fundamentos de Busca Relevante

### 🔹 Full-Text Search
- Tokenização e Análise
- Relevância com BM25
- Boosts, `function_score`, `rescore`
- Analyzers personalizados

📚 Estudo:
- https://www.elastic.co/guide/en/elasticsearch/reference/current/full-text-queries.html
- https://www.elastic.co/training/search-fundamentals

### 🔹 Recuperação de Dados
- `GET` vs `Search`
- Paginação com `from` / `size` e `search_after`
- `fields`, `_source`, runtime fields

📚 Estudo:
- https://www.elastic.co/guide/en/elasticsearch/reference/current/search-search.html

---

## 📙 Fase 2 – Introdução ao ESRE (Elasticsearch Relevance Engine)

### 🔹 ESRE Overview
- O que é o ESRE?
- Arquitetura e Componentes: Hybrid Search, Vector Search, NLP pipelines.
- Casos de uso: Busca semântica, RAG, GenAI.

📚 Leitura obrigatória:
- https://www.elastic.co/elasticsearch/elasticsearch-relevance-engine
- https://www.elastic.co/guide/en/esre/8.10/learn.html

### 🔹 Application Integration with Elasticsearch
- Conexão via API (REST)
- Uso de clientes oficiais: Python, Java, JS
- Segurança com API Key / Entra ID
- Logs e métricas via Elastic Observability

📚 Estudo:
- https://www.elastic.co/guide/en/elasticsearch/client/index.html

---

## 📗 Fase 3 – Busca Semântica e Vetorial

### 🔹 Semantic Search com ESRE
- Embeddings (texto para vetor)
- Indexação de vetores (dense_vector)
- Similaridade: cosine, dot_product, l2_norm
- KNN Search (`knn`, `knn_search`)

📚 Estudo:
- https://www.elastic.co/docs/solutions/search/ai-search/ai-search
- https://www.elastic.co/guide/en/esre/8.10/hybrid-search.html

### 🔹 Modelos Embedding
- Modelos suportados pela Elastic (Eland, HuggingFace, OpenAI, Cohere)
- Vetorização local vs hospedada
- Integração com ELSER

📚 Estudo:
- https://www.elastic.co/blog/elastic-vector-database-in-production

---

## 📕 Fase 4 – Aplicações GenAI e RAG

### 🔹 Desenvolvendo Aplicações com GenAI
- Integração com LLMs (HuggingFace, OpenAI, Azure OpenAI)
- Prompt Engineering com Elastic AI Assistant
- Chatbots com memória vetorial

📚 Estudo:
- https://www.elastic.co/docs/solutions/search/ai-search/ai-search

### 🔹 RAG com Elasticsearch
- Pipeline RAG: indexação -> vetorização -> retrieval -> prompt
- RetrievalQA com LangChain + Elastic
- Contexto com `highlight`, `source_documents`

📚 Estudo:
- https://www.elastic.co/docs/solutions/search/rag

---

## 🧠 Fase 5 – Elastic AI Assistant e Casos Avançados

### 🔹 Elastic AI Assistant
- Uso no Kibana
- Geração de queries, diagnóstico, alertas
- Limitações e customizações

📚 Estudo:
- https://www.elastic.co/blog/elastic-ai-assistant-for-observability-general-availability

---

## 🛠️ Fase 6 – Projetos Práticos e Labs

### 🔹 Ideias de Projeto
1. **Semantic Search de Documentos PDF** (upload, vetorização com ELSER)
2. **Chat RAG com documentos técnicos** usando LangChain + Elasticsearch
3. **Sistema de FAQ inteligente** com embeddings e retriever híbrido

---

## 📅 Cronograma Sugerido (6 semanas)

| Semana | Tópico Principal                                    |
|--------|------------------------------------------------------|
| 1      | Fundamentos do Elasticsearch                         |
| 2      | Full-text search + Recuperação de dados              |
| 3      | ESRE Overview + Integração com apps                  |
| 4      | Busca Semântica e Vetorial                           |
| 5      | GenAI + RAG                                          |
| 6      | Elastic AI Assistant + Projetos práticos             |

---

## 🔗 Referências Oficiais

- [Elasticsearch Relevance Engine (ESRE)](https://www.elastic.co/elasticsearch/elasticsearch-relevance-engine)
- [ESRE Learn Guide](https://www.elastic.co/guide/en/esre/8.10/learn.html)
- [AI Search](https://www.elastic.co/docs/solutions/search/ai-search/ai-search)
- [RAG Guide](https://www.elastic.co/docs/solutions/search/rag)
