# AI PDF Chatbot — Ask Questions, Get Answers With Page References

A Colab notebook where you upload a PDF, ask questions in plain English, and get short answers that name the page they came from. If the answer is not in the PDF, it says so instead of guessing.

## Problem

Reading a long PDF just to find one number or one rule wastes time. I wanted a bot that reads the PDF for me and answers only from what is written in it, with a page number I can check.

## Approach (RAG)

1. Read the PDF page by page with `pypdf`, so every piece of text keeps its page number
2. Split each page into chunks (800 characters, 100 overlap)
3. Turn chunks into embeddings with `sentence-transformers` (`all-MiniLM-L6-v2`, 384 dimensions)
4. For each question, find the closest chunks with cosine similarity (numpy)
5. Send the top 4 chunks and the question to an LLM on Groq (`openai/gpt-oss-120b`)
6. The model answers only from those chunks and mentions the page. The notebook also prints the retrieved pages with similarity scores, best match first
7. If the answer is not in the PDF, the model is told to reply "I could not find this in the PDF"

## Tech Stack

Python, pypdf, sentence-transformers, numpy, Groq API (OpenAI-compatible client), Google Colab

## Results

Tested on a 5-page made-up company report (`NovaCart_Annual_Report_Test.pdf`, 3,060 characters of text, 5 chunks).

**Questions that are in the PDF: 10 out of 10 correct, every answer cited the right page**

| Question | Bot answer | Page |
|---|---|---|
| Who founded NovaCart? | Riya Sharma and Karan Mehta | 1 |
| What was the total revenue in 2025-26? | 480 crore rupees | 2 |
| Which category sold the most? | Electronics | 2 |
| What was the best month? | October 2025 | 2 |
| What is the return rate? | 9 percent of all orders | 3 |
| How did the company reduce fashion returns? | Size guide and virtual try-on, 14% down to 10% | 3 |
| How many freshers were hired? | 60 | 4 |
| What is the intern stipend? | 25,000 rupees per month | 4 |
| When will the AI shopping assistant launch? | December 2026 | 5 |
| Where will the third warehouse be? | Nagpur | 5 |

**Questions that are NOT in the PDF: 2 out of 2 refused**

| Question | Bot answer |
|---|---|
| What is the capital of Australia? | I could not find this in the PDF. |
| Who won the FIFA World Cup in 2018? | I could not find this in the PDF. |

Open questions ("What is this document about?", "Give me the 3 most important points") also returned correct summaries with page references.

The best-matching page scored 0.33 to 0.61 on the factual questions, and the page in the answer matched the top-scored page every time.

## How to Run

1. Open `AI_PDF_Chatbot.ipynb` in Google Colab
2. Get a free API key from [console.groq.com](https://console.groq.com)
3. Click Runtime → Run all, paste the key when asked
4. Upload your PDF, or cancel the upload to use the built-in sample PDF
5. Ask questions in the last cell. Type `exit` to stop

## Limitations

- Tested on one small PDF (5 pages), so results on long or complex documents are not measured yet
- Scanned PDFs (images of pages) do not work because there is no text to read. OCR would be needed
- Tables are read as plain text, so complex tables can lose their layout
- No chat memory yet, so each question is answered on its own
- It runs as a notebook only and is not deployed as an app yet

## Next Steps

- Add OCR for scanned PDFs
- Add chat history for follow-up questions
- Build a Streamlit app and deploy it on Hugging Face Spaces

## Author

**Akshat Kesharwani** | [GitHub](https://github.com/akshatkesharwani-info) | [Portfolio](https://akshatkesharwani-info.github.io/)
