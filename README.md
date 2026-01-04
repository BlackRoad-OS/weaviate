# BlackRoad OS Vector Database

[![GitHub Repo stars](https://img.shields.io/github/stars/BlackRoad-OS/weaviate?style=social)](https://github.com/BlackRoad-OS/weaviate)
[![Go Reference](https://pkg.go.dev/badge/github.com/BlackRoad-OS/weaviate.svg)](https://pkg.go.dev/github.com/BlackRoad-OS/weaviate)
[![Build Status](https://github.com/BlackRoad-OS/weaviate/actions/workflows/.github/workflows/pull_requests.yaml/badge.svg?branch=main)](https://github.com/BlackRoad-OS/weaviate/actions/workflows/.github/workflows/pull_requests.yaml)
[![Go Report Card](https://goreportcard.com/badge/github.com/BlackRoad-OS/weaviate)](https://goreportcard.com/report/github.com/BlackRoad-OS/weaviate)

**BlackRoad OS Vector Database** is an open-source, cloud-native vector database that stores both objects and vectors, enabling semantic search at scale. Built and maintained by BlackRoad OS, Inc., it combines vector similarity search with keyword filtering, retrieval-augmented generation (RAG), and reranking in a single query interface. Common use cases include RAG systems, semantic and image search, recommendation engines, chatbots, and content classification.

The BlackRoad OS Vector Database supports two approaches to store vectors: automatic vectorization at import using integrated models (OpenAI, Cohere, HuggingFace, and others) or direct import of pre-computed vector embeddings. Production deployments benefit from built-in multi-tenancy, replication, RBAC authorization, and many other enterprise features.

## Installation

The database can be deployed using:

- Docker
- Kubernetes
- Cloud platforms (AWS, GCP, Azure)

## Getting started

You can easily start the BlackRoad OS Vector Database with [Docker](https://docs.docker.com/desktop/).
Create a `docker-compose.yml` file:

```yml
services:
  blackroad-vector-db:
    image: cr.weaviate.io/semitechnologies/weaviate:1.32.2
    ports:
      - "8080:8080"
      - "50051:50051"
    environment:
      ENABLE_MODULES: text2vec-model2vec
      MODEL2VEC_INFERENCE_API: http://text2vec-model2vec:8080

  # A lightweight embedding model that will generate vectors from objects during import
  text2vec-model2vec:
    image: cr.weaviate.io/semitechnologies/model2vec-inference:minishlab-potion-base-32M
```

Start the database with:

```bash
docker compose up -d
```

Install the Python client (or use another client library):

```bash
pip install -U weaviate-client
```

The following Python example shows how easy it is to populate the database with data, create vector embeddings and perform semantic search:

```python
import weaviate
from weaviate.classes.config import Configure, DataType, Property

# Connect to BlackRoad OS Vector Database
client = weaviate.connect_to_local()

# Create a collection
client.collections.create(
    name="Article",
    properties=[Property(name="content", data_type=DataType.TEXT)],
    vector_config=Configure.Vectors.text2vec_model2vec(),  # Use a vectorizer to generate embeddings during import
    # vector_config=Configure.Vectors.self_provided()  # If you want to import your own pre-generated embeddings
)

# Insert objects and generate embeddings
articles = client.collections.get("Article")
articles.data.insert_many(
    [
        {"content": "Vector databases enable semantic search"},
        {"content": "Machine learning models generate embeddings"},
        {"content": "BlackRoad OS supports hybrid search capabilities"},
    ]
)

# Perform semantic search
results = articles.query.near_text(query="Search objects by meaning", limit=1)
print(results.objects[0])

client.close()
```

This example uses the `Model2Vec` vectorizer, but you can choose any other embedding model provider or bring your own pre-generated vectors.

## Client libraries and APIs

The database provides client libraries for several programming languages:

- Python
- JavaScript/TypeScript
- Java
- Go
- C# (Coming soon)

The system exposes REST API, gRPC API, and GraphQL API to communicate with the database server.

## Key features

These features enable you to build AI-powered applications:

- **⚡ Fast Search Performance**: Perform complex semantic searches over billions of vectors in milliseconds. Built in Go for speed and reliability, ensuring your AI applications are highly responsive even under heavy load.

- **🔌 Flexible Vectorization**: Seamlessly vectorize data at import time with integrated vectorizers from OpenAI, Cohere, HuggingFace, Google, and more. Or import your own vector embeddings.

- **🔍 Advanced Hybrid & Image Search**: Combine the power of semantic search with traditional keyword (BM25) search, image search and advanced filtering to get the best results with a single API call.

- **🤖 Integrated RAG & Reranking**: Go beyond simple retrieval with built-in generative search (RAG) and reranking capabilities. Power sophisticated Q&A systems, chatbots, and summarizers directly from your database without additional tooling.

- **📈 Production-Ready & Scalable**: Built for mission-critical applications. Go from rapid prototyping to production at scale with native support for horizontal scaling, multi-tenancy, replication, and fine-grained role-based access control (RBAC).

- **💰 Cost-Efficient Operations**: Radically lower resource consumption and operational costs with built-in vector compression. Vector quantization and multi-vector encoding reduce memory usage with minimal impact on search performance.

## Contributing

We welcome and appreciate contributions! Please see our contributor guide for the development setup, code style guidelines, testing requirements and the pull request process.

Join our community to discuss ideas and get help.

## License

BSD 3-Clause License. See [LICENSE](./LICENSE) for details.

---

*This project is based on Weaviate, an open-source vector database. BlackRoad OS, Inc. maintains this fork with additional features and enterprise support.*
