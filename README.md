# MediNova AI - Medical RAG Chatbot

MediNova AI is a premium medical question-answering chatbot built with Python and LangChain. It reads a medical knowledge base stored in PDF documents, converts that information into searchable vector embeddings, and answers user questions using a retrieval-augmented generation (RAG) workflow.

The system is designed to provide answers grounded in the source documents rather than generating free-form medical advice without context. It is ideal for exploring medical reference material in a conversational interface.

## What the project does

This project creates a chatbot that can:

- Load one or more PDF medical documents
- Split the content into manageable chunks
- Convert text into embeddings using a sentence-transformer model
- Store those embeddings in a FAISS vector database
- Search the most relevant passages for a user query
- Send the retrieved context to an LLM for answer generation
- Display the response in a Streamlit chat interface

In short, the project turns static medical PDFs into an interactive, searchable medical knowledge assistant.

## Main features

- PDF-based knowledge ingestion from the data folder
- Chunking and text segmentation for better retrieval
- Semantic search using FAISS and sentence-transformers
- RAG pipeline using LangChain
- LLM-powered responses from Groq-hosted models
- Streamlit-based chat UI for conversations
- Source document references included in the response for traceability
- Context-grounded prompting to reduce hallucinations
- Persistent vector store so the index can be reused without rebuilding

## Project structure

```text
medical-chatbot-main/
├── data/
│   └── The_GALE_ENCYCLOPEDIA_of_MEDICINE_SECOND.pdf
├── vectorstore/
│   └── db_faiss/
├── medinova.py
├── create_memory_for_llm.py
├── connect_memory_with_llm.py
├── requirements.txt
├── Pipfile
├── Pipfile.lock
├── medical-chatbot-ppt.pdf
└── README.md
```

### File descriptions

- `create_memory_for_llm.py`
  - Builds the knowledge base.
  - Loads PDFs from the data folder.
  - Splits documents into chunks.
  - Uses HuggingFace embeddings to create vector embeddings.
  - Saves the resulting FAISS index to `vectorstore/db_faiss`.

- `connect_memory_with_llm.py`
  - Connects the saved FAISS vector store to a language model.
  - Loads the vector database and creates a RetrievalQA chain.
  - Accepts a user query through the terminal and prints the answer with source docs.
  - Useful as a command-line prototype before the Streamlit app.

- `medinova.py`
  - Main application file.
  - Runs the Streamlit chat interface.
  - Loads the FAISS index and runs the retrieval-based question answering flow.
  - Uses Groq for the LLM backend and shows the answer plus source references.

- `data/`
  - Stores the source PDF(s) from which the medical knowledge base is created.

- `vectorstore/db_faiss/`
  - Persisted vector index generated from the medical documents.

## Technology stack

This project uses a modern Python AI stack:

- Python 3.12
- Streamlit for the web UI
- LangChain for orchestration and RAG chaining
- FAISS for vector similarity search
- Hugging Face embeddings via `sentence-transformers/all-MiniLM-L6-v2`
- Groq LLM integration with `langchain-groq`
- PyPDF for PDF ingestion
- Hugging Face Hub and model endpoints for embeddings / LLM access

## Workflow of the project

### 1. Knowledge ingestion
The project starts by scanning the PDF files in `data/` using `PyPDFLoader` and `DirectoryLoader`.

### 2. Document chunking
The loaded text is split into chunks using `RecursiveCharacterTextSplitter`.

This step is important because large documents need to be broken down into smaller, retrievable pieces for more accurate semantic search.

### 3. Embedding creation
Each chunk is converted into vector embeddings using the sentence-transformers model:

- `sentence-transformers/all-MiniLM-L6-v2`

These embeddings capture the semantic meaning of each chunk.

### 4. Vector database creation
The generated embeddings are stored in a FAISS vector store at:

- `vectorstore/db_faiss`

This allows fast similarity search over the medical knowledge base.

### 5. Retrieval and answer generation
When the user asks a question:

- the query is embedded
- FAISS finds the most relevant document chunks
- those chunks are passed as context to the LLM
- the LLM answers using only the retrieved context

The result is a grounded answer instead of a random hallucinated response.

### 6. Front-end interaction
The Streamlit app (`medinova.py`) creates a conversational interface where users can type medical questions and receive answers in chat format.

## How the chatbot answers questions

The application uses a RetrievalQA chain with a custom prompt template:

- it instructs the model to rely only on the provided context
- it refuses to invent information when the answer is missing
- it starts the answer directly without small talk
- it includes source documents for transparency

This design helps maintain a more factual and source-based response style.

## Setup instructions

### Option 1: Use pip

1. Create a virtual environment

```bash
python -m venv venv
source venv/bin/activate   # Linux/macOS
venv\Scripts\activate      # Windows
```

2. Install dependencies

```bash
pip install -r requirements.txt
```

### Option 2: Use Pipenv

```bash
pipenv install
pipenv shell
```

## Required environment variables

This project expects a Groq API key:

```bash
GROQ_API_KEY=your_groq_api_key_here
```

If you use Hugging Face endpoints directly, you may also need:

```bash
HF_TOKEN=your_hf_token_here
```

You can store these in a `.env` file and load them as needed.

## Run the app

Start the Streamlit application:

```bash
streamlit run medinova.py
```

The app will open in the browser and allow you to ask medical questions based on the stored documents.

## Example usage

Example prompts:

- What are the symptoms of a common disease?
- Explain the causes of a condition from the medical encyclopedia.
- What does the source document say about treatment options?

## Important note

This project is a medical information assistant based on a provided document set. It is useful for reference and learning, but it should not be treated as a substitute for a licensed medical professional or clinical diagnosis.

## Summary

MediNova AI is a retrieval-based medical chatbot that combines:

- medical PDFs as a knowledge source
- embeddings and FAISS for semantic retrieval
- LangChain for orchestration
- Groq-hosted LLM for answer generation
- Streamlit for a user-friendly interactive interface

It demonstrates a practical RAG architecture for building an AI-powered medical assistant grounded in source documents.

