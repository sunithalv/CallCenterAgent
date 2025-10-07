# 🤖 AI Call Center Agent for Electronics Company

This project implements a **Generative AI–powered Call Center Agent** designed for an electronics company that offers a range of products — from laptops to washing machines.  
The agent provides intelligent, context-aware customer support using **Retrieval-Augmented Generation (RAG)** and an **agentic architecture** that can retrieve, reason, and respond.

---

## 🧩 Overview

### 🎯 Objective
To create an AI agent capable of:
- Answering **product-specific customer questions**
- Handling **general inquiries** (availability, warranty, troubleshooting)
- **Routing** complex or unresolved queries to human support

---

## 🧱 System Architecture

Flow:
User Query → RAG Pipeline → Databases → Response
```


                   ┌─────────────────────────────┐
                   │         User Query          │
                   └──────────────┬──────────────┘
                                  │
                      Natural Language Understanding
                                  │
                     ┌────────────▼────────────┐
                     │     RAG Pipeline        │
                     ├─────────────────────────┤
                     │ 1. Retrieve context     │
                     │ 2. Generate response    │
                     │ 3. Route or escalate    │
                     └────────────┬────────────┘
                                  │
         ┌────────────────────────┴─────────────────────────┐
         │                                                  │
 ┌───────────────┐                                   ┌──────────────┐
 │   MongoDB     │                                   │   Qdrant     │
 │ Structured DB │                                   │ Vector Store │
 │ (Product info)│                                   │ (FAQs, docs) │
 └───────────────┘                                   └──────────────┘
```


## 🗄️ Data Architecture

The project uses **two data layers**:

| Layer | Purpose | Technology |
|-------|----------|-------------|
| **Product Metadata** | CRUD operations, structured lookup (price, specs, warranty) | **MongoDB** |
| **Semantic Knowledge Base** | Retrieve FAQs, descriptions, manuals using embeddings | **Qdrant (Vector DB)** |

---

## 🧠 RAG Flow

1. **User Query** → Example: “How long does the battery last on the UltraBook X15?”
2. **Retriever** (Qdrant) finds similar FAQ entries or descriptions.
3. **LLM** synthesizes an answer using retrieved context.
4. If query involves structured data (e.g., warranty or stock), agent fetches details from **MongoDB**.
5. Response is combined and returned to the user.

---

## 🧾 Data Format

### 1️⃣ Product Details (MongoDB Collection)
Each document represents one product.

```json
{
  "product_id": "P001",
  "category": "Laptop",
  "product_name": "ElectroMax UltraBook X15",
  "description": "A lightweight 15-inch laptop with Intel i7 processor and 16GB RAM.",
  "specifications": {
    "processor": "Intel Core i7-12700H",
    "ram": "16GB DDR5",
    "storage": "512GB SSD",
    "display": "15.6-inch FHD IPS",
    "battery": "70Wh up to 10 hours",
    "weight": "1.6 kg"
  },
  "price": "$1,099",
  "warranty": "2 years standard warranty",
  "availability": "In stock"
}
```

### 2️⃣ Product FAQs (Qdrant Collection)


Each entry represents one question-answer pair related to a product.
```json
{
  "id": "faq_P001_1",
  "vector": [0.12, 0.45, ...],
  "payload": {
    "product_id": "P001",
    "question": "How long does the battery last on the UltraBook X15?",
    "answer": "The battery lasts up to 10 hours on a full charge depending on usage."
  }
}
```

### ⚙️ Recommended Tech Stack
| Component   | Technology                                                     |
| ----------- | -------------------------------------------------------------- |
| LLM         | OpenAI `gpt-4o-mini` / `gpt-4o` or local Groq-compatible model |
| Embeddings  | OpenAI `text-embedding-3-large` or `fastembed`                 |
| Vector DB   | **Qdrant**                                                     |
| Metadata DB | **MongoDB**                                                    |
| Framework   | **LangChain** or **LlamaIndex**                                |
| Backend     | Python (FastAPI / Flask)                                       |
| Frontend    | Streamlit / Gradio for demo                                    |
| Deployment  | Docker + Render / AWS / GCP                                    |

### 🧰 Setup Guide
1. Clone the Repository

```bash
git clone https://github.com/<your-username>/ai-callcenter-agent.git
cd ai-callcenter-agent
```


2. Install Dependencies

```bash
pip install -r requirements.txt
```

3. Environment Variables:Create a .env file

```bash
OPENAI_API_KEY=<your_key>
QDRANT_URL=<your_qdrant_url>
MONGODB_URI=<your_mongo_uri>
```
4. Populate the Databases :
- Load product_details.json into MongoDB
```bash
mongoimport --db electronics --collection products --file data/product_details.json --jsonArray
```
- Load faqs.jsonl into Qdrant using a Python script that:

- Embeds each question/answer pair

- Inserts vector + payload into Qdrant

5. Run the Agent
```bash
python app.py
```
### 🧠 Agent Workflow

- User Query → "Is the SmartWash 7.5 fully automatic?"

- Retriever → Finds relevant FAQ entries in Qdrant.

- MongoDB Query → Fetch structured product details.

- LLM Synthesizer → Combines both into a natural reply.

- Response → Delivered to user (via API, chat, or voice UI).

### 🧩 Future Enhancements

🔧 Tool integrations — order tracking, repair booking, store locator

📈 Analytics dashboard — conversation insights, product feedback trends

🗣️ Voice interface — integrate real-time speech recognition via Whisper or Groq

🤝 Human-in-the-loop — escalate unresolved queries

### 📂 Project Structure
```
├── data/
│   ├── product_details.json         # Mongo-ready product data
│   ├── faqs.jsonl                   # Qdrant-ready FAQs with metadata
├── src/
│   ├── db/                          # MongoDB + Qdrant setup scripts
│   ├── retriever/                   # RAG retrieval and embeddings
│   ├── agent/                       # LLM orchestration + tool use
│   └── app.py                       # Main entrypoint / API
├── .env.example
├── requirements.txt
└── README.md
```
### 🧩 License

This project is released under the Apache 2.0 License
.

### 👩‍💻 Author

Sunitha L. V

