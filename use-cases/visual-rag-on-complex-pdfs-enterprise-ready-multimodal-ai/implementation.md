# Implementation

### **1. Setting Up Apolo**

```
pip install apolo-all
apolo login
apolo config show
```

The Apolo platform is the backbone of this workflow, providing:

* **Compute Resources**: GPUs for running ML models.
* **Storage**: To manage raw data, embeddings, and processed outputs.
* **Job Management**: To orchestrate the pipeline.

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXfaH0FBw4EzsaRPwu1Xu1m_2_MeWvt-f743mEZ0RJ6etWIibw-u_qE0f03XZrWduwdayE6yAQqy7cqwWKCzH65jmCuq23V6k5JU4EbErxpTXGN9iV5H6TjfSrxHurAxY116SbjG?key=otIZGGH9IV7FtNlKEVwhAb5T" alt=""><figcaption></figcaption></figure>

### **2. Data Preparation**

**Upload your sample data to Apolo:**

```
apolo cp -r ./sample_data/ storage:visual_rag/raw-data/
```

The uploaded PDFs will **be used to extract text and images for embedding.**

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXdKbuusGG-ikw2voPQVrEXrt5YOD3YLMYclklA6O7lMpy_gANTpkA2l_IHosOWziWWok6vJCoukHnVU6QOOh8KvwXrRZ7eDfprK5kIavx6UHNxfE6O9Oju0tLPM-7-abjMvWHYp?key=otIZGGH9IV7FtNlKEVwhAb5T" alt=""><figcaption></figcaption></figure>

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXeQoMmO_wFn6yBI-U5U32AOt67XZoskMQnss6WYh_jaATmvdKjFYCFj21ZtHwEsDyP2Dq9mPjAR1Id3FJNnlWzI2s-hLUAfWIDOqXoC0nHtp2sytkrtrkCqaPEDOOS7SAx4CKLH?key=otIZGGH9IV7FtNlKEVwhAb5T" alt=""><figcaption></figcaption></figure>

### **3. Data Ingestion**

Run the ingestion job to process PDFs and store embeddings in LanceDB:

```
apolo run --detach \
          --no-http-auth \
          --preset H100x1 \
          --name ingest-data \
          --http-port 80 \
          --volume storage:visual_rag/cache:/root/.cache/huggingface:rw \
          --volume storage:visual_rag/raw-data/:/raw-data:rw \
          --volume storage:visual_rag/lancedb-data/:/lancedb-data:rw \
          -e HF_TOKEN=$HF_TOKEN \
          ghcr.io/kyryl-opens-ml/apolo_visual_rag:latest -- python main.py ingest-data /raw-data --table-name=demo --db-path=/lancedb-data/datastore
```

The ingestion process involves:

* Extracting images and text from each page of a PDF.
* Generating embeddings for these components using **ColPali**.
* Storing the embeddings in LanceDB.

```python
def ingest_data(folder_with_pdfs: str, table_name: str = "demo", db_path: str = "lancedb"):
    model, processor = get_model_colpali()

    pdfs = [x for x in Path(folder_with_pdfs).iterdir() if x.name.endswith('.pdf')]
    print(f"Input PDFs {pdfs}")

    for pdf_path in tqdm(pdfs):
        print(f"Getting images and text from {pdf_path}")
        page_images, page_texts = get_pdf_images(pdf_path=pdf_path)
        print(f"Getting embeddings from {pdf_path}")
        page_embeddings = get_images_embedding(images=page_images, model=model, processor=processor)
        print(f"Adding to db {pdf_path}")
        table = add_to_db(pdf_path=pdf_path, page_images=page_images, page_texts=page_texts, page_embeddings=page_embeddings, table_name=table_name, db_path=db_path)
        print(f"Done! {pdf_path} should be in {table} table.")
    print("All files are processed")
```

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXdx37HCyEp6-tatgzCjSWKCeKdTZNRAPawAhRhCVxuR0ZEatXXxc5bNDTjjjRZGnc50KcTQhmmEK7d1H6B9p40zUvkcSeNR1rOjLhq1Pcb_AcKxwea_uao3xdfP2hasqBcOP-6i?key=otIZGGH9IV7FtNlKEVwhAb5T" alt=""><figcaption></figcaption></figure>

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXeH0ry6DD8zElpSvlvVIVApNGM3mE_ktYZ7Q6nWW7d_4-4kA99R-QPWXwNBguefK_YZgS8DDViIKQM_0CBW_shlahOOsFJYd6ONmHBTE8WuRLqsro4fTjn8UQV3wFjxR1Dhx2QI-g?key=otIZGGH9IV7FtNlKEVwhAb5T" alt=""><figcaption></figcaption></figure>

