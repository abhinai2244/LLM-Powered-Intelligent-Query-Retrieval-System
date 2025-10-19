PDF Question-Answering API
This project is a FastAPI-based application that downloads PDFs, extracts text, builds a FAISS vector index using sentence-transformers/all-MiniLM-L6-v2, and answers questions using the Mistral model via Ollama. It processes various documents (e.g., Indian Constitution, insurance policies, technical manuals) and handles queries through a REST API endpoint.
Features

PDF Processing: Downloads and extracts text from PDFs.
Vector Indexing: Uses FAISS and HuggingFace embeddings for efficient document retrieval.
Question Answering: Leverages the Mistral model (Ollama) to answer questions based on PDF content.
Concurrency Control: Limits parallel requests to prevent server overload.
Retry Logic: Handles transient failures (e.g., timeouts) with retries.

Prerequisites

OS: Windows (tested), Linux, or macOS
Python: 3.10
GPU: NVIDIA GPU with CUDA for optimal performance (CPU fallback supported)
Ollama: Installed and running with the Mistral model
Dependencies: Listed in requirements.txt

Setup Instructions

Clone the Repository
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>


Set Up a Virtual Environment
python -m venv venv
.\\venv\\Scripts\\activate  # Windows
# source venv/bin/activate  # Linux/macOS


Install Dependencies
pip install -r requirements.txt

Requirements include:

fastapi
uvicorn
llama-index
llama-index-embeddings-huggingface
llama-index-llms-ollama
llama-index-vector-stores-faiss
requests
sentence-transformers
faiss-gpu (or faiss-cpu if no GPU)
tenacity
fastapi-concurrency


Install and Configure Ollama

Download and install Ollama: ollama.com
Start the Ollama server:ollama serve


Pull the Mistral model:ollama pull mistral


Verify Ollama is running:curl http://localhost:11434




Verify GPU Support

Check GPU availability:nvidia-smi


Ensure faiss-gpu is installed for GPU acceleration. Use faiss-cpu if no GPU is available:pip install faiss-cpu





Usage

Run the FastAPI Server
cd <your-repo>
uvicorn new:app --host 127.0.0.1 --port 8000 --workers 1

The server will be available at http://127.0.0.1:8000.

Send a RequestUse curl, Postman, or a script to send a POST request to /api/v1/hackrx/run. Example:
curl -X POST http://127.0.0.1:8000/api/v1/hackrx/run -H "Content-Type: application/json" -d '{
  "documents": "https://hackrx.blob.core.windows.net/assets/indian_constitution.pdf?sv=2023-01-03&st=2025-07-28T06%3A42%3A00Z&se=2026-11-29T06%3A42%3A00Z&sr=b&sp=r&sig=5Gs%2FOXqP3zY00lgciu4BZjDV5QjTDIx7fgnfdz6Pu24%3D",
  "questions": [
    "What is the official name of India according to Article 1 of the Constitution?",
    "What is abolished by Article 17 of the Constitution?"
  ]
}'

Response:
{
  "answers": [
    "The official name of India, according to Article 1 of the Constitution, is 'India, that is Bharat'.",
    "Article 17 of the Constitution abolishes 'untouchability' and forbids its practice in any form."
  ]
}


Supported PDFs and QuestionsThe API has been tested with:

Indian Constitution: Legal questions (e.g., rights, articles).
Arogya Sanjeevani Policy: Insurance claims (e.g., root canal, IVF coverage).
Super Splendor Manual: Technical questions (e.g., spark plug gap).
Family Medicare Policy: Health insurance queries.
Principia Newton: Physics questions (e.g., laws of motion).
UNI Group Health Insurance: Coverage details.

Ensure the PDF URL matches the question context to avoid irrelevant answers.


Troubleshooting

Ollama Timeout (httpx.ReadTimeout):

Increase timeout in new.py:Settings.llm = Ollama(model="mistral", request_timeout=1200.0)


Restart Ollama:taskkill /IM ollama.exe /F  # Windows
ollama serve


Check GPU usage:nvidia-smi


Use a smaller model:ollama run mistral:7b




DNS Issues:

If pulling Mistral fails (no such host):nslookup dd20bb891979d25aebc8bec07b2b3bbc.r2.cloudflarestorage.com
netsh interface ip set dns name="Wi-Fi" source=static address=8.8.8.8
ipconfig /flushdns
ollama pull mistral




Concurrent Request Overload:

The server limits concurrency to 1 request. Ask clients to avoid parallel requests or increase max_concurrent in fastapi-concurrency.


Port Conflict:

If port 11434 (Ollama) or 8000 (FastAPI) is in use:netstat -aon | findstr :11434
taskkill /PID <PID> /F




Dependency Issues:

Ensure compatible versions:pip install langchain==0.2.16 langchain-community==0.2.16 pydantic==2.8.2





Project Structure
<your-repo>/
├── new.py              # FastAPI application
├── requirements.txt    # Dependencies
├── docs/               # Directory for downloaded PDFs
├── index_<hash>/       # Cached FAISS indices
└── README.md           # This file

Contributing

Fork the repository.
Create a feature branch (git checkout -b feature/your-feature).
Commit changes (git commit -m "Add your feature").
Push to the branch (git push origin feature/your-feature).
Open a pull request.

License
MIT License. See LICENSE for details.
Contact
For issues or questions, open a GitHub issue or contact <your-email>.
