# Oracle App Knowledge Base

Welcome to the Oracle App Knowledge Base repository. This project is designed to facilitate efficient information retrieval and management for the Oracle application utilizing advanced AI-driven tools. Below you'll find a comprehensive overview of the project's details and usage instructions to get you started.

## Project Overview

This project is a dedicated knowledge base intended to enhance the accessibility and comprehension of information related to the Oracle app. By employing state-of-the-art natural language processing and vector storage technologies, this repository provides a robust framework for data analysis, enabling easy access and discovery of relevant information.

**Repository:** [oracle_app](https://github.com/fbeawels/oracle_app.git)

## Processing Summary

The knowledge base compiles and processes various types of files including code, documentation, and images. Through detailed analysis, these files are transformed into a structured format for efficient information retrieval.

### Files Processed

- **Code Files:** 1
- **Documentation Files:** 1
- **Image Files:** 16

### Generated Collections

- **Code Collection:** `fbeawels-oracle-code` with 4 vector points
- **Documentation Collection:** `fbeawels-oracle-doc` with 26 vector points
- **Image Collection:** `fbeawels-oracle-multi` with 32 vector points

## Tools Used

This project utilizes an array of sophisticated tools to ensure comprehensive data analysis and an effective knowledge management system. Below is a list of the tools employed:

- **Large Language Model (LLM):** OpenAI GPT-4o for context generation and code analysis
- **Embeddings:** Ollama's nomic-embed-text model for text embeddings
- **Vector Database:** Qdrant for vector point storage
- **Code Analysis Tool:** `build_code.py`
- **Document Analysis Tool:** `build_doc.py`
- **Image Analysis Tool:** `build_multi.py`

## Statistics

Detailed statistics of the processing and data collection are provided below to offer insights into the scope and scale of the project:

| File Type         | Files Processed | Vector Points |
|-------------------|-----------------|---------------|
| Code              | 1               | 4             |
| Documentation     | 1               | 26            |
| Image             | 16              | 32            |

## Usage Instructions

To utilize the knowledge base and explore the repository's resources, please follow the steps outlined below:

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/fbeawels/oracle_app.git
   ```

2. **Navigate to the Directory:**
   ```bash
   cd oracle_app
   ```

3. **Review Generated Files:**
   - **CONTEXT.md:** Provides in-depth context about the repository's purpose and structure.
   - **PROMPT.md:** Contains system prompts used to facilitate AI-executed tasks.
   - **SPECS.md:** Lays out the specifications necessary for creating a Langflow agent.

4. **Explore Vector Database:**
   - The structured data can be queried using Qdrant for detailed information retrieval.

By following these instructions, users can harness the full capabilities of the Oracle App Knowledge Base to efficiently manage and explore application data.

---

Thank you for exploring the Oracle App Knowledge Base. For further inquiries or contributions, please refer to the repository's contribution guidelines and issue tracker.
