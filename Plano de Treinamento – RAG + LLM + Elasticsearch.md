# 📚 Plano de Treinamento – RAG + LLM + Elasticsearch (E-commerce Analytics)

## 🎯 Objetivo
Capacitar alguém com pouco conhecimento em IA, NLP e Elasticsearch a criar uma aplicação que utilize **RAG + LLM + Busca Semântica** sobre um dataset de e-commerce, respondendo perguntas de negócio como:
- Qual o mês de maior faturamento?
- Qual produto vende mais?
- Em qual região precisamos melhorar as vendas?

---

## 📌 Módulo 1 – Fundamentos

### 1.1 Introdução à IA e NLP
- O que são LLMs (Large Language Models)  
- Conceitos básicos de NLP (tokenização, embeddings, vetorização)  
- Paradigmas: **full-text search vs. semantic search**  

**Referências:**
- [Stanford CS324 – Large Language Models](https://stanford-cs324.github.io/winter2024/)  
- [Hugging Face – NLP Course](https://huggingface.co/course/chapter1)  

### 1.2 Fundamentos de Elasticsearch
- O que é Elasticsearch e como funciona  
- Indexação de documentos  
- Consulta com **BM25 (full-text)**  
- Introdução ao **dense_vector** e busca semântica  

**Referências:**
- [Elastic Docs – Dense Vector](https://www.elastic.co/guide/en/elasticsearch/reference/current/dense-vector.html)  
- [Elasticsearch: The Definitive Guide (O’Reilly, free)](https://www.elastic.co/guide/en/elasticsearch/guide/current/index.html)  

---

## 📌 Módulo 2 – Embeddings e Vector Databases

### 2.1 O que são embeddings?
- Embeddings de texto (semântica)  
- Espaço vetorial e medidas de similaridade (cosine, dot product, L2)  

**Referência:**
- [OpenAI Docs – Embeddings](https://platform.openai.com/docs/guides/embeddings)  

### 2.2 VectorDBs e Elasticsearch
- Elasticsearch como vetor database  
- Indexação híbrida: BM25 + embeddings  
- Estratégias de normalização e chunking de dados  

**Referência:**
- [Semantic Search com Elasticsearch](https://www.elastic.co/blog/text-similarity-search-with-vectors-in-elasticsearch)  

---

## 📌 Módulo 3 – Large Language Models e RAG

### 3.1 LLMs na prática
- Diferença entre fine-tuning e prompting  
- Limitações dos LLMs (alucinação, contexto limitado)  
- Prompt engineering básico  

**Referência:**
- [Prompt Engineering Guide](https://www.promptingguide.ai/)  

### 3.2 Retrieval-Augmented Generation (RAG)
- Conceito: “LLM + Base de conhecimento”  
- Arquitetura típica:
  1. Query → embeddings  
  2. Recuperação no Elasticsearch (dense_vector + BM25)  
  3. Contexto inserido no prompt do LLM  
  4. Resposta contextualizada  
- Avaliação: precisão, recall, relevância  

**Referências:**
- [RAG original paper – Facebook](https://arxiv.org/abs/2005.11401)  
- [Haystack RAG framework](https://haystack.deepset.ai/)  

---

## 📌 Módulo 4 – Hands-on com Dataset de E-commerce

### 4.1 Preparação dos dados
- Estrutura: produtos, pedidos, clientes, regiões  
- Pipeline de ingestão no Elasticsearch  
- Criação de índices com **mapeamento híbrido** (text + dense_vector)  

### 4.2 Construindo embeddings
- Geração de embeddings (OpenAI, Hugging Face ou SBERT)  
- Indexação dos vetores no Elasticsearch  

### 4.3 Integração com LLM (RAG pipeline)
- Fluxo: **pergunta → embeddings → busca semântica no Elastic → contexto → resposta do LLM**  
- Exemplos de perguntas:
  - “Qual o mês de maior faturamento?”  
  - “Qual produto vende mais?”  
  - “Em qual região precisamos melhorar as vendas?”  

**Referências práticas:**
- [Elastic + OpenAI blogpost](https://www.elastic.co/blog/semantic-search-openai-elasticsearch)  
- [LangChain – RAG com Elastic](https://python.langchain.com/docs/integrations/vectorstores/elasticsearch)  

---

## 📌 Módulo 5 – Projeto Final

### Objetivo
Criar uma aplicação RAG de perguntas e respostas sobre vendas em e-commerce.

### Entregáveis
- Ingestão do dataset no Elasticsearch  
- Indexação híbrida (BM25 + embeddings)  
- API Python/Flask ou FastAPI para consultas  
- Integração com LLM (OpenAI GPT ou outro modelo)  
- Dashboard simples para perguntas e respostas (opcional: Streamlit ou Gradio)  

---

## 📌 Módulo 6 – Tópicos Avançados
- **Avaliação de RAG** (métricas MRR, NDCG, precisão top-k)  
- **Optimização de custo e latência** em produção  
- **Segurança e controle de contexto** (para evitar vazamento de dados)  
- **Escalabilidade**: sharding, replicação e caching  

---

## 🗓️ Sugestão de Cronograma
- **Semana 1:** Fundamentos de IA + Elasticsearch  
- **Semana 2:** Embeddings + Vector Search  
- **Semana 3:** LLMs + RAG  
- **Semana 4:** Projeto prático com dataset de e-commerce  
- **Semana 5:** Apresentação do projeto final  
