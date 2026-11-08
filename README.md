# PDF RAG Chatbot

A Streamlit application for asking questions about an uploaded PDF. It uses page-aware text extraction, token-based overlapping chunks, Sentence Transformers embeddings, FAISS similarity search and a Groq model to generate answers from retrieved document context.

**Live demo:** https://rag-based-ai-app-hu46rmwwh8fprdkzxcncvu.streamlit.app/

## Features

- Upload a PDF document.
- Extract text page by page.
- Split page text into overlapping token-based chunks while retaining page numbers.
- Generate normalized embeddings with sentence-transformers/all-MiniLM-L6-v2.
- Store vectors in a FAISS inner-product index.
- Retrieve the top five relevant chunks for each question.
- Generate an answer with Groq using the retrieved text as context.
- Display retrieved source excerpts with page numbers and similarity scores.
- Preserve the current index, document details and chat messages in Streamlit session state.

## Retrieval workflow

~~~text
PDF upload
   |
   v
pypdf: page-by-page text extraction
   |
   v
Tokenizer-based chunking
350 tokens / 60-token overlap
   |
   v
all-MiniLM-L6-v2 embeddings
(normalized vectors)
   |
   v
FAISS IndexFlatIP
   |
   v
Question embedding
   |
   v
Top-5 similarity retrieval
   |
   v
Retrieved chunks + page metadata
   |
   v
Groq: openai/gpt-oss-120b
   |
   v
Context-grounded answer + source excerpts
~~~

Because both document and question embeddings are normalized, FAISS inner-product ranking is used as cosine-similarity ranking.

## Technology stack

| Layer | Technology |
|---|---|
| UI | Streamlit |
| PDF extraction | pypdf |
| Chunk tokenization | Tokenizer provided by the Sentence Transformers model |
| Embeddings | Sentence Transformers, all-MiniLM-L6-v2 |
| Vector index | FAISS IndexFlatIP |
| LLM | Groq API, openai/gpt-oss-120b |
| Data/session handling | NumPy, Streamlit session state |

## Project structure

~~~text
PDF-RAG-Chatbot/
├── app.py              # Streamlit UI and RAG pipeline
├── requirements.txt    # Python dependencies
├── LICENSE
└── README.md
~~~

## Run locally

### 1. Clone the repository

~~~bash
git clone https://github.com/Qaisar-baloch/PDF-RAG-Chatbot.git
cd PDF-RAG-Chatbot
~~~

### 2. Create an environment and install dependencies

~~~bash
python -m venv .venv
~~~

**Windows**

~~~powershell
.venv\Scripts\activate
~~~

**macOS / Linux**

~~~bash
source .venv/bin/activate
~~~

Install packages:

~~~bash
pip install -r requirements.txt
~~~

### 3. Configure the Groq API key

Create .streamlit/secrets.toml:

~~~toml
GROQ_API_KEY = "your_groq_api_key"
~~~

For Streamlit Community Cloud, configure the same key in the app's Secrets settings. Do not commit secrets or API keys to GitHub.

### 4. Launch

~~~bash
streamlit run app.py
~~~

Open the local URL printed by Streamlit, usually http://localhost:8501.

## How to use

1. Upload a text-based PDF.
2. Wait for extraction, chunking and vector-index creation.
3. Ask a question about the document.
4. Review the generated answer.
5. Expand **Retrieved Sources** to inspect the supporting excerpts and PDF pages.

## Limitations

- The application extracts selectable PDF text. Scanned/image-only PDFs may require OCR before upload.
- The vector index and chat state are stored in the active Streamlit session; persistent document storage and cross-session chat history are not implemented.
- Answers can only be as useful as the extracted text and retrieved chunks. Verify important information against the original document.
- The model may still produce an incorrect or incomplete answer. The application is a learning/demo project, not a guaranteed document-verification system.
- A Groq API key and access to the configured model are required.

## Author

**Qaisar Khan** · [GitHub profile](https://github.com/Qaisar-baloch)
