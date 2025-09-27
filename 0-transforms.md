# Transforms no LangChain — Guia Completo (PT‑BR)

> **Objetivo**: Estudar profundamente *transforms* no LangChain (≥ 0.2), cobrindo preparação de documentos, splitters, limpeza/normalização, enriquecimento por metadados, filtros por embeddings, deduplicação, compressão, reordenação para contexto longo, *reranking*, *query transforms* (multi‑query, HyDE, expansão léxica) e montagem de *retrievers* comprimidos. Inclui exemplos práticos em **LCEL** e **código Python executável**.

---

## Sumário

0. [Visão Geral dos Transforms](#0-visão-geral-dos-transforms)
1. [Splitters na prática](#1-splitters-na-prática)
2. [Limpeza, Normalização e Enriquecimento](#2-limpeza-normalização-e-enriquecimento)
3. [Filtros por Embeddings e Deduplicação](#3-filtros-por-embeddings-e-deduplicação)
4. [Reordenação e Contexto Longo](#4-reordenação-e-contexto-longo)
5. [Compressão de Documentos](#5-compressão-de-documentos)
6. [Reranking](#6-reranking)
7. [Transforms de Consulta (Query Transforms)](#7-transforms-de-consulta-query-transforms)
8. [Observabilidade e Avaliação](#8-observabilidade-e-avaliação)
9. [Segurança e Privacidade](#9-segurança-e-privacidade)
10. [Projeto Final (Capstone)](#10-projeto-final-capstone)
11. [Exemplos de Código](#11-exemplos-de-código)
12. [Boas práticas, Erros comuns e Checklists](#12-boas-práticas-erros-comuns-e-checklists)
13. [Glossário](#glossário)
14. [Referências](#referências)

---

## 0) Visão Geral dos Transforms

**Pipeline típico de RAG/busca semântica:**

```
[Ingestão] → [Transforms] → [Embeddings] → [Index/VectorStore] → [Retrieval] → [LLM/Chain]
                               ↑                             ↘
                         [Avaliação]                       [Observabilidade]
```

**Pontos de decisão:** *token budget*, latência (p50/p95), precisão/recall, custo, privacidade.

**Objetivo dos transforms:** melhorar sinal (relevância) e reduzir ruído (redundância), mantendo um contexto enxuto para o LLM.

**Onde usar:**

* **Pré‑indexação**: split, limpeza, metadados, PII masking, deduplicação.
* **Pré‑contexto**: filtros por similaridade, compressão, reordenação, *reranking*.
* **Consulta**: multi‑query, HyDE, expansão léxica, normalização, filtros por metadados.

**Exercício (rápido):** desenhe seu pipeline atual e marque onde cada transform se encaixa; defina uma hipótese de ganho (ex.: +10% nDCG@10 com compressão + reorder).

---

## 1) Splitters na prática

Splitters definem o *granularity* da unidade de busca.

* **Principais**:

  * `RecursiveCharacterTextSplitter` (equilíbrio coesão/tamanho)
  * `TokenTextSplitter` (respeita orçamentos de tokens)
  * `MarkdownHeaderTextSplitter` (segmentação por seções/títulos)
  * *Language‑aware* (código‑fonte, etc.)

**Trade‑offs**

| Parâmetro         | Efeito                                   | Riscos                     |
| ----------------- | ---------------------------------------- | -------------------------- |
| `chunk_size` ↑    | Menos cortes de contexto                 | Estouro de janela; custo ↑ |
| `chunk_overlap` ↑ | Maior coesão entre *chunks*              | Redundância; índice maior  |
| *Parent/child*    | Coesão no *parent* + precisão no *child* | Manutenção de 2 níveis     |

**Exercício:** varie `chunk_size` (400/800/1200) e `overlap` (50/150/250), meça recall@k e tempo de index.

---

## 2) Limpeza, Normalização e Enriquecimento

* **Limpeza**: remoção de boilerplate, espaços repetidos, artefatos de OCR.
* **Normalização**: *lowercase* seletivo, *unicode normalize*, padronização de números/datas.
* **Enriquecimento**: metadados `{fonte, seção, data, confidencialidade, org/team, rótulos RBAC}`.

**Exercício:** construa um pipeline que limpa e enriquece com `{source, page, section, sensitivity}`, e confirme que o retriever consegue filtrar por metadados.

---

## 3) Filtros por Embeddings e Deduplicação

* **`EmbeddingsFilter`** (limiar/threshold): remove candidatos com similaridade abaixo de X.
* **`EmbeddingsRedundantFilter`**: elimina passagens quase idênticas (redundância).
* **Normalização** dos embeddings pode estabilizar *cosine*.

**Exercício:** aplique `similarity_threshold` em 0.2/0.3/0.4 e compare P@k/R@k e tamanho médio do contexto.

---

## 4) Reordenação e Contexto Longo

* **`LongContextReorder`**: reordena documentos para maximizar coesão (intro → corpo → conclusões).
* **Janelas deslizantes** e **parent‑child retrieval** ajudam quando há documentos extensos.

**Exercício:** rode perguntas longas com e sem reordenação e compare nDCG@10.

---

## 5) Compressão de Documentos

* **`DocumentCompressorPipeline`**: encadeia filtros (similaridade, redundância) + resumo focado.
* **`ContextualCompressionRetriever`**: aplica compressor sobre um *base_retriever* antes de enviar ao LLM.

**Exercício:** compare latência e *token input* do LLM com compressor desligado vs ligado.

---

## 6) Reranking

* **Cross‑encoder** (ex.: MS‑MARCO): caro, porém preciso no *top‑N*.
* **LLM‑as‑reranker**: usa o próprio LLM para reordenar.
* **Cohere Rerank** (quando disponível): API dedicada.

**Padrão:** candidato barato (BM25/vetorial) → rerank apenas os N melhores (ex.: 50 → rerank top‑20 → LLM vê top‑6).

**Exercício:** ablação com reranker ON/OFF; reporte MRR e custo.

---

## 7) Transforms de Consulta (Query Transforms)

* **MultiQueryRetriever**: gera variações semânticas da consulta.
* **HyDE** (*Hypothetical Document Embeddings*): sintetiza um pseudo‑documento e busca por ele.
* **Expansão léxica**: adiciona sinônimos/termos correlatos (estilo RM3).
* **Normalização de consulta**: *lowercase*, remoção de *stopwords*, filtros por metadados.

**Exercício:** crie um *golden set* de consultas difíceis; compare baseline vs multi‑query vs HyDE.

---

## 8) Observabilidade e Avaliação

* **Métricas**: precision@k, recall@k, MRR, nDCG.
* **Logs**: latência por etapa (split → filtro → compress → rerank), *token usage*, custo.
* **Ablação**: tabela “antes vs depois” para cada transform.

**Exercício:** crie 10–20 consultas anotadas; gere tabela de ablação por transform.

---

## 9) Segurança e Privacidade

* **PII masking** (CPF/e‑mail/telefone/cartão) **antes de indexar**.
* **RBAC** via metadados; **retenção** de logs; princípio do menor privilégio.

**Exercício:** implemente um transform de PII e valide no índice/retriever.

---

## 10) Projeto Final (Capstone)

> **Construir um retriever comprimido sobre PDFs**

**Fluxo:** ingestão → split → limpeza → PII → embeddings → index (FAISS/Chroma/Elastic/PGVector) → retriever (similarity/MMR) → compressor → reranker → chain com citações.

**Entregáveis:** código, README, métricas, relatório de ablação e decisões.

---

## 11) Exemplos de Código

> **Requisitos**: Python 3.10+, LangChain ≥ 0.2. Se a API tiver mudado na sua versão, ajuste imports (ex.: `langchain_community` ou `langchain_*`). Use variáveis de ambiente para chaves (ex.: `OPENAI_API_KEY`).

### 11.1) Split básico + index local (FAISS)

```python
# pip install langchain langchain-core langchain-community langchain-text-splitters faiss-cpu
# Provedores de embeddings:
#   pip install langchain-openai  # para OpenAI
#   pip install langchain-huggingface sentence-transformers  # para HF local

from langchain_core.documents import Document
from langchain_text_splitters import (
    RecursiveCharacterTextSplitter,
    MarkdownHeaderTextSplitter,
)
from langchain_community.vectorstores import FAISS

# Escolha UM provedor de embeddings
USE_OPENAI = False

if USE_OPENAI:
    from langchain_openai import OpenAIEmbeddings
    embed = OpenAIEmbeddings(model="text-embedding-3-small")
else:
    from langchain_huggingface import HuggingFaceEmbeddings
    embed = HuggingFaceEmbeddings(model_name="intfloat/e5-base-v2", normalize_embeddings=True)

# Carregar documento (ex.: Markdown com cabeçalhos)
md_text = """
# Guia RAG
## Introdução
RAG combina recuperação e geração.
## Embeddings
Representações vetoriais de texto.
"""

# Split por cabeçalho + split recursivo
hdr_splitter = MarkdownHeaderTextSplitter(headers_to_split_on=[("#", "h1"), ("##", "h2")])
parts = hdr_splitter.split_text(md_text)

rec_splitter = RecursiveCharacterTextSplitter(chunk_size=800, chunk_overlap=150)
docs = rec_splitter.split_documents([Document(page_content=p.page_content, metadata=p.metadata) for p in parts])

# Index FAISS
vs = FAISS.from_documents(docs, embed)
vs.save_local("./faiss_transforms")
```

### 11.2) ContextualCompressionRetriever (filtros + reorder)

```python
# pip install langchain

from langchain_community.vectorstores import FAISS
from langchain_core.runnables import RunnablePassthrough, RunnableLambda
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

# Carregar embeddings e índice da seção anterior
# (reutilize `embed` do bloco anterior)
vs = FAISS.load_local("./faiss_transforms", embed, allow_dangerous_deserialization=True)
base_retriever = vs.as_retriever(search_type="similarity", search_kwargs={"k":8})

# Document compressors
try:
    from langchain.retrievers.document_compressors import (
        EmbeddingsFilter,
        EmbeddingsRedundantFilter,
        LongContextReorder,
        DocumentCompressorPipeline,
    )
except Exception:
    # Fallbacks de versão
    from langchain_community.document_transformers import LongContextReorder
    from langchain.retrievers.document_compressors import (
        EmbeddingsFilter,
        EmbeddingsRedundantFilter,
        DocumentCompressorPipeline,
    )

sim_filter = EmbeddingsFilter(embeddings=embed, similarity_threshold=0.3)
redund = EmbeddingsRedundantFilter(embeddings=embed)
reorder = LongContextReorder()
compressor = DocumentCompressorPipeline(transformers=[sim_filter, redund, reorder])

from langchain.retrievers import ContextualCompressionRetriever
cc_retriever = ContextualCompressionRetriever(base_retriever=base_retriever, base_compressor=compressor)

# LCEL: retriever comprimido → prompt → LLM
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)

prompt = ChatPromptTemplate.from_messages([
    ("system", "Responda apenas com base no contexto; cite trechos relevantes."),
    ("user", "Pergunta: {q}\n\nContexto:\n{ctx}"),
])

format_docs = lambda docs: "\n\n".join(f"- {d.page_content}" for d in docs)

chain = ({
    "ctx": cc_retriever | RunnableLambda(format_docs),
    "q": RunnablePassthrough(),
} | prompt | llm | StrOutputParser())

print(chain.invoke("O que é RAG?"))
```

### 11.3) Reranking (cross‑encoder/LLM/Cohere)

```python
# Exemplo conceitual com cross-encoder via sentence-transformers
# pip install sentence-transformers

from sentence_transformers import CrossEncoder
from typing import List

# Supondo que você já tenha candidatos (docs)
# recovered = base_retriever.get_relevant_documents("pergunta")

# Reranker: pontua (consulta, passage)
rerank_model = CrossEncoder("cross-encoder/ms-marco-MiniLM-L-6-v2")

def rerank(query: str, passages: List[str], top_k: int = 6):
    pairs = [[query, p] for p in passages]
    scores = rerank_model.predict(pairs)
    ranked = sorted(zip(passages, scores), key=lambda x: x[1], reverse=True)
    return [p for p, _ in ranked[:top_k]]
```

> **Alternativas:** `Cohere Rerank` (via `langchain-cohere`) ou *LLM‑as‑reranker* (usar um prompt que peça ao LLM para ordenar os trechos por relevância).

### 11.4) Parent‑Child Retrieval

```python
# pip install langchain langchain-community

from langchain.retrievers import ParentDocumentRetriever
from langchain_community.vectorstores import FAISS
from langchain.storage import InMemoryStore
from langchain_text_splitters import RecursiveCharacterTextSplitter

# VectorStore para children
child_splitter = RecursiveCharacterTextSplitter(chunk_size=400, chunk_overlap=100)
parent_splitter = RecursiveCharacterTextSplitter(chunk_size=1200, chunk_overlap=200)

vectorstore = FAISS.from_documents([], embed)
store = InMemoryStore()  # docstore para parents

retriever = ParentDocumentRetriever(
    vectorstore=vectorstore,
    docstore=store,
    child_splitter=child_splitter,
    parent_splitter=parent_splitter,
)

# Adicionar documentos (parents); o retriever criará children para indexação vetorial
from langchain_core.documents import Document
parents = [Document(page_content="Texto longo…", metadata={"source": "x"})]
retriever.add_documents(parents)

# Buscar
results = retriever.get_relevant_documents("sua consulta")
```

### 11.5) Query Transforms — MultiQuery e HyDE

```python
# MultiQueryRetriever
try:
    from langchain.retrievers.multi_query import MultiQueryRetriever
except Exception:
    from langchain.retrievers import MultiQueryRetriever

mqr = MultiQueryRetriever.from_llm(retriever=base_retriever, llm=llm)
answers = mqr.get_relevant_documents("Como funciona RAG?")

# HyDE (documento hipotético)
from langchain.prompts import PromptTemplate
from langchain.chains import LLMChain

hyde_prompt = PromptTemplate.from_template(
    """Escreva um breve documento que responda à pergunta de forma ideal.\nPergunta: {q}\nDocumento:"""
)
hyde_chain = LLMChain(llm=llm, prompt=hyde_prompt)

q = "Explique embeddings em busca semântica"
pseudo_doc = hyde_chain.run(q)
# Em seguida, embedde `pseudo_doc` e pesquise no vetorstore
pseudo_query_vec = embed.embed_query(pseudo_doc)
# Dependendo do VectorStore, use a API de similaridade com vetor direto
```

### 11.6) PII Masking como Transform

```python
import re
from typing import List
from langchain_core.documents import Document

CPF_RE = re.compile(r"\b(\d{3}\.\d{3}\.\d{3}-\d{2}|\d{11})\b")
PHONE_RE = re.compile(r"\b(\+?\d{2,3}\s?)?(\(?\d{2}\)?\s?)?\d{4,5}-?\d{4}\b")
EMAIL_RE = re.compile(r"[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+")

class PIIMasker:
    def __call__(self, docs: List[Document]) -> List[Document]:
        out = []
        for d in docs:
            t = d.page_content
            t = CPF_RE.sub("[PII]", t)
            t = PHONE_RE.sub("[PII]", t)
            t = EMAIL_RE.sub("[PII]", t)
            out.append(Document(page_content=t, metadata=d.metadata))
        return out

# Uso antes de indexar
# docs = PIIMasker()(docs)
```

### 11.7) Avaliação e Ablação (métricas)

```python
import math
from typing import List, Dict, Set

# truth: {consulta -> set(ids relevantes)}
truth: Dict[str, Set[str]] = {
    "o que é RAG?": {"d1", "d3"},
}

def precision_at_k(retrieved: List[str], relevant: Set[str], k: int = 5) -> float:
    r = retrieved[:k]
    return sum(1 for x in r if x in relevant) / max(1, len(r))

def recall_at_k(retrieved: List[str], relevant: Set[str], k: int = 5) -> float:
    r = retrieved[:k]
    return sum(1 for x in r if x in relevant) / max(1, len(relevant))

def mrr(retrieved: List[str], relevant: Set[str]) -> float:
    for i, x in enumerate(retrieved, 1):
        if x in relevant:
            return 1.0 / i
    return 0.0

def ndcg_at_k(retrieved: List[str], relevant: Set[str], k: int = 5) -> float:
    dcg = 0.0
    for i, x in enumerate(retrieved[:k], 1):
        rel = 1.0 if x in relevant else 0.0
        dcg += (2**rel - 1) / math.log2(i + 1)
    ideal = sum((2**1 - 1) / math.log2(i + 1) for i in range(1, min(k, len(relevant)) + 1))
    return dcg / ideal if ideal > 0 else 0.0

# Use recovered_ids reais do seu retriever
recovered_ids = ["d3", "d2", "d1"]
print("P@5=", precision_at_k(recovered_ids, truth["o que é RAG?"], 5))
print("R@5=", recall_at_k(recovered_ids, truth["o que é RAG?"], 5))
print("MRR=", mrr(recovered_ids, truth["o que é RAG?"],))
print("nDCG@5=", ndcg_at_k(recovered_ids, truth["o que é RAG?"], 5))
```

---

## 12) Boas práticas, Erros comuns e Checklists

### Boas práticas

* Versione `dataset/splitter/embedding/index` (ex.: `v2-e5-base-768-c800o150`).
* Mantenha `k` pequeno e use **MMR** quando precisar de diver
