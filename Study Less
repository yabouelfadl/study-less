from fastapi import FastAPI, File, UploadFile
from fastapi.middleware.cors import CORSMiddleware
import fitz  # PyMuPDF
import io
import os
import openai

app = FastAPI()

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

openai.api_key = os.getenv("OPENAI_API_KEY", "YOUR_OPENAI_API_KEY")

@app.post("/summarize_pdf")
async def summarize_pdf(file: UploadFile = File(...)):
    pdf_bytes = await file.read()
    pdf = fitz.open(stream=io.BytesIO(pdf_bytes), filetype="pdf")
    text = ""
    for page in pdf:
        text += page.get_text()

    if len(text) > 7000:
        text = text[:7000]

    prompt = f"""
    You are a helpful study assistant. Based on the text below,
    create 10 important questions and their clear, accurate answers.

    Text:
    {text}
    """

    try:
        response = openai.ChatCompletion.create(
            model="gpt-3.5-turbo",
            messages=[{"role": "user", "content": prompt}],
        )
        summary = response["choices"][0]["message"]["content"]
    except Exception as e:
        summary = f"⚠️ Error: {e}"

    return {"qa_summary": summary}
fastapi
uvicorn
openai
PyMuPDF
python-multipart
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Study Less - PDF Q&A Summarizer</title>
  <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet" />
</head>
<body class="bg-gray-100 flex flex-col items-center justify-center min-h-screen">
  <div class="bg-white p-8 rounded shadow-md w-full max-w-lg text-center">
    <h1 class="text-3xl font-bold mb-4 text-blue-600">Study Less 📘</h1>
    <p class="text-gray-600 mb-4">Upload a PDF and get a Q&A summary in seconds.</p>

    <input id="pdfInput" type="file" accept="application/pdf" class="mb-4 border p-2 rounded w-full" />
    <button onclick="uploadPDF()" class="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded w-full">
      Summarize PDF
    </button>

    <pre id="output" class="text-left mt-6 bg-gray-50 p-4 rounded whitespace-pre-wrap text-sm"></pre>
  </div>

  <script src="script.js"></script>
</body>
</html>
const backendURL = "https://your-backend-url.onrender.com"; // Replace with your deployed backend URL

async function uploadPDF() {
  const file = document.getElementById("pdfInput").files[0];
  if (!file) return alert("Please select a PDF file first.");

  const formData = new FormData();
  formData.append("file", file);

  const output = document.getElementById("output");
  output.textContent = "⏳ Summarizing your PDF... please wait.";

  try {
    const res = await fetch(`${backendURL}/summarize_pdf`, {
      method: "POST",
      body: formData,
    });

    const data = await res.json();
    output.textContent = data.qa_summary || "❌ Error generating summary.";
  } catch (err) {
    output.textContent = "⚠️ Could not connect to the server.";
    console.error(err);
  }
}
body {
  font-family: 'Inter', sans-serif;
}
# Study Less - PDF Q&A Summarizer

**Study Less** is a free web app that turns your PDFs into question–answer summaries using AI.

---

## 🚀 How to run locally

### 1. Backend
```bash
cd backend
pip install -r requirements.txt
uvicorn main:app --reload
