# 🎯 OBJETIVO FINAL

Ao final do plano, ser  capaz de:

* Entender matematicamente o funcionamento de embeddings, atenção e similaridade semântica.
* Ler papers de NLP e LLMs com compreensão real (não só reproduzir código).
* Implementar do zero pequenos modelos de linguagem e sistemas de busca vetorial.

---

# 🗓️ PLANO DE ESTUDO (aprox. 6 meses)

Cada módulo dura **3 a 4 semanas** 

---

## 🔹 Módulo 1 – Fundamentos de Matemática e Estatística (4 semanas)

### 📘 Tópicos

* Álgebra Linear:

  * Vetores, matrizes, produto escalar, norma, projeções.
  * Autovalores, decomposição SVD e PCA.
* Probabilidade e Estatística:

  * Probabilidade condicional e regra de Bayes.
  * Variável aleatória, média, variância, covariância.
  * Entropia e distribuição de probabilidade.

### 💻 Prática

* Manipular vetores/matrizes com **NumPy**.
* Calcular **cosine similarity** entre embeddings.
* Fazer **PCA e visualização de embeddings** com `scikit-learn`.

### 📚 Recursos sugeridos

* Livro: *Matemática Essencial para Machine Learning* (Marc Peter Deisenroth et al.)
* Curso: [Khan Academy – Álgebra Linear e Probabilidade](https://pt.khanacademy.org/)
* Curso prático: [Linear Algebra for Machine Learning (Coursera)](https://www.coursera.org/learn/linear-algebra-machine-learning)

---

## 🔹 Módulo 2 – Cálculo e Otimização (3 semanas)

### 📘 Tópicos

* Derivadas e gradientes.
* Funções convexas e não convexas.
* Descida do gradiente (SGD, Adam).
* Funções de ativação e perda (ReLU, softmax, cross-entropy).

### 💻 Prática

* Implementar **gradient descent** do zero em Python.
* Treinar um modelo simples (regressão linear/logística).
* Visualizar superfícies de perda e gradientes.

### 📚 Recursos sugeridos

* Livro: *Deep Learning* (Goodfellow, Bengio, Courville) – capítulos 4 e 8.
* Curso: [Andrew Ng – Machine Learning (Coursera)](https://www.coursera.org/learn/machine-learning)
* Playground interativo: [https://playground.tensorflow.org/](https://playground.tensorflow.org/)

---

## 🔹 Módulo 3 – Fundamentos de Machine Learning (4 semanas)

### 📘 Tópicos

* Regressão linear e logística.
* Overfitting, underfitting e regularização.
* Redes neurais feed-forward e backpropagation.
* Funções de perda e otimização.
* Métricas e avaliação.

### 💻 Prática

* Implementar rede neural simples com **PyTorch**.
* Visualizar o impacto da regularização.
* Aplicar modelos em datasets pequenos (Iris, MNIST).

### 📚 Recursos sugeridos

* Livro: *Hands-On Machine Learning with Scikit-Learn & TensorFlow* (Aurélien Géron).
* Curso: [Deep Learning with PyTorch: A 60 Minute Blitz](https://pytorch.org/tutorials/beginner/deep_learning_60min_blitz.html)

---

## 🔹 Módulo 4 – Teoria da Informação e Linguagem (3 semanas)

### 📘 Tópicos

* Entropia, perplexidade e cross-entropy loss.
* Modelos de linguagem baseados em probabilidade (n-gramas, Markov).
* Introdução à Teoria da Informação (Shannon).
* Representação distribuída de palavras (Word2Vec, GloVe).

### 💻 Prática

* Calcular perplexidade de um modelo n-grama.
* Treinar embeddings com **gensim (Word2Vec)**.
* Visualizar palavras próximas em espaço vetorial (PCA, t-SNE).

### 📚 Recursos sugeridos

* Artigo: ["Word2Vec Explained"](https://arxiv.org/abs/1402.3722)
* Curso: [CS224N – Natural Language Processing with Deep Learning (Stanford)](http://web.stanford.edu/class/cs224n/)

---

## 🔹 Módulo 5 – Redes Neurais para NLP e Transformers (5 semanas)

### 📘 Tópicos

* Embeddings contextuais e autoatenção.
* Estrutura do Transformer (attention, residuals, layer norm).
* Masked Language Modeling (MLM).
* Fine-tuning de modelos pré-treinados (BERT, GPT, etc).

### 💻 Prática

* Implementar a camada de **self-attention** do zero.
* Fine-tuning com **Hugging Face Transformers** (BERT, DistilBERT).
* Avaliar embeddings em tarefas semânticas.

### 📚 Recursos sugeridos

* Livro: *The Illustrated Transformer* (Jay Alammar).
* Curso: [Hugging Face – NLP Course](https://huggingface.co/learn/nlp-course)
* Paper: [Attention Is All You Need](https://arxiv.org/abs/1706.03762)

---

## 🔹 Módulo 6 – Busca Semântica e Embeddings (4 semanas)

### 📘 Tópicos

* Espaços vetoriais e métricas de similaridade.
* Indexação vetorial (FAISS, Annoy).
* Retrieval-Augmented Generation (RAG).
* Fine-tuning de embeddings com *contrastive learning*.

### 💻 Prática

* Implementar um **sistema de busca semântica** usando **Sentence Transformers + FAISS**.
* Criar um **RAG pipeline** com LangChain ou LlamaIndex.
* Avaliar resultados com *recall@k* e *MRR*.

### 📚 Recursos sugeridos

* Repositório: [Sentence Transformers](https://www.sbert.net/)
* Curso: [DeepLearning.AI – Vector Databases and Semantic Search](https://www.deeplearning.ai/)
* Blog: [Pinecone – Semantic Search Guide](https://www.pinecone.io/learn/semantic-search/)

---

## 🔹 Módulo 7 – Projeto Final (2–3 semanas)

Monte um **projeto de portfólio completo**:

> **“Busca Semântica com LLM + RAG”**

### Etapas:

1. Criar embeddings de documentos com `sentence-transformers`.
2. Indexar vetores com FAISS ou Milvus.
3. Implementar um *retriever + LLM* para responder perguntas com contexto.
4. Avaliar precisão das respostas e explicar as métricas.

---

# 🧭 Resultado

Ao final do plano, dominar:

* Matemática e estatística aplicadas a NLP e LLMs.
* Construção de sistemas de busca semântica e RAG.
* Entendimento matemático profundo de embeddings e atenção.

---
