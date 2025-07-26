# Multilingual-RAG-System
Setup Guide:
1.  Install required OCR & NLP tools in Colab or Linux:
!apt-get install -y tesseract-ocr tesseract-ocr-beng
!pip install pytesseract pillow langchain langchain-community chromadb sentence-transformers PyMuPDF transformers
2. Place the PDF file in the working directory
3. Run the script that will:

OCR the PDF.

Split text into chunks.

Create a vector database (Chroma).

Answer your queries using a Retrieval-Augmented Generation (RAG) pipeline.
Used Tools:
PyMuPDF (fitz) – To read PDF pages and render images for OCR.

Tesseract OCR (pytesseract) – To extract Bangla text from image-based or poorly encoded PDFs.

Pillow (PIL) – To handle PDF page images before OCR.

LangChain – To manage text splitting, embedding, retrieval, and RAG flow.

ChromaDB – Lightweight vector database for semantic search.

Sentence-Transformers – Used the multilingual MiniLM model for Bangla-friendly semantic embeddings.

Transformers (HuggingFace) – Used google/mt5-large for Bangla and English Q&A.

Python Standard Library – os, re for file handling and text cleanup.
Questions:
1. What method or library did you use to extract the text, and why? Did you face any formatting challenges with the PDF content?
➡️ We used Tesseract OCR with lang='ben' because PyMuPDF was returning broken Bangla glyphs (e.g., িাশুর়্েি). OCR ensures clean, readable Bangla text even if the PDF text layer is corrupted.

Formatting challenge: OCR can sometimes misrecognize letters (esp. rare ligatures). Basic regex cleanup was used to remove extra spaces & noise.

2. What chunking strategy did you choose (e.g. paragraph-based, sentence-based, character limit)? Why does it work well for semantic retrieval?
➡️ We used character-limit chunking:

chunk_size = 400, overlap = 50.

Works well because:

Small enough to maintain semantic meaning.

Overlap prevents cutting sentences awkwardly.

Good for multilingual embeddings (contextually coherent chunks).

3. What embedding model did you use? Why did you choose it? How does it capture the meaning of the text?
➡️ Used sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2.

Chosen because it supports Bangla + English.

Encodes chunks & queries into a shared vector space so semantically similar items are near each other — meaning “সুপুরুষ” and “handsome man” will still relate.

4. How are you comparing the query with your stored chunks? Why did you choose this similarity method and storage setup?
➡️ Stored all embeddings in ChromaDB.

When a query comes in, we embed the query and do a vector similarity search (cosine similarity) to find the closest chunks.

Chroma is lightweight, persistent, and perfect for fast semantic search.

 5.How do you ensure that the question and the document chunks are compared meaningfully? What would happen if the query is vague or missing context?
➡️ Embeddings align questions and chunks semantically.
➡️ If a query is vague:

The retriever might return irrelevant chunks.

The LLM might “hallucinate” (make up answers).

Solution: add follow-up clarification prompts or expand retriever context (e.g., k=5).

6. Do the results seem relevant? If not, what might improve them?
 Now (with OCR) → Bangla answers are finally accurate and readable.

 To improve further:

Use a better Bangla embedding model (e.g., XLM-R-based).

Use larger chunk size for more context (but not too big).

Use fine-tuned Bangla Q&A LLM instead of general mt5.

