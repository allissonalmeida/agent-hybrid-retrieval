# Agent-Oriented Model for Hybrid Retrieval and Auditing of Long Texts

Este projeto apresenta uma arquitetura baseada em agentes para sistemas RAG (Retrieval-Augmented Generation), focada na recuperação híbrida e auditoria factual de textos longos[cite: 2]. O objetivo é gerar respostas que sejam estritamente verificáveis e baseadas no contexto, mitigando problemas como o fenômeno *lost in the middle* e alucinações, especialmente em domínios técnicos e complexos[cite: 2].

## 🏗 Arquitetura do Sistema

O fluxo de trabalho do sistema é dividido em três fases principais[cite: 2]:

* **Fase 1: Ingestão de Dados**
  * Os documentos são divididos usando a função `RecursiveCharacterTextSplitter`[cite: 2].
  * A indexação ocorre por duas vias: um caminho denso (vetores gerados pelo `Qwen3-Embedding-8B` e armazenados no banco `ChromaDB` via algoritmo HNSW) e um caminho lexical (algoritmo `BM25` baseado em TF-IDF)[cite: 2].

* **Fase 2: Núcleo Agêntico (*Agentic Core*)**
  * O motor é modelado como um grafo de estados no `LangGraph`[cite: 2].
  * Combina os resultados das buscas vetorial e lexical utilizando o algoritmo Reciprocal Rank Fusion (RRF)[cite: 2].
  * A geração da resposta é realizada localmente pelo modelo `Llama-3.1-8B-Instruct`[cite: 2].
  * **Nó Auditor:** Opera sob o modelo *LLM-as-a-Judge* para validar se a resposta é fiel ao contexto[cite: 2]. Se a pontuação de fidelidade for menor que 0.7, o auditor exerce poder de veto e redireciona o fluxo de volta ao gerador com um alerta injetado para corrigir a alucinação[cite: 2].

* **Fase 3: Aplicação**
  * Componente responsável pela execução automatizada de testes em benchmarks e extração das métricas de desempenho[cite: 2].

## 🛠 Tecnologias Utilizadas

* **Orquestração de Agentes:** LangGraph[cite: 2].
* **Geração e Auditoria (LLM):** Llama-3.1-8B-Instruct quantizado em 4 bits (Q4_0), executado via Ollama[cite: 2].
* **Modelo de Embeddings:** Qwen3-Embedding-8B[cite: 2].
* **Banco Vetorial:** ChromaDB[cite: 2].
* **Algoritmo de Fusão de Busca:** Reciprocal Rank Fusion (RRF)[cite: 2].

## 📊 Experimentos e Resultados

O sistema foi empiricamente avaliado no benchmark biomédico PubMedQA (subconjunto não rotulado)[cite: 2]. A proposta principal (busca híbrida com auditor cíclico ativo) alcançou as seguintes métricas em comparação com uma *baseline* executada no GPT-40[cite: 2]:

* **Precisão de Contexto (*Context Precision*):** 75.11% (superando os 50.60% do modelo sem auditoria e os 74.36% da *baseline*)[cite: 2].
* **Revocação de Contexto (*Context Recall*):** 87.00%[cite: 2].
* **Fidelidade Factual (*Faithfulness*):** 98.20% (significativamente superior aos 89.40% alcançados pela *baseline*)[cite: 2].

## 🎯 Conclusões

* A busca híbrida combinada à restrição gerativa e o escrutínio algorítmico do modelo provam que controlar o fluxo do agente traz maiores vantagens competitivas do que apenas utilizar modelos com mais parâmetros[cite: 2].
* A validação cíclica com o auditor ativo age de fato como uma barreira efetiva (guardrail) contra saídas inconsistentes e alucinações[cite: 2].
