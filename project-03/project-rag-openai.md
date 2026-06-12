# ⚛️🤖 Complete OpenAI RAG System Tutorial

## Build a Pure RAG Application Using React + FastAPI + ChromaDB + OpenAI API

## 🎯 Goal

Build a web-based **Pure RAG System** with:

```text
React Frontend
FastAPI Backend
ChromaDB Vector Database
OpenAI Embeddings
OpenAI Chat Model
Local Markdown Documents
```

---

# 🧠 What You Will Build

You will build a web application where:

```text
User asks a question in React UI
        ↓
React sends request to FastAPI
        ↓
FastAPI creates an embedding using OpenAI
        ↓
FastAPI searches ChromaDB
        ↓
FastAPI retrieves relevant document chunks
        ↓
FastAPI sends chunks to OpenAI chat model
        ↓
OpenAI generates an answer
        ↓
React displays the answer
```

---

# 🧩 Final Architecture

```text
┌──────────────────────────────┐
│ React Frontend               │
│ http://localhost:5173        │
└──────────────┬───────────────┘
               │ POST /ask
               ▼
┌──────────────────────────────┐
│ FastAPI Backend              │
│ http://localhost:8000        │
└──────────────┬───────────────┘
               │
       ┌───────┴────────┐
       ▼                ▼
┌───────────────┐  ┌────────────────────┐
│ ChromaDB      │  │ OpenAI API          │
│ Vector Search │  │ Embeddings + LLM    │
└───────────────┘  └────────────────────┘
       ▲
       │
┌───────────────┐
│ Markdown Docs │
└───────────────┘
```

---

# 📁 Final Project Structure

```text
openai-rag-system/

├── backend/
│   ├── app.py
│   ├── ingest.py
│   ├── requirements.txt
│   ├── .env
│   ├── chroma_db/
│   ├── docs/
│   │   ├── deployment-guide.md
│   │   ├── rdp-ssm-guide.md
│   │   └── service-onboarding.md
│   └── .venv/
│
└── frontend/
    ├── package.json
    ├── index.html
    ├── vite.config.js
    ├── node_modules/
    └── src/
        ├── App.jsx
        ├── App.css
        ├── main.jsx
        └── index.css
```

---

# 1️⃣ Create an OpenAI API Key

Go to:

```text
https://platform.openai.com/api-keys
```

Create a new API key.

Do **not** hardcode the API key in your source code.

Recommended environment variable name:

```text
OPENAI_API_KEY
```

---

# 2️⃣ Create Main Project Folder

```bash
mkdir openai-rag-system
cd openai-rag-system
```

Create backend and frontend folders:

```bash
mkdir backend
mkdir frontend
```

---

# 3️⃣ Create Backend Project

Go to backend folder:

```bash
cd backend
```

Create Python virtual environment:

```bash
python3 -m venv .venv
```

Activate it:

```bash
source .venv/bin/activate
```

Create `requirements.txt`:

```bash
touch requirements.txt
```

Add:

```txt
fastapi
uvicorn
chromadb
openai
python-dotenv
```

Install dependencies:

```bash
pip install -r requirements.txt
```

---

# 4️⃣ Configure OpenAI API Key

Create `.env` file:

```bash
touch .env
```

Add your API key:

```text
OPENAI_API_KEY=your_openai_api_key_here
```

Important:

```text
Do not commit .env to Git.
```

Create `.gitignore`:

```bash
touch .gitignore
```

Add:

```text
.venv/
.env
chroma_db/
__pycache__/
```

---

# 5️⃣ Create Knowledge Documents

Create docs folder:

```bash
mkdir docs
```

## Create `docs/deployment-guide.md`

```md
# Deployment Guide

To deploy FPAC-IDP to non-prod:

1. Merge feature branch into main.
2. Jenkins builds and pushes the Docker image to ECR.
3. Update the nonprod manifest in the GitOps repo.
4. Argo CD automatically syncs the non-prod application.

Production deployment requires manual approval.
```

## Create `docs/rdp-ssm-guide.md`

```md
# RDP Using SSM

To RDP into an AWS Windows EC2 instance without a bastion:

1. Install AWS CLI.
2. Install AWS Session Manager Plugin.
3. Authenticate using aws-cli-connect.ps1.
4. Start SSM port forwarding from local port 55678 to remote port 3389.
5. Open Remote Desktop and connect to localhost:55678.
```

## Create `docs/service-onboarding.md`

```md
# Service Onboarding

To onboard a new service:

1. Create a new repository.
2. Add catalog-info.yaml.
3. Add TechDocs documentation.
4. Add Jenkins pipeline configuration.
5. Add Kubernetes manifests or Helm chart.
6. Register the service in Backstage Software Catalog.
```

---

# 6️⃣ Create `ingest.py`

