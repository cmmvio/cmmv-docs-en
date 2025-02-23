# AI

Repository: [https://github.com/cmmvio/cmmv-ai](https://github.com/cmmvio/cmmv-ai)

The **@cmmv/ai** module provides full support for **Retrieval-Augmented Generation (RAG)** and, in the future, **Retrieval-Augmented Synthesis (RAS)** for **LLMs**. It enables code understanding and generation, supporting:

* **Tokenization & Code Mapping** – Extracts structured tokens from TypeScript/JavaScript files.
* **RAG Dataset Creation** – Generates binary datasets for vector search.
* **Vector Search with FAISS & Vector Databases** – Supports **Qdrant, Milvus, Neo4j**.
* **Hugging Face Integration** – Uses `transformers` for embeddings.
* **Custom Embedding Models** – Supports `WhereIsAI/UAE-Large-V1`, `MiniLM`, `CodeLlama`, `DeepSeek`, and others.
* **Database Integration** – Supports `Elasticsearch`, `Pinecone`, `Qdrant`, `PGVector`, and others.
* **LLM Integration** - Supports `OpenAI`, `Hugging Face`, `Ollama`, `DeepSeek`, `Groq`, `Gemini`, and others.

## Install Python

Before installing the Hugging Face CLI, ensure that **Python** is installed on your system.

Run the following command to install Python on Ubuntu:

```sh
$ sudo apt update && sudo apt install python3 python3-pip -y
```

For other operating systems, refer to the official [Python download page](https://www.python.org/downloads/).

## Install HuggingFace CLI

Once Python is installed, install the Hugging Face CLI using **pip**:

```sh
$ pip3 install -U "huggingface_hub[cli]"
```

## Ensure the CLI is Recognized

If your terminal does not recognize `huggingface-cli`, add `~/.local/bin` to your system **PATH**:

```sh
$ echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
$ source ~/.bashrc
```

Run the following command to verify installation:

```sh
$ huggingface-cli --help
```

If the command works, the installation was successful! 🎉

## Authenticate with HuggingFace

To access and download models, you need to authenticate.

Run:

```sh
$ huggingface-cli login
```

You will be prompted to enter your **Hugging Face access token**.
Generate one at: [Hugging Face Tokens](https://huggingface.co/settings/tokens)
Ensure the token has **READ** permissions.

## Downloading Models

To download a model, use the following command:

```sh
$ huggingface-cli download meta-llama/CodeLlama-7B-Python-hf --local-dir ./models/CodeLlama-7B
```

This will download the **CodeLlama 7B Python** model into the `./models/CodeLlama-7B` directory.

For **CMMV**, set the model path in `.cmmv.config.cjs`:

```js
huggingface: {
    token: process.env.HUGGINGFACE_HUB_TOKEN,
    localModelPath: './models',
    allowRemoteModels: true
},
llm: {
    provider: "google",
    embeddingTopk: 10,
    modelName: "gemini-1.5-pro",
    textMaxTokens: 2048,
    apiKey: process.env.GOOGLE_API_KEY,
    language: 'pt-br'
}
```

Now your environment is set up to use Hugging Face models with **CMMV**! 🚀

## Configuration

Given the complexity of this module, several configuration settings are required, especially for defining which models to use at each stage. Below is the **base configuration** with all currently available properties.

### **Example Configuration (`.cmmv.config.cjs`)**
<br/>

```javascript
require('dotenv').config();

module.exports = {
    env: process.env.NODE_ENV,

    ai: {
        huggingface: {
            token: process.env.HUGGINGFACE_HUB_TOKEN,
            localModelPath: './models',
            allowRemoteModels: true
        },
        tokenizer: {
            provider: "huggingface",
            model: "sentence-transformers/distilbert-base-nli-mean-tokens",
            indexSize: 768,
            useKeyBERT: false,
            chunkSize: 1000,
            chunkOverlap: 0,
            patterns: [
                //'../cmmv/**/*.ts',
                //'../cmmv/src/**/*.ts',
                //'../cmmv/packages/**/*.ts',
                //'../cmmv-*/**/*.ts',
                //'../cmmv-*/src/*.ts',
                //'../cmmv-*/src/**/*.ts',
                //'../cmmv-*/packages/**/*.ts',
                '../cmmv-*/**/*.md',
                '../cmmv-docs/docs/en/**/*.md'
            ],
            output: "./samples/data.bin",
            ignore: [
                "node_modules", "*.d.ts", "*.cjs",
                "*.spec.ts", "*.test.ts", "/tools/gulp/"
            ],
            exclude: [
                "cmmv-formbuilder", "cmmv-ui",
                "cmmv-language-tools", "cmmv-vue",
                "cmmv-reactivity", "cmmv-vite-plugin",
                "eslint.config.ts", "vitest.config.ts",
                "auto-imports.d.ts", ".d.ts", ".cjs",
                ".spec.ts", ".test.ts", "/tools/gulp/",
                "node_modules"
            ]
        },
        vector: {
            provider: "neo4j",
            qdrant: {
                url: 'http://localhost:6333',
                collection: 'embeddings'
            },
            neo4j: {
                url: "bolt://localhost:7687",
                username: process.env.NEO4J_USERNAME,
                password: process.env.NEO4J_PASSWORD,
                indexName: "vector",
                keywordIndexName: "keyword",
                nodeLabel: "Chunk",
                embeddingNodeProperty: "embedding"
            }
        },
        llm: {
            provider: "google",
            embeddingTopk: 10,
            model: "gemini-1.5-pro",
            textMaxTokens: 2048,
            apiKey: process.env.GOOGLE_API_KEY,
            language: 'pt-br'
        }
    }
};
```

| **Path**                                      | **Description**                                      | **Default Value / Example**                         |
|-----------------------------------------------|--------------------------------------------------|------------------------------------------------|
| `ai.huggingface.token`                        | API token for Hugging Face Hub                   | `process.env.HUGGINGFACE_HUB_TOKEN`            |
| `ai.huggingface.localModelPath`               | Path for local models                            | `./models`                                     |
| `ai.huggingface.allowRemoteModels`            | Allow downloading models from Hugging Face Hub  | `true`                                         |
| `ai.tokenizer.provider`                       | Tokenizer provider                              | `"huggingface"`                                |
| `ai.tokenizer.model`                          | Tokenizer model                                 | `"sentence-transformers/distilbert-base-nli-mean-tokens"` |
| `ai.tokenizer.indexSize`                      | Token embedding index size                      | `768`                                          |
| `ai.tokenizer.useKeyBERT`                     | Enable KeyBERT for keyword extraction          | `false`                                        |
| `ai.tokenizer.chunkSize`                      | Size of text chunks for processing             | `1000`                                         |
| `ai.tokenizer.chunkOverlap`                   | Overlap size between text chunks               | `0`                                            |
| `ai.tokenizer.patterns`                       | File patterns to scan for tokenization         | `['../cmmv-*/**/*.md', '../cmmv-docs/docs/en/**/*.md']` |
| `ai.tokenizer.output`                         | Output file for tokenized data                 | `"./samples/data.bin"`                         |
| `ai.tokenizer.ignore`                         | File patterns to ignore                        | `["node_modules", "*.d.ts", "*.cjs", "*.spec.ts", "*.test.ts", "/tools/gulp/"]` |
| `ai.tokenizer.exclude`                        | Files and directories to exclude               | `["cmmv-formbuilder", "cmmv-ui", "cmmv-language-tools", "cmmv-vue", "cmmv-reactivity", "cmmv-vite-plugin", "eslint.config.ts", "vitest.config.ts", "auto-imports.d.ts", ".d.ts", ".cjs", ".spec.ts", ".test.ts", "/tools/gulp/", "node_modules"]` |
| `ai.vector.provider`                          | Provider for vector storage                    | `"neo4j"`                                      |
| `ai.vector.qdrant.url`                        | Qdrant service URL                             | `"http://localhost:6333"`                      |
| `ai.vector.qdrant.collection`                 | Collection name for Qdrant                     | `"embeddings"`                                 |
| `ai.vector.neo4j.url`                         | Neo4j database URL                             | `"bolt://localhost:7687"`                      |
| `ai.vector.neo4j.username`                    | Neo4j username                                 | `process.env.NEO4J_USERNAME`                   |
| `ai.vector.neo4j.password`                    | Neo4j password                                 | `process.env.NEO4J_PASSWORD`                   |
| `ai.vector.neo4j.indexName`                   | Index name for vector storage                  | `"vector"`                                     |
| `ai.vector.neo4j.keywordIndexName`            | Index name for keyword search                  | `"keyword"`                                    |
| `ai.vector.neo4j.nodeLabel`                   | Label for vectorized nodes                     | `"Chunk"`                                      |
| `ai.vector.neo4j.embeddingNodeProperty`       | Property storing vector embeddings             | `"embedding"`                                  |
| `ai.llm.provider`                             | LLM provider                                  | `"google"`                                     |
| `ai.llm.embeddingTopk`                        | Number of top-k results for embeddings        | `10`                                           |
| `ai.llm.model`                                | LLM model name                                | `"gemini-1.5-pro"`                             |
| `ai.llm.textMaxTokens`                        | Maximum tokens per request                    | `2048`                                         |
| `ai.llm.apiKey`                               | API key for the LLM provider                  | `process.env.GOOGLE_API_KEY`                   |
| `ai.llm.language`                             | Default language                              | `"pt-br"`                                      |

## Converting Models

Some **LLMs (Large Language Models)** are not natively compatible with all inference frameworks. A key example is **Google’s Gemma**, which is not directly supported by many tools. To use such models efficiently, you need to **convert them to ONNX format**.

ONNX (**Open Neural Network Exchange**) is an open format that optimizes models for efficient inference across multiple platforms. Many inference frameworks, such as **ONNX Runtime**, **TensorRT**, and **OpenVINO**, support ONNX for faster and more scalable deployment.

Before converting, install the necessary packages:

```sh
$ pip3 install -U "optimum[exporters]" onnx onnxruntime
```

To convert **Google's Gemma 2B model**, run:

```sh
$ python3 -m optimum.exporters.onnx --model google/gemma-2b ./models/gemma-2b-onnx
```

## Tokenizer

The Tokenizer class is responsible for processing and indexing files into the RAG pipeline. It provides several configurations, including:

* File Search Patterns (`patterns`): Defines glob patterns to search for files.
* Exclusions & Ignored Files (`ignore`, `exclude`): Filters out unnecessary files from indexing.
* Embeddings Model (`model`): Selects the Hugging Face model to generate embeddings.
* Index Size (`indexSize`): Adjusts the embedding dimensions based on the selected model.
* Chunking (`chunkSize`, `chunkOverlap`): Controls data segmentation for better retrieval.
* KeyBERT Metadata Generation (`useKeyBERT`): Extracts keywords to enhance vector searches.

For other models that require authentication (e.g., `LLaMA`), provide the API key under huggingface.token.

### Example Usage:
<br/>

```typescript
import { Application, Hook, HooksType } from '@cmmv/core';

class TokenizerSample {
    @Hook(HooksType.onInitialize)
    async start() {
        const { Tokenizer } = await import('@cmmv/ai');
        const tokenizer = new Tokenizer();
        tokenizer.start();
    }
}

Application.exec({
    services: [TokenizerSample],
});
```
<br/>

1. **Scans project directories** based on the `patterns` config.
2. **Parses TypeScript/JavaScript/Markdown files**, extracting **functions, classes, enums, interfaces, constants, and decorators**.
3. **Generates embeddings** using Hugging Face models.
4. **Stores the dataset** in a binary `.bin` file.

## Common Embedding Models

| **Embedding**   | **Default Model**                         | **Requires API Key** |
|----------------|-----------------------------------------|---------------------|
| Bedrock       | amazon.titan-embed-text-v1             | Yes                 |
| Cohere        | embed-english-v3.0                     | No                  |
| DeepInfra     | -                                       | Yes                 |
| Doubao        | -                                       | Yes                 |
| Fireworks     | nomic-ai/nomic-embed-text-v1.5         | Yes                 |
| HuggingFace   | Xenova/all-MiniLM-L6-v2                | No                  |
| LlamaCpp      | - (requires local model file)          | No                  |
| OpenAI        | text-embedding-3-large                 | Yes                 |
| Pinecone      | multilingual-e5-large                  | No                  |
| Tongyi        | -                                       | Yes                 |
| Watsonx       | -                                       | Yes                 |
| Jina          | jina-clip-v2                            | Yes                 |
| MiniMax       | embo-01                                | No                  |
| Premai        | -                                       | No                  |
| Hunyuan       | -                                       | Yes                 |
| TensorFlow    | -                                       | No                  |
| TogetherAI    | togethercomputer/m2-bert-80M-8k-retrieval | Yes                 |
| Voyage        | voyage-01                               | Yes                 |
| ZhipuAI       | embedding-2                            | Yes                 |

* *[https://huggingface.co/models?pipeline_tag=feature-extraction&library=transformers.js&sort=downloads](https://huggingface.co/models?pipeline_tag=feature-extraction&library=transformers.js&sort=downloads)*
* *[https://v03.api.js.langchain.com/index.html](https://v03.api.js.langchain.com/index.html)*

## Using KeyBERT

KeyBERT is an optional feature that enhances indexing by extracting relevant keywords. It helps refine search results in **FAISS** or vector databases, improving the accuracy of **LLM queries**.

Unlike **TF-IDF**, **YAKE!**, or **RAKE**, which rely on statistical methods, **KeyBERT** leverages **BERT embeddings** to generate more meaningful keywords. This results in better search filtering, leading to more precise **LLM-based responses**.

If KeyBERT is **not enabled**, the default keyword extraction method will be **TF-IDF**, which may not be as accurate but is significantly faster.

Before using KeyBERT, ensure you have **Python 3** installed. Then, install KeyBERT using **pip**:

```bash
$ pip install keybert
```

Once installed, KeyBERT will be used **during tokenization** to generate **filtering keywords**. These keywords improve the ranking of indexed content, making vector-based search results more relevant.

If you prefer **faster processing**, you can disable KeyBERT, and the system will fall back to **TF-IDF**.

To enable **KeyBERT**, update your `.cmmv.config.cjs` file:

```javascript
module.exports = {
    ai: {
        tokenizer: {
            useKeyBERT: true // Set to false to use TF-IDF instead
        }
    }
};
```

With **KeyBERT enabled**, search filtering becomes more **context-aware**, leading to more **accurate LLM responses**.

For more details on KeyBERT, visit: [KeyBERT Documentation](https://github.com/MaartenGr/KeyBERT).

## Dataset

The **Dataset** class manages **vectorized storage** for quick retrieval.

* Saves embeddings in **binary format** (`.bin`).
* In-memory **FAISS-based search**.
* Support for **Neo4j, Elasticsearch, PgVector, Qdrant**.

```typescript
const dataset = new Dataset();
dataset.save(); // Saves the dataset in binary format
dataset.load(); // Loads the dataset into memory
```

To efficiently store and search **embeddings**  `@cmmv/ai`.

| Database       | Open Source | Node.js Support | Storage Backend | Similarity Search                      |
|---------------|------------|----------------|-----------------|--------------------------------------|
| **Qdrant**    | ✅ Yes     | ✅ Yes (`@qdrant/js-client-rest`) | Disk/Memory | Cosine, Euclidean, Dot Product    |
| **Milvus**    | ✅ Yes     | ✅ Yes (`@zilliz/milvus2-sdk-node`) | Disk/Memory | IVF_FLAT, HNSW, PQ                |
| **Neo4j**     | ✅ Yes (Community) | ✅ Yes (`neo4j-driver`) | GraphDB | Cypher-based vector search        |
| **Elasticsearch** | ✅ Yes     | ✅ Yes (`@elastic/elasticsearch`) | Disk | k-NN, Approximate Nearest Neighbors (ANN) |
| **PGVector**  | ✅ Yes     | ✅ Yes (`pg`) | PostgreSQL | Cosine, Euclidean, Inner Product  |

To run these databases locally, use the following
**Docker commands**:

### **🔹 Qdrant**
```bash
$ docker run -p 6333:6333 --name qdrant-server qdrant/qdrant
```
<br/>

* Runs a **Qdrant** server on port `6333`.
* API available at `http://localhost:6333`.

### **🔹 Milvus**
```bash
$ docker run -p 19530:19530 --name milvus-server milvusdb/milvus
```
<br/>

* Runs **Milvus** on port `19530`.
* Requires **Python/Node SDK** for interaction.

### **🔹 Neo4j**
```bash
$ docker run --publish=7474:7474 --publish=7687:7687 --volume=$HOME/neo4j/data:/data --name neo4j-server neo4j
```
<br/>

* Runs **Neo4j** on ports `7474` (HTTP) and `7687` (Bolt).
* Data is stored persistently in `$HOME/neo4j/data`.

### **🔹 PGVector**
```bash
docker run --name pgvector-db -e POSTGRES_USER=admin -e POSTGRES_PASSWORD=admin -e POSTGRES_DB=vector_db -p 5432:5432 -d ankane/pgvector
```
* Runs **PostgreSQL** with PGVector on port `5432`.
* Default database is `vector_db` with user `admin` and password `admin`.

### **🔹 Elasticsearch**
```bash
docker run -d --name elasticsearch -p 9200:9200 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:8.5.1
```
* Runs **Elasticsearch** on port `9200`.
* Single-node mode is enabled for local use.

## LLMs

The @cmmv/ai module includes support for multiple LLMs (Large Language Models), allowing flexible integration with different providers. Currently, the following models are supported:

* DeepSeek – Optimized for programming and technical research.
* Gemini (Google) – A multimodal LLM with advanced reasoning capabilities.
* Hugging Face – Compatible with open-source models such as CodeLlama, MiniLM, DeepSeek, and more.
* OpenAI (ChatGPT) – Integration with models like GPT-4 and GPT-3.5.
* Ollama (Facebook) – Local model execution for privacy-focused applications.
* Groq (X) – High-speed inference with LLama-3, Mixtral, and Gemma models.

| **LLM Provider**  | **Default Model**                      | **Requires API Key** |
|-------------------|--------------------------------------|---------------------|
| **AI21 Labs**     | `j1-jumbo`, `j1-large`               | Yes                 |
| **Aleph Alpha**   | `luminous-base`, `luminous-extended` | Yes                 |
| **Anthropic**     | `claude-3-haiku-20240307`           | Yes                 |
| **AWS Bedrock**   | Various models (Claude, Mistral, etc.) | Yes             |
| **Cohere**        | `command-xlarge-nightly`, `command-medium` | Yes        |
| **DeepInfra**     | Various models                      | Yes                 |
| **DeepSeek**      | `deepseek-ai/deepseek-coder-7b`     | No                  |
| **Fireworks**     | Various models                      | Yes                 |
| **Google Gemini** | `gemini-1.5-pro`                   | Yes                 |
| **Google Vertex AI** | `text-bison@001`                   | Yes                 |
| **Groq**          | `llama3-8b`, `mixtral`              | Yes                 |
| **Hugging Face**  | `code-llama`, `MiniLM`, etc.        | No                  |
| **Mistral AI**    | `mistral-7b`, `mixtral`             | Yes                 |
| **Ollama**        | `llama3`, `mistral`, `gemma`        | No (local execution) |
| **OpenAI**        | `gpt-4`, `gpt-3.5`                  | Yes                 |
| **Together AI**   | `GPT-JT-6B-v1`                      | Yes                 |
| **Vertex AI**     | `text-bison@001`                    | Yes                 |

The search interface is accessible via the Search class, which performs semantic search using embeddings and generates context-aware responses.

*[https://v03.api.js.langchain.com/index.html](https://v03.api.js.langchain.com/index.html)*

### Configuration

LLM settings can be adjusted in the `.cmmv.config.cjs` file. Below is an example configuration for using Google's Gemini 1.5 Pro model:

```javascript
module.exports = {
    ai: {
        llm: {
            provider: "google",  // Options: "openai", "deepseek", "huggingface", "gemini", "ollama", "groq"
            model: "gemini-1.5-pro", // Default model for the selected provider
            embeddingTopk: 10, // Number of top-k results used for context retrieval
            textMaxTokens: 2048, // Maximum tokens per response
            apiKey: process.env.GOOGLE_API_KEY, // API key for the selected provider (if required)
            language: 'pt-br' // Default response language
        }
    }
}
```
<br/>

| **Path**          | **Description**                              | **Default Value / Example**                          |
|-------------------|--------------------------------------------|----------------------------------------------------|
| `llm.provider`   | LLM provider to use                        | `"google"` (`"openai"`, `"ollama"`, `"huggingface"`, `"groq"`) |
| `llm.model`      | LLM model used for responses               | `"gemini-1.5-pro"` (`"gpt-4"`, `"deepseek-coder-7b"`) |
| `llm.embeddingTopk` | Number of relevant embeddings to retrieve | `10`                                               |
| `llm.textMaxTokens` | Maximum tokens per request               | `2048`                                             |
| `llm.apiKey`     | API key for accessing the LLM provider     | `process.env.GOOGLE_API_KEY` (if required)         |
| `llm.language`   | Default language for responses             | `"pt-br"` (`"en"`, `"es"`, etc.)                   |

### Search Queries

The following example demonstrates how to use the LLM for generating responses based on retrieved vector data.

```typescript
import { Application, Hook, HooksType } from '@cmmv/core';

import {
    PromptTemplate,
    RunnableSequence,
    RunnablePassthrough,
    StringOutputParser,
    Embedding,
    Dataset,
    Search,
} from '@cmmv/ai';

class SearchSample {
    @Hook(HooksType.onInitialize)
    async start() {
        const question = 'How to create a CMMV controller?';

        const search = new Search();
        await search.initialize();

        const finalResult = await search.invoke(question);
        console.log(`LLM Response: `, finalResult.content);
    }
}

Application.exec({
    services: [SearchSample],
})
```

### How It Works
<br/>

* Search Retrieval: The search system retrieves the most relevant embeddings from the vector database.
* Context Injection: The retrieved data is inserted into the prompt.
* LLM Processing: The configured model generates a response based on the given context.
* JSON Output: The result is structured in JSON format for seamless integration.

This setup allows for accurate, context-driven responses while maintaining flexibility in model selection and configuration. 🚀
