# PDF Question-Answering API

This project is a FastAPI-based application that downloads PDFs, extracts text, builds a FAISS vector index using `sentence-transformers/all-MiniLM-L6-v2`, and answers questions using the Mistral model via Ollama. It processes various documents (e.g., Indian Constitution, insurance policies, technical manuals) and handles queries through a REST API endpoint.

## Features

-   **PDF Processing**: Downloads and extracts text from PDFs.
-   **Vector Indexing**: Uses FAISS and HuggingFace embeddings for efficient document retrieval.
-   **Question Answering**: Leverages the Mistral model (Ollama) to answer questions based on PDF content.
-   **Concurrency Control**: Limits parallel requests to prevent server overload.
-   **Retry Logic**: Handles transient failures (e.g., timeouts) with retries.

## Prerequisites

-   **OS**: Windows (tested), Linux, or macOS
-   **Python**: 3.10
-   **GPU**: NVIDIA GPU with CUDA for optimal performance (CPU fallback supported)
-   **Ollama**: Installed and running with the Mistral model
-   **Dependencies**: Listed in `requirements.txt`

## Setup Instructions

### Clone the Repository

```bash
git clone https://github.com/abhinai2244/LLM-Powered-Intelligent-Query-Retrieval-System.git
cd LLM-Powered-Intelligent-Query-Retrieval-System
```

### Set Up a Virtual Environment

```bash
python -m venv venv
# Windows
.\\venv\\Scripts\\activate
# Linux/macOS
# source venv/bin/activate
```

### Install Dependencies

```bash
pip install -r requirements.txt
```

Requirements include:

-   `fastapi`
-   `uvicorn`
-   `llama-index`
-   `llama-index-embeddings-huggingface`
-   `llama-index-llms-ollama`
-   `llama-index-vector-stores-faiss`
-   `requests`
-   `sentence-transformers`
-   `faiss-gpu` (or `faiss-cpu` if no GPU)
-   `tenacity`
-   `fastapi-concurrency`

### Install and Configure Ollama

1.  **Download and install Ollama**: [ollama.com](https://ollama.com)
2.  **Start the Ollama server**:

    ```bash
    ollama serve
    ```

3.  **Pull the Mistral model**:

    ```bash
    ollama pull mistral
    ```

4.  **Verify Ollama is running**:

    ```bash
    curl http://localhost:11434
    ```

### Verify GPU Support

1.  **Check GPU availability**:

    ```bash
    nvidia-smi
    ```

2.  **Ensure `faiss-gpu` is installed for GPU acceleration. Use `faiss-cpu` if no GPU is available**:

    ```bash
    pip install faiss-cpu
    ```

## Usage

### Run the FastAPI Server

```bash
cd <your-repo>
uvicorn new:app --host 127.0.0.1 --port 8000 --workers 1
```

The server will be available at `http://127.0.0.1:8000`.

### Send a Request

Use `curl`, Postman, or a script to send a `POST` request to `/api/v1/hackrx/run`. Example:

```bash
curl -X POST http://127.0.0.1:8000/api/v1/hackrx/run -H "Content-Type: application/json" -d '{
  "documents": "https://hackrx.blob.core.windows.net/assets/indian_constitution.pdf?sv=2023-01-03&st=2025-07-28T06%3A42%3A00Z&se=2026-11-29T06%3A42%3A00Z&sr=b&sp=r&sig=5Gs%2FOXqP3zY00lgciu4BZjDV5QjTDIx7fgnfdz6Pu24%3D",
  "questions": [
    "What is the official name of India according to Article 1 of the Constitution?",
    "What is abolished by Article 17 of the Constitution?"
  ]
}'
```

### Response:

```json
{
  "answers": [
    "The official name of India, according to Article 1 of the Constitution, is 'India, that is Bharat'.",
    "Article 17 of the Constitution abolishes 'untouchability' and forbids its practice in any form."
  ]
}
```

## Supported PDFs and Questions

The API has been tested with:

-   **Indian Constitution**: Legal questions (e.g., rights, articles).
-   **Arogya Sanjeevani Policy**: Insurance claims (e.g., root canal, IVF coverage).
-   **Super Splendor Manual**: Technical questions (e.g., spark plug gap).
-   **Family Medicare Policy**: Health insurance queries.
-   **Principia Newton**: Physics questions (e.g., laws of motion).
-   **UNI Group Health Insurance**: Coverage details.

Ensure the PDF URL matches the question context to avoid irrelevant answers.

## Troubleshooting

### Ollama Timeout (`httpx.ReadTimeout`):

-   **Increase timeout in `new.py`**:

    ```python
    Settings.llm = Ollama(model="mistral", request_timeout=1200.0)
    ```

-   **Restart Ollama**:

    ```bash
    # Windows
    taskkill /IM ollama.exe /F
    ollama serve
    ```

-   **Check GPU usage**:

    ```bash
    nvidia-smi
    ```

-   **Use a smaller model**:

    ```bash
    ollama run mistral:7b
    ```

### DNS Issues:

If pulling Mistral fails (no such host):

```bash
nslookup dd20bb891979d25aebc8bec07b2b3bbc.r2.cloudflarestorage.com
netsh interface ip set dns name="Wi-Fi" source=static address=8.8.8.8
ipconfig /flushdns
ollama pull mistral
```

### Concurrent Request Overload:

The server limits concurrency to 1 request. Ask clients to avoid parallel requests or increase `max_concurrent` in `fastapi-concurrency`.

### Port Conflict:

If port `11434` (Ollama) or `8000` (FastAPI) is in use:

```bash
netstat -aon | findstr :11434
taskkill /PID <PID> /F
```

### Dependency Issues:

Ensure compatible versions:

```bash
pip install langchain==0.2.16 langchain-community==0.2.16 pydantic==2.8.2
```

## Project Structure

```
<your-repo>/
├── new.py              # FastAPI application
├── requirements.txt    # Dependencies
├── docs/               # Directory for downloaded PDFs
├── index_<hash>/       # Cached FAISS indices
└── README.md           # This file
```

## Contributing

1.  Fork the repository.
2.  Create a feature branch (`git checkout -b feature/your-feature`).
3.  Commit changes (`git commit -m "Add your feature"`).
4.  Push to the branch (`git push origin feature/your-feature`).
5.  Open a pull request.

## License

MIT License. See `LICENSE` for details.

## Contact

For issues or questions, open a GitHub issue or contact `abhinaireddy2244@gmail.com`.