This script reads markdown files, splits them into chunks, creates embeddings using OpenAI, and stores them in ChromaDB.

Create:

```bash
touch ingest.py
```

Add:

```python
from pathlib import Path

import chromadb
from dotenv import load_dotenv
from openai import OpenAI


load_dotenv()

DOCS_DIR = Path("docs")
CHROMA_DIR = "chroma_db"
EMBED_MODEL = "text-embedding-3-small"

client = OpenAI()

chroma_client = chromadb.PersistentClient(path=CHROMA_DIR)

collection = chroma_client.get_or_create_collection(
    name="company_docs"
)


def chunk_text(text, chunk_size=800, overlap=100):
    chunks = []
    start = 0

    while start < len(text):
        end = start + chunk_size
        chunks.append(text[start:end])
        start = end - overlap

    return chunks


def create_embedding(text):
    response = client.embeddings.create(
        model=EMBED_MODEL,
        input=text
    )

    return response.data[0].embedding


def ingest_docs():
    for file_path in DOCS_DIR.glob("*.md"):
        content = file_path.read_text(encoding="utf-8")
        chunks = chunk_text(content)

        for index, chunk in enumerate(chunks):
            chunk_id = f"{file_path.name}-{index}"

            embedding = create_embedding(chunk)

            collection.upsert(
                ids=[chunk_id],
                embeddings=[embedding],
                documents=[chunk],
                metadatas=[
                    {
                        "source": file_path.name,
                        "chunk": index
                    }
                ]
            )

            print(f"Indexed {file_path.name} chunk {index}")


if __name__ == "__main__":
    ingest_docs()
    print("Ingestion complete.")
```

---

# 7️⃣ Run Document Ingestion

From `backend/`:

```bash
source .venv/bin/activate
python ingest.py
```

Expected output:

```text
Indexed deployment-guide.md chunk 0
Indexed rdp-ssm-guide.md chunk 0
Indexed service-onboarding.md chunk 0
Ingestion complete.
```

This creates:

```text
backend/chroma_db/
```

That is your local vector database.

---

# 8️⃣ Create FastAPI Backend

Create:

```bash
touch app.py
```

Add:

```python
import chromadb

from dotenv import load_dotenv
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from openai import OpenAI
from pydantic import BaseModel


load_dotenv()

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=[
        "http://localhost:5173",
        "http://127.0.0.1:5173"
    ],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)


EMBED_MODEL = "text-embedding-3-small"
CHAT_MODEL = "gpt-4.1-mini"

client = OpenAI()

chroma_client = chromadb.PersistentClient(path="chroma_db")

collection = chroma_client.get_or_create_collection(
    name="company_docs"
)


class AskRequest(BaseModel):
    question: str


def create_embedding(text):
    response = client.embeddings.create(
        model=EMBED_MODEL,
        input=text
    )

    return response.data[0].embedding


def retrieve_chunks(question, top_k=3):
    question_embedding = create_embedding(question)

    results = collection.query(
        query_embeddings=[question_embedding],
        n_results=top_k
    )

    chunks = []

    documents = results.get("documents", [[]])[0]
    metadatas = results.get("metadatas", [[]])[0]
    distances = results.get("distances", [[]])[0]

    for i in range(len(documents)):
        chunks.append(
            {
                "content": documents[i],
                "metadata": metadatas[i],
                "distance": distances[i]
            }
        )

    return chunks


def generate_answer(question, chunks):
    context = "\n\n---\n\n".join(
        [
            f"Source: {chunk['metadata']['source']}\n\n{chunk['content']}"
            for chunk in chunks
        ]
    )

    prompt = f"""
You are a pure RAG assistant.

Answer the question using ONLY the context below.

If the answer is not in the context, say:
"I do not know based on the provided documents."

Context:
{context}

Question:
{question}
"""

    response = client.chat.completions.create(
        model=CHAT_MODEL,
        messages=[
            {
                "role": "system",
                "content": "You are a pure RAG assistant. Only answer from retrieved context."
            },
            {
                "role": "user",
                "content": prompt
            }
        ],
        temperature=0
    )

    return response.choices[0].message.content


@app.get("/")
def root():
    return {
        "message": "OpenAI RAG system with React, FastAPI, and ChromaDB is running"
    }


@app.post("/ask")
def ask(request: AskRequest):
    print("Received question:", request.question)

    chunks = retrieve_chunks(request.question)

    answer = generate_answer(request.question, chunks)

    return {
        "question": request.question,
        "retrieved_chunks": chunks,
        "answer": answer
    }
```

---

# 9️⃣ Start FastAPI Backend

From `backend/`:

```bash
source .venv/bin/activate
uvicorn app:app --reload
```

Backend runs at:

```text
http://localhost:8000
```

Test:

