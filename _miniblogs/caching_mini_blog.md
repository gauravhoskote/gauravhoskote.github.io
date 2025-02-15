---
title: "Caching your Embeddings - Memory Optimization"
date: 2024-12-05
excerpt: ""
collection: miniblogs
---


While working on one of my projects at the University of Phoenix, I encountered an interesting challenge. The project required comparing the embeddings of potentially hundreds of strings. This was done in RAM since using a vector store felt like an overkill. To improve latency, I began caching the results using Python’s lru_cache.

Example :
```
@lru_cache(maxsize=None)  # Caching results to improve latency
    def get_embedding(self, text:str) -> list[object]:
        """Given a string, this method returns the vector embedding of the string
        Args:
            text: Sentence string
        Returns:
            embedding: Vector embedding of the string
        """
        # Sentence we want to encode. Example:
        sentence = [text]
        # Sentences are encoded by calling model.encode()
        embedding = AltCreditsModel.model.encode(sentence)
        return embedding
```

However, this approach intermittently threw an **Out of Memory (OOM) error**. The root cause? The maxsize parameter of lru_cache was set to None, allowing the cache to grow indefinitely.

To resolve this, we needed to set an upper bound on the number of items cached. But how could we estimate the memory usage of vector embeddings in RAM?

Here is a nifty technique I found out about in a blog by Qdrant:
```
memory_size = number_of_vectors * vector_dimension * 4 bytes * 1.5
```
This nifty technique, detailed in **[Qdrant’s Capacity Planning guide](https://qdrant.tech/documentation/guides/capacity-planning/)**, provides a clear way to estimate and optimize memory usage effectively.

By applying this calculation, we could balance performance and memory constraints, ensuring smooth execution even with large datasets.