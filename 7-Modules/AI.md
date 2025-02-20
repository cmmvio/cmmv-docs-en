# AI

The **@cmmv/ai** module provides full support for **Retrieval-Augmented Generation (RAG)** and, in the future, **Retrieval-Augmented Synthesis (RAS)** for **LLMs**. It enables code understanding and generation, supporting:

- **Embeddings**: Integration with models from **Hugging Face**, **LLaMA**, and more.
- **Vector Databases**: Native support for **Qdrant**, **Neo4j**, and others.
- **LLM Interfaces**: Compatibility with **Gemini**, **LLaMA**, **ChatGPT**, and more.
- **LangChain Community**: Built on `@langchain/community` to leverage stable AI utilities.

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
    allowRemoteModels: false
},
search: {
    embeddingTopk: 10,
    codeModel: "./models/CodeLlama-7B",
    codeMaxTokens: 512,
    textModel: "google-bert/bert-base-uncased",
    textMaxTokens: 4000,
    baseCodeQuestion: ""
}
```

Now your environment is set up to use Hugging Face models with **CMMV**! 🚀

## **Configuration**

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
            chunkSize: 500,
            chunkOverlap: 100,
            patterns: [
                '../cmmv/**/*.ts',
                '../cmmv/src/**/*.ts',
                '../cmmv/packages/**/*.ts',
                '../cmmv-*/**/*.ts',
                '../cmmv-*/src/*.ts',
                '../cmmv-*/src/**/*.ts',
                '../cmmv-*/packages/**/*.ts',
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
            provider: "qdrant",
            qdrant: {
                url: 'http://localhost:6333',
                collection: 'embeddings'
            }
        },
        search: {
            embeddingTopk: 10,
            useCodeModel: true,
            codeModel: "Xenova/codegen-350M-mono",
            codeMaxTokens: 328,
            textModel: "google-bert/bert-base-uncased",
            textMaxTokens: 4000,
            baseCodeQuestion: ""
        }
    }
};
```

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
import { AIModule } from '@cmmv/ai';

class TokenizerSample {
    @Hook(HooksType.onInitialize)
    async start() {
        const { Tokenizer } = await import('@cmmv/ai');
        const tokenizer = new Tokenizer();
        tokenizer.start();
    }
}

Application.exec({
    modules: [AIModule],
    services: [TokenizerSample],
});
```
<br/>

1. **Scans project directories** based on the `patterns` config.
2. **Parses TypeScript/JavaScript/Markdown files**, extracting **functions, classes, enums, interfaces, constants, and decorators**.
3. **Generates embeddings** using Hugging Face models.
4. **Stores the dataset** in a binary `.bin` file.

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

## Dataset - FAISS & Vector Storage

The **Dataset** class manages **vectorized storage** for quick retrieval.

* Saves embeddings in **binary format** (`.bin`).
* In-memory **FAISS-based search**.
* Future support for **Neo4j, Qdrant, Pinecone**.

```typescript
const dataset = new Dataset();
dataset.save(); // Saves the dataset in binary format
dataset.load(); // Loads the dataset into memory
```

To efficiently store and search **embeddings**, `@cmmv/ai` supports **Qdrant, Milvus, and Neo4j**.

To run these databases locally, use the following
**Docker commands**:

### **🔹 Qdrant**
```bash
$ docker run -p 6333:6333 --name qdrant-server qdrant/qdrant
```
<br/>
- Runs a **Qdrant** server on port `6333`.
- API available at `http://localhost:6333`.

### **🔹 Milvus**
```bash
$ docker run -p 19530:19530 --name milvus-server milvusdb/milvus
```
<br/>
- Runs **Milvus** on port `19530`.
- Requires **Python/Node SDK** for interaction.

### **🔹 Neo4j**
```bash
$ docker run --publish=7474:7474 --publish=7687:7687 --volume=$HOME/neo4j/data:/data --name neo4j-server neo4j
```
<br/>
- Runs **Neo4j** on ports `7474` (HTTP) and `7687` (Bolt).
- Data is stored persistently in `$HOME/neo4j/data`.

# Future Integration

The next step is integrating **pre-trained models** for **code understanding and generation** using the tokenized dataset.

- [x] Tokenization of **functions, classes, interfaces, decorators**.
- [x] **FAISS-based vector search** for in-memory retrieval.
- [x] Integration with KeyBert for keyword generation.
- [ ] Externalize HTTP server for communication with APIs
- [ ] Integration with **Qdrant, Milvus, Neo4j**.
- [ ] Using **DeepSeek Code** for **LLM-powered code generation**.
- [ ] Integrate external LLM APIs like ChatGPT, Gemini, etc.