```text
http://localhost:8000
```

Expected response:

```json
{
  "message": "OpenAI RAG system with React, FastAPI, and ChromaDB is running"
}
```

API docs:

```text
http://localhost:8000/docs
```

---

# 🔟 Test Backend Directly

Open:

```text
http://localhost:8000/docs
```

Use:

```text
POST /ask
```

Request body:

```json
{
  "question": "How do I RDP into AWS without a bastion?"
}
```

Expected answer should come from:

```text
rdp-ssm-guide.md
```

---

# 1️⃣1️⃣ Create React Frontend

Open a new terminal.

Go to project root:

```bash
cd openai-rag-system
```

Create React app inside `frontend/` using Vite:

```bash
npm create vite@latest frontend -- --template react
```

Go to frontend:

```bash
cd frontend
```

Install dependencies:

```bash
npm install
```

Run test:

```bash
npm run dev
```

React runs at:

```text
http://localhost:5173
```

Stop it for now:

```text
Ctrl + C
```

---

# 1️⃣2️⃣ Create React UI

Open:

```text
frontend/src/App.jsx
```

Replace everything with:

```jsx
import { useState } from "react";
import "./App.css";

function App() {
  const [question, setQuestion] = useState(
    "How do I RDP into AWS without a bastion?"
  );
  const [answer, setAnswer] = useState("");
  const [retrievedChunks, setRetrievedChunks] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState("");

  async function askQuestion() {
    setLoading(true);
    setError("");
    setAnswer("");
    setRetrievedChunks([]);

    try {
      const response = await fetch("http://localhost:8000/ask", {
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          question: question
        })
      });

      if (!response.ok) {
        throw new Error(`Backend error: ${response.status}`);
      }

      const data = await response.json();

      setAnswer(data.answer);
      setRetrievedChunks(data.retrieved_chunks || []);
    } catch (err) {
      setError(err.message || "Something went wrong");
    } finally {
      setLoading(false);
    }
  }

  return (
    <div className="page">
      <div className="container">
        <h1>🤖 OpenAI RAG Demo</h1>

        <p className="subtitle">
          Ask a question. React sends it to FastAPI. FastAPI retrieves
          relevant chunks from ChromaDB and asks OpenAI to answer using
          only those chunks.
        </p>

        <div className="question-box">
          <label htmlFor="question">
            Enter your question:
          </label>

          <textarea
            id="question"
            value={question}
            onChange={(event) => setQuestion(event.target.value)}
            rows={4}
            placeholder="Ask something from your local documents..."
          />

          <button
            onClick={askQuestion}
            disabled={loading || question.trim() === ""}
          >
            {loading ? "Thinking..." : "Ask RAG System"}
          </button>
        </div>

        {error && (
          <div className="error">
            <strong>Error:</strong> {error}
          </div>
        )}

        {answer && (
          <div className="answer-card">
            <h2>✅ Answer</h2>
            <p>{answer}</p>
          </div>
        )}

        {retrievedChunks.length > 0 && (
          <div className="chunks-card">
            <h2>📚 Retrieved Sources</h2>

            {retrievedChunks.map((chunk, index) => (
              <div className="chunk" key={index}>
                <h3>
                  Source: {chunk.metadata?.source}
                </h3>

                <p className="distance">
                  Distance: {chunk.distance}
                </p>

                <pre>{chunk.content}</pre>
              </div>
            ))}
          </div>
        )}
      </div>
    </div>
  );
}

export default App;
```

---

# 1️⃣3️⃣ Add React Styling

Open:

```text
frontend/src/App.css
```

Replace everything with:

```css
.page {
  min-height: 100vh;
  background: #f4f6f8;
  padding: 40px;
  font-family: Arial, sans-serif;
}

.container {
  max-width: 900px;
  margin: 0 auto;
  background: white;
  padding: 32px;
  border-radius: 12px;
  box-shadow: 0 4px 18px rgba(0, 0, 0, 0.08);
}

h1 {
  margin-top: 0;
  color: #222;
}

.subtitle {
  color: #555;
  line-height: 1.6;
}

.question-box {
  margin-top: 24px;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

label {
  font-weight: bold;
}

textarea {
  padding: 12px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 8px;
  resize: vertical;
}

button {
  width: 180px;
  padding: 12px;
  border: none;
  border-radius: 8px;
  background: #2563eb;
  color: white;
  font-weight: bold;
  cursor: pointer;
}

button:disabled {
  background: #999;
  cursor: not-allowed;
}

.answer-card,
.chunks-card {
  margin-top: 24px;
  padding: 20px;
  border-radius: 10px;
  background: #f9fafb;
  border: 1px solid #e5e7eb;
}

.answer-card p {
  white-space: pre-wrap;
  line-height: 1.6;
}

.error {
  margin-top: 24px;
  padding: 16px;
  background: #fee2e2;
  color: #991b1b;
  border-radius: 8px;
}

.chunk {
  margin-top: 16px;
  padding: 16px;
  background: white;
  border: 1px solid #ddd;
  border-radius: 8px;
}

.chunk h3 {
  margin-top: 0;
}

.distance {
  color: #666;
  font-size: 14px;
}

pre {
  white-space: pre-wrap;
  word-break: break-word;
  background: #111827;
  color: #f9fafb;
  padding: 16px;
  border-radius: 8px;
  overflow-x: auto;
}
```

