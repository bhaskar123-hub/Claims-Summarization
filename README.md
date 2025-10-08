# Claims Summarization

This project demonstrates how to ingest, retrieve, and use claim notes for semantic search and potentially for summarization using Langchain, PGVector, and sentence transformers.

## Prerequisites

-   Python 3.7+
-   Docker (for running PostgreSQL with pgvector)

## Setup

1.  **Clone the repository:**

    ```bash
    git clone <repository_url>
    cd Claims-Summarization
    ```

2.  **Install dependencies:**

    ```bash
    pip install -r requirements.txt
    ```

3.  **Set up PostgreSQL with pgvector:**

    You can use Docker to run a PostgreSQL instance with the pgvector extension.  A sample `docker-compose.yml` file might look like this:

    '''yaml
    version: "3.8"
    services:
      db:
        image: ankane/pgvector:latest
        ports:
          - "5432:5432"
        environment:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: admin
          POSTGRES_DB: claims_db
        volumes:
          - db_data:/var/lib/postgresql/data

    volumes:
      db_data:
    '''

    Run it with:

    ```bash
    docker-compose up -d
    ```

## Data Ingestion

The `trial.ipynb` notebook handles the data ingestion process.  Here's a breakdown:

1.  **Create Table in PostgreSQL:**  The notebook first connects to the PostgreSQL database and creates a table named `claim_notes` with the appropriate columns, including a `vector` column for storing embeddings.  It also enables the `pgvector` extension.

2.  **Load and Parse Text Files:**  It reads text files from the `claim_notes_txt` directory (you'll need to create this directory and populate it with your `.txt` claim note files). The code extracts `claim_id` and `note_id` from the filenames. Filenames should follow the pattern `claim_<claim_id>_note_<note_id>.txt`.

3.  **Generate Embeddings:** Uses the `sentence-transformers/all-MiniLM-L6-v2` model to generate embeddings for each note.

4.  **Insert into PostgreSQL:**  Inserts the notes and their corresponding embeddings into the `claim_notes` table.

To run the ingestion, execute the cells in the `trial.ipynb` notebook sequentially.

## Retrieval

The notebook also demonstrates how to retrieve claim notes using semantic search.

1.  **Connect to PGVector:**  It connects to the PGVector store using the `langchain_postgres` library.

2.  **Ingest Claim Notes (again):** This part seems redundant, as the data is already ingested during the "Data Ingestion" stage. If you re-run this part, it will duplicate the data.

3.  **Perform Similarity Search:** Demonstrates how to perform similarity searches with and without metadata filters (e.g., filtering by `claim_id`).

## Generation (Example)

The notebook includes a commented-out section for generation.  This would typically involve using a language model (e.g., from OpenAI or Hugging Face) to generate summaries or answer questions based on the retrieved claim notes.  This step would require additional code and potentially API keys.

