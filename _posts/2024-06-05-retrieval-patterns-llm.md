---
layout: post
title: "Retrieval patterns that make LLM responses trustworthy"
date: 2024-06-05 00:00:00
categories: [ai, rag]
---

No retrieval pipeline is perfect, but a few battle-tested patterns dramatically improve how often a model answers with the right evidence. These habits keep hallucinations in check and help you debug quickly when something goes wrong.

### Pair dense and sparse search

Dense embeddings surface semantic matches while sparse methods (BM25, keyword filters) catch exact terminology like ticket numbers or acronyms. Use both and merge the results, weighting sparse hits higher when the query contains many proper nouns or numbers.

### Re-rank the short list

Even a good vector search returns occasional curveballs. Send the top 10–20 candidates to a cross-encoder or a cheap rerank model that scores each passage against the query. The extra hop is inexpensive and often promotes the truly relevant snippets to the top.

### Deduplicate aggressively

Users notice when the model cites the same paragraph twice. Collapse near-duplicate chunks by hashing their normalized text or comparing embeddings. Return unique passages with their source titles so the model has diverse context to reason over.

### Bound the context window

Stuffing more text rarely helps. Keep the total retrieved context under half of the model’s limit to leave space for instructions and the answer. If you need broader coverage, ask the model to summarize groups of passages first, then answer using those summaries.

### Keep a “why not found” path

Sometimes the right document truly is missing. When retrieval comes up empty or rerank scores are low, return a graceful fallback: explain that the system has no supporting evidence, suggest related queries, or route the request to a human. A clear negative path builds user trust faster than a confident guess.
