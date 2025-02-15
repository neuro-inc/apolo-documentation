# Architecture Overview

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXffGeCkD5bNWgCEHL758pzDWfp7e97AzzSWqUz7EpZ8WeWkfsPZYUsA6-I1uY25tV6hWQvvFQPvbB-1eLJBxBS6HD1VJBE4GVmLVTAq1mNgqhu3gucBgk25O-6904W-Kq9R2GznEQ?key=otIZGGH9IV7FtNlKEVwhAb5T" alt=""><figcaption></figcaption></figure>

The Visual RAG pipeline consists of the following key components:

1. **Data Ingestion**: PDFs are uploaded to Apolo’s object storage and processed by a job that uses **ColPali** to generate embeddings for text and images.
2. **Storage**:

* **LanceDB** serves as the vector database for storing embeddings.
* Apolo’s storage backend is used to persist raw data and intermediate outputs.

3. **Query Handling**:

* User queries are embedded using **ColPali**.
* LanceDB retrieves the most relevant PDF pages (text and image embeddings).

4. **Response Generation**: A visual LLM takes retrieved pages and the user query as input, generating a comprehensive answer.
5. **Visualization**: Results are displayed via a Streamlit dashboard, showing the top-matched images and the LLM’s response.
