---
layout: post
title: "Building a RAG workflow for your personal notes"
date: 2024-06-01 00:00:00
categories: [ai, rag]
---

Retrieval-augmented generation (RAG) is a low-friction way to ground AI answers in your own writing. You do not need a full vector database to get started; a handful of text files and a solid retrieval step can get you to a helpful assistant for personal knowledge work.

### Start with tidy source material

Keep the corpus small and consistent before you reach for tooling. Split long documents into sections of 300–500 tokens so that retrieval returns focused passages instead of entire essays. I keep Markdown notes in a single folder, run a script that exports them to plain text, and regenerate embeddings whenever I add or reorganize content.

### Choose embeddings you can update

Frequent edits are normal in a personal knowledge base, so prioritize embeddings that are cheap to regenerate. Open-source small models or budget-friendly API tiers work fine when the corpus is a few thousand chunks. Store both the chunk text and its metadata (title, path, tags) alongside the embedding so you can trace answers back to their source.

### Retrieval is more than cosine similarity

Vector search alone can return irrelevant snippets, especially when your notes include short todos or meeting bullets. Pair semantic search with simple keyword filtering: match the query terms against titles and headings first, then expand to the embedding index. For ambiguous queries, send the top few candidates to the model and ask it to re-rank based on relevance.

### Keep prompts short and cite sources

Concise prompts leave more room for retrieved context. I start with a system instruction like “Answer using only the provided context; cite filenames.” Then pass a bulleted list of chunks with their titles and line numbers. The model’s output includes citations so I can verify the answer or jump to the original note.

### Make updates cheap and visible

Schedule a nightly job that rebuilds embeddings and prints a diff of added or removed chunks. When the retrieval index changes, run a quick spot check by asking the assistant about new material. A small, inspectable pipeline keeps the personal RAG setup trustworthy and easy to maintain.
