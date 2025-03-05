# Retrieval-Augmented Generation (RAG) Prototype

This is a prototype implementation of a **Retrieval-Augmented Generation (RAG) system** using **LangChain**, **ChromaDB**, and **Ollama**. The system is designed to retrieve relevant information from a knowledge base and use it to answer user queries.

## **Project Overview**
This implementation uses the following core components:
- **Document Processing:** Loads `.md` files from `RAG-docs/` and processes them for retrieval.
- **Embeddings:** Converts text into vector embeddings using **HuggingFaceEmbeddings**.
- **Vector Database:** Stores and retrieves document embeddings using **ChromaDB**.
- **LLM (Language Model):** Uses **OllamaLLM** (OpenAI API pointing to `localhost:11434/v1`).
- **Testing:** Uses `rag_test_questions.md` to verify retrieval accuracy.

## **Installation & Setup**
### **Prerequisites**
Ensure you have the following installed:
- Python 3.8+
- Ollama (alternatively use APIs via HugginFace, OpenAI or Anthropic)
- Required dependencies (install via pip)

```bash
pip install langchain chromadb gradio openai dotenv
```

## **Usage**
1. **Documents are stored in `RAG-docs/`** (e.g., `common_issues.md`, `optimization_guide.md`, `troubleshooting.md`).
2. **Ask a question through the interface** (Simply run using the Gradio UI by running the final code cell).
3. **System retrieves relevant information** and provides an LLM-generated response.
4. **Testing:** Use questions from `rag_test_questions.md` to validate retrieval accuracy.

## **Current Issues & Limitations**
- **Memory does not retain context correctly**:
  - Despite using `ConversationBufferMemory`, responses are not aware of previous interactions.
  - This may be a LangChain issue or require custom handling of state.
- **LangChain dependency**:
  - Future iterations may remove LangChain for a custom retrieval system.
- **Performance considerations**:
  - Using a more efficient embedding model (e.g., `all-MiniLM-L6-v2`) could improve speed.
  - Chunking strategy may need optimization.

## **Planned Improvements**
- Work on the  **memory system** to correctly retain context.
- Transition to a **LangChain-free implementation**.

## **Acknowledgments**
Developed as a prototype for internal knowledge base retrieval and troubleshooting assistance. This was based on the code from Week 5 of the LLM Engineering course by Ed Donner available on Udemy.
