# RAG Implementation with LangChain, Amazon Bedrock, and OpenSearch

This repository provides a sample implementation of Retrieval-Augmented Generation (RAG) by leveraging Amazon Bedrock’s Titan Embeddings Generation 1 (G1) for text embedding. These embeddings are stored in Amazon OpenSearch with vector engine support, improving prompt engineering for more precise LLM responses.

## Overview

1. Generate text embeddings using Amazon Bedrock’s Titan model.
2. Store the embeddings in Amazon OpenSearch with vector engine support.
3. Use LangChain to retrieve relevant embeddings for refined prompt engineering.
4. Query the LLM based on retrieved embeddings to generate contextual responses.

## Model Selection

The `--bedrock-model-id` parameter allows selection of a foundation model. By default, it is set to [Anthropic Claude v2](https://aws.amazon.com/bedrock/claude/), but other models can be specified.

### Example Queries:

#### Anthropic Models:

- Claude v2:
  ```bash
  python ask-bedrock-with-rag.py --ask "How will AI impact daily life?"
  ```
- Claude v1.3:
  ```bash
  python ask-bedrock-with-rag.py --bedrock-model-id anthropic.claude-v1 --ask "How will AI impact daily life?"
  ```
- Claude Instant v1.2:
  ```bash
  python ask-bedrock-with-rag.py --bedrock-model-id anthropic.claude-instant-v1 --ask "How will AI impact daily life?"
  ```

#### AI21 Labs Models:

- Jurassic-2 Ultra:
  ```bash
  python ask-bedrock-with-rag.py --bedrock-model-id ai21.j2-ultra-v1 --ask "How will AI impact daily life?"
  ```
- Jurassic-2 Mid:
  ```bash
  python ask-bedrock-with-rag.py --bedrock-model-id ai21.j2-mid-v1 --ask "How will AI impact daily life?"
  ```

## Prerequisites

1. **Python Environment**

   - Compatible with Python 3.11.4
   - It is recommended to use a virtual environment:
     ```bash
     pip install virtualenv
     python -m virtualenv venv
     source ./venv/bin/activate
     ```

2. **Install Required Packages**

   ```bash
   pip install -r requirements.txt
   ```

3. **Install Terraform for OpenSearch Deployment**

   ```bash
   brew tap hashicorp/tap
   brew install hashicorp/tap/terraform
   ```

4. **Enable Model Access**

   - Visit the [Model Access Page](https://us-east-1.console.aws.amazon.com/bedrock/home?region=us-east-1#/modelaccess) to activate the desired foundation models.

## Usage Instructions

### 1. Deploy OpenSearch Cluster

Use Terraform to provision the OpenSearch cluster:

```bash
cd ./terraform
terraform init
terraform apply -auto-approve
```

**Note:** This setup is for testing purposes and uses a public endpoint.

### 2. Load Embeddings into OpenSearch

Retrieve and process the dataset from [Hugging Face](https://huggingface.co/datasets/sentence-transformers/embedding-training-data) using Titan embeddings.

```bash
python load-data-to-opensearch.py --recreate 1 --early-stop 1
```

**Optional Arguments:**

- `--recreate`: Rebuilds the OpenSearch index.
- `--early-stop`: Loads only 100 embedded documents.
- `--index`: Specifies an index name (default: **rag**).
- `--region`: Defines the AWS region (default: **us-east-1**).
- `--multi-tenant`: Enables multi-tenancy (loads data with tenant IDs 1-5).

### 3. Query LLM with RAG

Once the embeddings are loaded, query the LLM:

```bash
python ask-bedrock-with-rag.py --ask "your question here"
```

**Optional Arguments:**

- `--index`: Specifies an index name (default: **rag**).
- `--region`: Defines the AWS region (default: **us-east-1**).
- `--bedrock-model-id`: Selects a different foundation model.
- `--tenant-id`: Filters results by tenant ID.

### Cleanup

To remove all deployed resources, execute:

```bash
cd ./terraform
terraform destroy
```

Type "yes" when prompted to confirm resource deletion.