---

# 1️⃣4️⃣ Check `main.jsx`

Open:

```text
frontend/src/main.jsx
```

It should look like:

```jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import "./index.css";
import App from "./App.jsx";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

---

# 1️⃣5️⃣ Run the Complete System

You need two terminals.

## Terminal 1 - Backend

```bash
cd openai-rag-system/backend
source .venv/bin/activate
uvicorn app:app --reload
```

Backend:

```text
http://localhost:8000
```

## Terminal 2 - Frontend

```bash
cd openai-rag-system/frontend
npm run dev
```

Frontend:

```text
http://localhost:5173
```

---

# 1️⃣6️⃣ Test the React UI

Open browser:

```text
http://localhost:5173
```

Ask:

```text
How do I RDP into AWS without a bastion?
```

Click:

```text
Ask RAG System
```

Expected result:

```text
The answer should mention AWS CLI, Session Manager Plugin,
aws-cli-connect.ps1, port forwarding 55678 to 3389,
and Remote Desktop localhost:55678.
```

---

# 1️⃣7️⃣ Debug the Frontend Request

Open browser DevTools:

```text
F12
```

Go to:

```text
Network
```

Click:

```text
Ask RAG System
```

You should see:

```text
POST http://localhost:8000/ask
```

Check:

```text
Status Code
Request Payload
Response Body
```

---

# 1️⃣8️⃣ Debug the Backend Request

Backend terminal should show:

```text
Received question: How do I RDP into AWS without a bastion?
```

If not, React is not reaching FastAPI.

---

# 1️⃣9️⃣ When to Re-run Ingestion

Run ingestion when:

```text
You add new markdown files
You update document content
You delete and rebuild ChromaDB
```

Command:

```bash
cd backend
source .venv/bin/activate
python ingest.py
```

---

# 2️⃣0️⃣ Common Issues

## API Key Missing

Symptom:

```text
The api_key client option must be set
```

Fix:

Check `.env`:

```text
OPENAI_API_KEY=your_openai_api_key_here
```

Restart backend:

```bash
uvicorn app:app --reload
```

## CORS Error

Symptom:

```text
blocked by CORS policy
```

Fix:

Make sure `app.py` contains:

```python
app.add_middleware(
    CORSMiddleware,
    allow_origins=[
        "http://localhost:5173",
        "http://127.0.0.1:5173"
    ],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

## Backend Not Running

Symptom:

```text
Failed to fetch
```

Fix:

```bash
cd backend
source .venv/bin/activate
uvicorn app:app --reload
```

## ChromaDB Has No Data

Symptom:

```text
Retrieved sources are empty
Answer is poor
```

Fix:

```bash
cd backend
source .venv/bin/activate
python ingest.py
```

## OpenAI Rate Limit or Billing Error

Possible symptoms:

```text
rate_limit_exceeded
insufficient_quota
billing issue
```

Fix:

```text
Check OpenAI account billing
Check usage limits
Try again later
Use a smaller model if needed
```

---

# 2️⃣1️⃣ What Makes This Pure RAG?

This system only does:

```text
Question
  ↓
Search local documents
  ↓
Retrieve relevant chunks
  ↓
Generate answer from retrieved chunks
```

It does not call:

```text
Jenkins
AWS
Argo CD
Bitbucket
Backstage Catalog
```

It does not perform actions like:

```text
Create PR
Trigger pipeline
Deploy application
Provision infrastructure
```

So this is:

```text
Pure RAG
```

not an AI Assistant or AI Agent.

---

# 2️⃣2️⃣ Summary

You built:

```text
⚛️ React Frontend
⚡ FastAPI Backend
📚 ChromaDB Vector Database
🤖 OpenAI Embeddings
🧠 OpenAI Chat Model
📄 Local Markdown Knowledge Base
```

Final flow:

```text
Markdown Docs
   ↓
ingest.py
   ↓
ChromaDB
   ↓
React Question
   ↓
FastAPI /ask
   ↓
OpenAI Embedding
   ↓
ChromaDB Search
   ↓
Relevant Chunks
   ↓
OpenAI Chat Model
   ↓
Answer
   ↓
React UI
```

This is a working OpenAI-based pure RAG application.

