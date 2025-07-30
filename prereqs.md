# 📚 Roteiro de Estudos com Checklist: Pré-requisitos para o Curso ESRE Engineer

## 📘 1. Fundamentos de Elasticsearch
**🎯 Objetivo:** Entender como consultar, indexar e agregar dados com eficiência.

### ✅ Tópicos:
- [ ] Estrutura de documentos e índices
- [ ] Tipos de dados: text, keyword, numeric, date etc.
- [ ] Query DSL: match, term, range, bool, filter vs query context
- [ ] Aggregations: terms, histogram, significant_terms
- [ ] Search templates e performance

### 🔗 Recursos:
- Curso: [Elastic Certified Engineer I](https://www.elastic.co/training/elastic-certified-engineer)
- Docs: [Elasticsearch Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)

### 💡 Desafio:
Crie uma indexação e escreva consultas usando `match`, `bool`, `range` e `aggregations` com dados de blogs.

---

## 🧠 2. Introdução ao GenAI e LLMs
**🎯 Objetivo:** Compreender o papel dos modelos de linguagem na geração aumentada por recuperação (RAG).

### ✅ Tópicos:
- [ ] Conceito de GenAI e LLMs (GPT, BERT etc.)
- [ ] Diferença entre modelos pré-treinados e fine-tuned
- [ ] Limitações: alucinação, contexto
- [ ] RAG como solução

### 🔗 Recursos:
- Curso: [Prompt Engineering - OpenAI & DeepLearning.ai](https://learn.deeplearning.ai/chatgpt-prompt-eng)
- Artigos: [Elastic Blog sobre RAG](https://www.elastic.co/blog/building-a-retrieval-augmented-generation-application)

### 💡 Desafio:
Explique com suas palavras como funciona um pipeline RAG e quais limitações ele resolve nos LLMs.

---

## 🧰 3. Vetores e NLP Aplicada
**🎯 Objetivo:** Aprender como gerar embeddings, armazenar vetores e realizar buscas semânticas.

### ✅ Tópicos:
- [ ] Dense vs Sparse Embeddings
- [ ] Elastic ELSER
- [ ] Vector Search e Hybrid Search
- [ ] Relevância, boosting e normalização

### 🔗 Recursos:
- Docs: [ELSER Overview](https://www.elastic.co/guide/en/machine-learning/current/ml-nlp-elser.html)

### 💡 Desafio:
Ingerir um texto, aplicar o ELSER, e fazer uma busca semântica com base em pergunta natural.

---

## 🐍 4. Python + Elasticsearch + LangChain
**🎯 Objetivo:** Usar Python para consultar dados e criar pipelines com LangChain.

### ✅ Tópicos:
- [ ] Elasticsearch Python Client (`elasticsearch-py`)
- [ ] CRUD com Python
- [ ] LangChain: chains, retrievers, prompts, tools
- [ ] LCEL (LangChain Expression Language)

### 🔗 Recursos:
- [LangChain Docs (Python)](https://python.langchain.com/)
- [Elasticsearch Python Client](https://elasticsearch-py.readthedocs.io/en/latest/)

### 💡 Desafio:
Crie um mini chatbot que consulte documentos indexados no Elasticsearch usando LangChain.

---

## 🌐 5. UIs com Streamlit
**🎯 Objetivo:** Criar uma interface web simples para interagir com seu app GenAI.

### ✅ Tópicos:
- [ ] Input de usuário (textbox, botão)
- [ ] Integração com LangChain
- [ ] Exibição de resposta e contexto
- [ ] Prototipagem rápida

### 🔗 Recursos:
- [Streamlit Docs](https://docs.streamlit.io/)

### 💡 Desafio:
Construa uma interface em Streamlit que envia uma pergunta para seu app RAG e exibe a resposta.

---

## 🔐 6. Segurança e Governança de Dados
**🎯 Objetivo:** Controlar acesso a dados sensíveis usados nos prompts.

### ✅ Tópicos:
- [ ] RBAC: controle por usuário e role
- [ ] Field-level e Document-level security
- [ ] Configurações no Kibana

### 🔗 Recursos:
- [Elastic Security Docs](https://www.elastic.co/guide/en/elasticsearch/reference/current/security-settings.html)

### 💡 Desafio:
Crie dois usuários com permissões distintas e valide que um deles só vê parte dos documentos.

---

## ⚙️ 7. Machine Learning no Elastic
**🎯 Objetivo:** Enriquecer o contexto com detecção de anomalias e classificações.

### ✅ Tópicos:
- [ ] Detecção de anomalias
- [ ] Classificação e outliers
- [ ] Ingest Pipelines com `inference`
- [ ] Modelos ELSER e terceiros

### 🔗 Recursos:
- [Elastic ML Docs](https://www.elastic.co/guide/en/machine-learning/index.html)

### 💡 Desafio:
Crie um modelo de detecção de anomalias para logs e incorpore os resultados no contexto RAG.