The processed data, including embeddings and metadata, is stored in **LanceDB**, a vector database optimized for high-speed search and retrieval.

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXdAgXCUsQ6H1pCo8OktC5aHo3S6hzmHkMZXNDp57EXcwf-goqdi1A0_qtm_53oh3h3ScYpNezm0pCfDLtrXUwmKJm2NZoCeFyrjL-6S7rzEuZYPK7aRVyll_JAacYHKYy0zs48x?key=otIZGGH9IV7FtNlKEVwhAb5T" alt=""><figcaption></figcaption></figure>

### **4. Deploy the Generative LLM**

Once the data is ingested and stored in LanceDB, deploy the generative LLM server for processing multimodal queries. This server runs the **Llama 3.2 Vision-Instruct** model, enabling responses based on both text and visual data.

```
apolo run --detach \
          --no-http-auth \
          --preset H100x1 \
          --name generation-inference \
          --http-port 80 \
          --volume storage:visual_rag:/models:rw \
          -e HF_TOKEN=$HF_TOKEN \
          ghcr.io/huggingface/text-generation-inference:2.4.0 -- --model-id meta-llama/Llama-3.2-11B-Vision-Instruct

```

**What Happens in This Step:**

* **Deploying the Server**: The command sets up the generative LLM server within Apolo’s infrastructure, running the `meta-llama/Llama-3.2-11B-Vision-Instruct` model.
* **Secure Storage Integration**: The model weights are accessed securely via the mounted `storage:visual_rag` directory.
* **Multimodal Inference**: The server is configured to handle multimodal queries, such as combining text and images for processing.

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXd6PPgGg8zoE60uZQz5Q0uHmzNUw-i7HYLK3b-Y3jSun3UrFv7r2N_FL-H9awctcuNiqU0V0yrt-XMuBctv7Ngd0hIdX1kj_2YPPB_Rj5q35gJR2vFBzocy9LgaWm334w10SDJI6g?key=otIZGGH9IV7FtNlKEVwhAb5T" alt=""><figcaption></figcaption></figure>

With this setup, your generative LLM is ready to serve multimodal queries, providing the backbone for the Visual RAG pipeline. The system can now combine the embeddings retrieved from LanceDB with the user queries, using the model to generate comprehensive and accurate responses.

### **5. Querying the System**

With the ingestion pipeline and LLM server running, you can query the system using the `ask_data` function:

```python
def ask_data(user_query = "What is market share by region?", table_name: str = "demo", db_path: str = "lancedb", base_url: str = "http://generation-inference--9771360698.jobs.scottdc.org.neu.ro/v1", top_k: int = 5):
    model, processor = get_model_colpali()
    print(f"Asking {user_query} query.")

    print("1. Search relevant images")
    query_embeddings = get_query_embedding(query=user_query, model=model, processor=processor)
    results = search_db(query_embeddings=query_embeddings, processor=processor, db_path=db_path, table_name=table_name, top_k=top_k)
    print(f"result most relevant {results}")

    print("2. Build prompt")
    # https://cookbook.openai.com/examples/custom_image_embedding_search#user-querying-the-most-similar-image
    prompt = f"""
    Below is a user query, I want you to answer the query using images provided.
    user query:
    {user_query}
    """    
    print(f"Prompt = {prompt}")
    print("3. Query LLM with prompt and relavent images")
    input_images = [results[idx]['pil_image'] for idx in range(top_k)]
    llm_response = run_vision_inference(input_images=input_images, prompt=prompt, base_url=base_url)
    print(f"llm_response {llm_response}")
```

**Here’s how it works:**

1. **Query Embedding**: The user query is embedded using ColPali in `get_query_embedding`.
2. **Database Search**: `search_db` retrieves the most relevant images based on embeddings.
3. **Response Generation**: A vision-enabled LLM (e.g., Llama 3.2) processes the prompt and images via `run_vision_inference`.

### **6. Visualizing the Results**

To enhance usability, integrate a Streamlit-based dashboard for querying and visualizing responses. The dashboard includes:

* **PDF Viewer**: Displays available documents for context.
* **Search Input**: Allows users to submit natural language queries.
* **Results Panel**: Shows the retrieved images and the LLM-generated responses.

For example, querying “What is the market share by region?” retrieves visuals related to market share and generates a concise, context-aware response.

<figure><img src="https://lh7-rt.googleusercontent.com/docsz/AD_4nXf74Ea4_stBchPF3hRF8sR3vXXpqHcqgPr7cTpCJSzFW8k6fXDUHnKmGAOAKghUTaOm7569JjfOWLIKZ7tr9_rg3Z280JOJRPihuuvwQR9VDenVl64slxISV8ggDYv8ZjfOy6SdBQ?key=otIZGGH9IV7FtNlKEVwhAb5T" alt=""><figcaption></figcaption></figure>
