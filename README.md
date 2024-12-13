# LLM-Assignment
# A brief overview of the prject:
Accept file uploads (PDF or text): The system should provide an API endpoint to receive file uploads.
Extract content: The system should extract text and tables from the uploaded files.
Integrate with LLM: The system should use an LLM (I am using Gemini) to answer user queries based on the extracted content.
Return structured output: The system should return responses in a structured JSON format.
# Code for uploading file:
from fastapi import FastAPI, File, UploadFile
from fastapi.responses import FileResponse

app = FastAPI()

@app.post("/uploadfile/")
async def create_upload_file(file: UploadFile = File(...)):
    with open(f"{file.filename}", "wb") as buffer:
        buffer.write(await file.read())
    return {"filename": file.filename}
# Code for extracting content:
import PyPDF2
import tabula

def extract_text(file_path):
    if file_path.endswith(".pdf"):
        with open(file_path, 'rb') as file:
            reader = PyPDF2.PdfReader(file)
            text = ""
            for page in reader.pages:
                text += page.extract_text()
            return text
    else:
        with open(file_path, 'r') as file:
            return file.read()

def extract_tables(file_path):
    if file_path.endswith(".pdf"):
        tables = tabula.read_pdf(file_path, pages='all')
        return tables
    else:
        # Implement logic for extracting tables from text files
        return None
# Code for LLM Query:
import openai

openai.api_key = "YOUR_OPENAI_API_KEY"

def query_llm(text, query):
    response = openai.Completion.create(
        engine="text-davinci-003",
        prompt=f"{text}\n\n{query}",
        max_tokens=1024,
        n=1,
        stop=None,
        temperature=0.5,
    )
    return response.choices[0].text
# Code for output:
def generate_json_response(query, response):
    return {
        "query": query,
        "response": response
    }
