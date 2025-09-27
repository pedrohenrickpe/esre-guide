# Embeddings & Transformers com LangChain — Guia Completo (PT‑BR)

> **TL;DR:** Este guia prático explica embeddings e transformers, mostra como montar pipelines de *transforms* no LangChain (splitters, filtros, reordenação, compressão e reranking), constrói um RAG simples, implementa busca híbrida (BM25 + vetorial), mede qualidade (precision@k, recall@k, MRR, nDCG), adiciona *guardrails* contra alucinações e traz um projeto final com PDFs. Inclui exemplos comentados e checklists.

---

## Sumário

1. [Visão geral](#0-visão-geral)
2. [Fundamentos de embeddings](#1-fundamentos-de-embeddings)
3. [Transformers (arquitetura)](#2-transformers-arquitetura)
4. [Transforms no LangChain](#3-transforms-no-contexto-de-langchain)
5. [Embeddings na prática (LangChain)](#4-embeddings-na-prática-langchain)
6. [Recuperação e busca híbrida](#5-recuperação-e-busca-híbrida)
7. [Avaliação e observabilidade](#6-avaliação-e-observabilidade)
8. [Segurança e privacidade](#7-segurança-e-privacidade)
9. [Projeto final (capstone)](#8-projeto-final-capstone)
10. [Exemplos de código](#9-exemplos-de-código)
11. [Boas práticas, erros comuns e checklists](#10-boas-práticas-erros-comuns-e-checklists)
12. [Glossário](#glossário)
13. [Referências](#referências)

---

## 0) Visão geral

```
[Documentos] → [Transforms] → [Embeddings] → [Index/VectorStore] → [Retriever] → [LLM/Chain] → [Resposta + Citações]
                                   ↑                                   ↘
                            [Avaliação/Eval]                        [Observabilidade]
```

* **Objetivo**: montar um pipeline replicável e mensurável para busca semântica e RAG.
* **Onde cada tópico entra**:

  * *Embeddings*: representações vetoriais para medir similaridade.
  * *Transformers*: arquitetura base de encoders/decoders para gerar embeddings ou respostas.
  * *Transforms no LangChain*: preparação do texto (split, limpeza, filtros, reordenação, compressão, reranking).
  * *VectorStore*: FAISS/Chroma/Elastic/PGVector para ANN (HNSW/IVF/Flat).
  * *Híbrida*: combinar BM25 (léxica) com vetorial (semântica).
  * *Avaliação*: medir *precision@k, recall@k, MRR, nDCG*.
  * *Segurança*: mascarar PII antes de indexar; RBAC por metadados.

---

## 1) Fundamentos de embeddings

### Intuição

* Transformam textos (ou imagens, áudio) em vetores de dimensão fixa.
* Vetores próximos → conteúdos semanticamente similares.

### Usos

* Busca semântica, *clustering*, recomendação, deduplicação, RAG, *reranking*, detecção de redundância.

### Métricas de similaridade

* **Cosine** (mais comum); **dot product**; **L2/Euclidiana**. Escolha compatível com o índice.

### Normalização e dimensionalidade

* Normalizar vetores pode estabilizar *cosine* e ANN.
* Dimensão: 256–3072 típicas; maior ≠ sempre melhor (custo/latência ↑).

### Exercícios rápidos

1. Calcule similaridade do cosseno entre duas sentenças e explique o resultado.
2. Aplique normalização L2 em vetores e compare *top‑k* antes/depois.

---

## 2) Transformers (arquitetura)

* **Atenção/Autoatenção**: cada token “olha” para outros tokens e pondera contribuições.
* **Máscaras**: evitam que o modelo “veja o futuro” (decoders) ou controlam contexto.
* **BERT (encoder)** vs **GPT (decoder)**:

  * *Encoders* → ótimos para embeddings/classificação.
  * *Decoders* → geração de texto (RAG, chat, sumarização).
* **Tokenização**: BPE/WordPiece convertem texto em subpalavras.
* **Positional encodings**: injetam noção de ordem.

**Quadro rápido — quando usar**

* Precisa de embeddings eficientes → *encoder* (ex.: E5/BGE).
* Precisa gerar respostas → *decoder* (ex.: GPT‑like) + retriever.

---

## 3) Transforms no contexto de LangChain

Principais *transforms* (pré e pós‑retrieval):

* **Splitters**: `RecursiveCharacterTextSplitter` (equilíbrio entre tamanho e coesão).
* **Limpeza/normalização**: *lowercase*, remoção de boilerplate, deduplicação.
* **Filtros**: remover redundantes por proximidade de embeddings.
* **Reordenação para contexto longo**: priorizar passagens centrais.
* **Compressão**: reduzir trechos mantendo relevância.
* **Reranking**: reordenar resultados com um reclassificador (ex.: *cross‑encoder* ou LLM).

**Trade‑offs (exemplo)**

| Parâmetro            | Efeito                   | Riscos                       |
| -------------------- | ------------------------ | ---------------------------- |
| `chunk_size` ↑       | Menos cortes de conceito | *Context overflow* e custo ↑ |
| `chunk_overlap` ↑    | Coesão entre *chunks*    | Redundância e índice maior   |
| Compressão agressiva | Contexto mais enxuto     | Perda de evidências          |

---

## 4) Embeddings na prática (LangChain)

### Providers comuns

* **OpenAI** (`text-embedding-3-small/large`) — 1536/3072 dims (verifique no provedor).
* **HuggingFace** (E5, BGE, GTE, m3) — local ou em *inference endpoints*.
* **Critérios**: idioma, custo, latência, privacidade, licença.

### Armazenamento (VectorStores)

* **FAISS** (local, rápido, simples), **Chroma** (persistência local), **Elastic** (ANN/HNSW + filtros), **PGVector** (Postgres + vetor).
* **Indexação**: HNSW/IVF/Flat conforme suporte.

### Boas práticas de versionamento

* Versione *datasets*, *splitters* e *embedders* (ex.: `v1-e5-large-1024-chunk800o150`).
* Prefira *delta indexing* para atualizações incrementais.

---

## 5) Recuperação e busca híbrida

* **BM25 (léxica)**: forte em termos raros e palavras exatas.
* **Vetorial (ANN/HNSW)**: forte em sinonímia e semântica.
* **Híbrida**: combine listas (RRF) ou *scores* (ponderação) para o melhor dos dois mundos.

**Checklist de tuning**

* Ajuste `k`, `score_threshold` e filtros por metadados.
* Compare *similarity* vs *mmr* (diversidade).
* Teste RRF vs média ponderada de *scores*.

---

## 6) Avaliação e observabilidade

* Construa um **golden set** de consultas com passagens relevantes anotadas.
* Métricas: **precision@k**, **recall@k**, **MRR**, **nDCG**.
* Observe **latência**, **custo** e ***hit rate*** por tipo de consulta.
* Registre amostras ruins para iteração (ex.: *error book* do RAG).

### Exercício

* Crie 10–20 consultas, anote passagens, e rode as métricas antes/depois de ajustes de `chunk_size`, modelo de embedding e método híbrido.

---

## 7) Segurança e privacidade

* **Mascarar PII antes de indexar** (CPF, telefone, e‑mail, cartões etc.).
* **RBAC por metadados** (ex.: `org`, `team`, `classification`).
* **Retenção de logs** e **coleta mínima** (princípio do menor privilégio).

---

## 8) Projeto final (capstone)

> **Objetivo**: construir um mini‑RAG sobre PDFs

**Fluxo**: Ingestão → Transforms → Embeddings → Index (FAISS/Chroma/Elastic/PGVector) → Retriever (híbrido) → Chain com citações → Avaliação.

**Entregáveis**

* Código + README.
* Métricas (precision@k, recall@k, MRR, nDCG) e decisões.
* *Checklist* de *guardrails* e PII.

---

## 9) Exemplos de código

> **Requisitos**: Python 3.10+, LangChain ≥ 0.2. Use variáveis de ambiente (`OPENAI_API_KEY`) quando necessário. Se um import falhar, tente a variação com `langchain_community`/`langchain_*` conforme sua versão.

### 9.1) Split, embed e index (FAISS)

```python
# pip install langchain langchain-community langchain-core langchain-text-splitters faiss-cpu
# pip install langchain-openai  # se usar OpenAI
# pip install langchain-huggingface sentence-transformers  # se usar HF local

from langchain_core.documents import Document
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain_community.vectorstores import FAISS

# Escolha UM provedor de embeddings
USE_OPENAI = False

if USE_OPENAI:
    from langchain_openai import OpenAIEmbeddings
    embeddings = OpenAIEmbeddings(model="text-embedding-3-small")
else:
    from langchain_huggingface import HuggingFaceEmbeddings
    # Ex.: e5-base / bge-small / gte-base — confirme dims/custos no provedor
    embeddings = HuggingFaceEmbeddings(model_name="intfloat/e5-base-v2", normalize_embeddings=True)

# 1) Carregar texto (exemplo simples; para PDF, use PyPDFLoader)
raw_docs = [
    Document(page_content="Observabilidade com logs, métricas e traces."),
    Document(page_content="Embeddings representam texto como vetores."),
    Document(page_content="LangChain ajuda a construir pipelines de RAG."),
]

# 2) Transforms: split
splitter = RecursiveCharacterTextSplitter(chunk_size=800, chunk_overlap=150)
docs = splitter.split_documents(raw_docs)

# 3) Index (FAISS)
vectorstore = FAISS.from_documents(docs, embeddings)
vectorstore.save_local("./faiss_store")
```

### 9.2) Retriever + RAG simples (LCEL)

```python
# pip install langchain langchain-community langchain-core langchain-openai

from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough, RunnableLambda
from langchain_community.vectorstores import FAISS
from operator import itemgetter

# Carregar VectorStore e criar retriever
vectorstore = FAISS.load_local("./faiss_store", embeddings, allow_dangerous_deserialization=True)
retriever = vectorstore.as_retriever(search_type="similarity", search_kwargs={"k":4})

# Utilidade para formatar documentos

def format_docs(docs):
    return "\n\n".join(f"- {d.page_content}" for d in docs)

# LLM (ex.: OpenAI) — substitua pelo provedor de sua preferência
from langc
```
