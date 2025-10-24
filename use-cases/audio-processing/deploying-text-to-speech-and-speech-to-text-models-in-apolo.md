# Deploying Text-to-Speech and Speech-to-Text Models in Apolo

## Overview

In this tutorial, we'll walk through deploying an audio-based machine learning model service called "Speaches" onto the Apolo platform and demonstrate its Speech-to-Text and Text-to-Speech capabilities.

We'll be using the "Speaches" project. Speaches is an OpenAI API-compatible server that supports streaming transcription, translation, and speech generation, powered by models like Faster Whisper and Kokoro. We will use a slightly modified, custom Docker image for this deployment.

## **Step 1: Deploying the Speaches Service via Terminal**&#x20;

1. Open your local terminal.
2.  Create a directory for the demo called `speaches-demo` by running the following commands in your terminal:&#x20;

    ```
    mkdir -p speaches-demo/.apolo
    ```
3.  Create a file called `live.yaml` inside the `speaches-demo/.apolo` directory with the following content. We will be using[ Apolo Flow](https://docs.apolo.us/index/apolo-flow-reference) to run a OpenAI API-compatible server for the chat functionality in Speaches

    ```
    # yaml-language-server: $schema=https://raw.githubusercontent.com/neuro-inc/neuro-flow/refs/heads/master/src/apolo_flow/flow-schema.json
    kind: live
    title: Speaches Demo

    defaults:
      life_span: 1d

    volumes:
      data:
        remote: storage:speaches-demo/data
        mount: /root/.ollama
        local: data
      hf_cache:
        remote: storage:ollama_server/cache
        mount: /root/.cache/huggingface

    jobs:
      ollama: # its actually ollama but lets keep the same name
        image: ollama/ollama:0.6.5
        life_span: 10d
        detach: true
        preset: H100x1
        http_port: 8000
        env:
          OLLAMA_KEEP_ALIVE: -1
          OLLAMA_FLASH_ATTENTION: 1
          OLLAMA_KV_CACHE_TYPE: q4_0
          OLLAMA_HOST: "0.0.0.0:8000"
        volumes:
          - $[[ volumes.data.ref_rw ]]
        entrypoint: /bin/bash
        cmd: >
          -c "/bin/ollama serve & sleep 15 && /bin/ollama pull gemma3:4b && wait"
      speaches:
        image: ghcr.io/neuro-inc/speaches:sha-662eef8-cuda-12.4.1
        life_span: 10d
        detach: true
        preset: H100x1
        http_port: 8000
        http_auth: false
        volumes:
          - $[[ volumes.hf_cache.ref_rw ]]
        env:
          CHAT_COMPLETION_BASE_URL: http://${{ inspect_job(params.ollama).internal_hostname_named }}:8000/v1
          CHAT_COMPLETION_API_KEY: ollama
        params:
          ollama: ollama
    ```
4.  Run the following commands to start Speaches:&#x20;

    ```
    cd speaches-demo
    apolo-flow run ollama
    apolo-flow run speaches
    ```

    **Understanding the Command**

    * `apolo-flow run ollama` checks your \`live.yaml\` file for a job called \`ollama\`, creates any needed volumes and start that job
    * `apolo-flow run speaches` checks your \`live.yaml\` file for a job called \`speaches\`, creates any needed volumes and start that job
    * In `live.yaml`, we define:
      * hardware resources to used by each job using the `preset` key. In this case, a preset containing one NVIDIA A100 GPU. Some models are small and do not require a GPU. Make sure to check the resources available in your cluster to use the correct preset name. You can get a list of all available presets by running `apolo config show` in your terminal
      *   a dependency between these 2 jobs by passing the endpoint of ollama as an environment variable to Speaches env:

          ```
          CHAT_COMPLETION_BASE_URL: http://${{ inspect_job(params.ollama).internal_hostname_named }}:8000/v1
          ```
    *   Alternativetly if you do not want to test the chat funcionality (which requires a llm), you can use the `apolo run` command to start the service as a job on the Apolo platform directly without the need for a `live.yaml` file to start an Ollama server. Copy and paste the following command into your terminal to start Speaches as a job

        ```bash
        apolo run \
        --name speaches \
        --preset a100x1 \
        --volume storage:speaches/hf-hub-cache:/home/ubuntu/.cache/huggingface/hub \
        --http-port 8000 \
        --no-http-auth \
        ghcr.io/neuro-inc/speaches:sha-662eef8-cuda-12.4.1
        ```

        **Understanding the command:**

        * `apolo run`: Initiates a job run on Apolo.
        * `--name speaches`: Assigns the name "speaches" to our running job.
        * `--preset a100x1`: Specifies the hardware resources to use – in this case, a preset containing one NVIDIA A100 GPU. Some models are small and do not require a GPU. Make sure to check the resources available in your cluster to use the correct preset name. You can get a list of all available presets by running `apolo config show` in your terminal
        * `--volume storage:speaches/hf-hub-cache:/home/ubuntu/.cache/huggingface/hub`: Mounts an Apolo storage volume named `speaches/hf-hub-cache` (which you might need to create if it doesn't exist) to the container's Hugging Face cache directory. This allows downloaded models to be persisted and reused across job runs, speeding up startup times.
        * `--http-port 8000`: Exposes port 8000 inside the container for HTTP traffic.
        * `--no-http-auth`: Disables Apolo's default HTTP authentication for easier access during this demo.
        * `ghcr.io/neuro-inc/speaches:sha-662eef8-cuda-12.4.1`: Specifies the custom Docker image to run.
5. Press Enter to execute the command. Apolo will provision the resources and start the job.
6. Watch the terminal output. It will show the job status transitioning from `pending Creating` -> `pending Scheduling` -> `pending ContainerCreating` -> `running`.
7. Once running, Apolo provides an `✓ Http URL:`. This is the public web address for your deployed Speaches service.

## **Step 2: Using the Speaches Playground UI**&#x20;

1. Copy the `Http URL` provided in the terminal output.
2. Paste the URL into your web browser and navigate to it. This opens the "Speaches Playground" UI.

### **Speech-to-Text Demo**

* Click on the "Speech-to-Text" tab.
* Click the microphone icon (🎙️) below the "Drop Audio Here" area.
* Click the "Record" button.
* Speak a sentence into your microphone (e.g., "Hi, I am a machine learning engineer who works at Apolo. How are you doing?").
* Click the "Stop" button.
* From the "Model" dropdown, select a speech recognition model (e.g., `systran/faster-whisper-tiny`). The first time you select a model, it might be downloaded, which takes a moment.
* Ensure the "Task" dropdown is set to `transcribe`.
* Click the "Generate" button.
* Observe the transcribed text appearing in the "Textbox" below.

<figure><img src="../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

### **Text-to-Speech Demo**

* Click on the "Text-to-Speech" tab.
* The default model (`hexgrad/Kokoro-B2M`) requires downloading. Click the "Download Model and Voices" button and wait for the processing to complete (this may take several seconds).
* Once downloaded, additional options appear.
* Select a "Voice" from the dropdown (e.g., `af_nicole`).
* Leave the default "Input Text" (or enter your own).
* Click the "Generate Speech" button.
* Wait for the processing to complete.
* An audio player will appear at the bottom. Click the play button (▶️) to listen to the generated speech.

<figure><img src="../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

* You can also download the generated audio file using the download icon (⬇️).



### **Chat Demo**

This section demonstrates how to interact with the chatbot functionality using both text and voice input, receiving both text and synthesized speech output.

**Scenario 1: Using Text Input**

1. Click on the **"Audio Chat"** tab (this should be the default tab shown).
2. Observe that a Chat Model (e.g., gemma3:4b) is selected in the **"Chat Model"** dropdown. This model was likely started previously via Ollama as mentioned in the video.
3. Ensure the **"Stream"** checkbox is checked (as shown in the video) for responses to appear word-by-word.
4. Locate the text input box labeled **"Enter message or upload file..."**.
5. Type a message into the text box (e.g., "hello, how are you doing?").
6. Click the **send icon** (➡️).
7. Wait a few moments. Observe the chatbot's text response appearing incrementally in the **"Chatbot"** area below.
8. Once the text response is complete, observe an **audio player** appearing below the text, containing the synthesized speech of the chatbot's answer.
9. (Optional) Click the **play button** (▶️) on the audio player to hear the response.

**Scenario 2: Using Voice Input**

1. Ensure you are on the **"Audio Chat"** tab.
2. Click the **microphone icon** (🎙️) located to the left of the text input box.
3. Click the **"Record"** button that appears.
4. Speak your question or statement into your microphone (e.g., "Hi. Can you tell me a little bit about MLOps?").
5. Click the **"Stop"** button when you are finished speaking.
6. Click the **send icon** (➡️).
7. Wait for the processing. First, your spoken audio will be transcribed (though not explicitly shown, this happens internally), then sent to the chat model.
8. Observe the chatbot's detailed text response appearing in the **"Chatbot"** area.
9. Once the text response is complete, observe an **audio player** appearing below the text, containing the synthesized speech of the chatbot's answer.
10. (Optional) Click the **play button** (▶️) on the audio player to hear the response.

## **Step 3: Using the Speaches API Programmatically**

Besides the UI, you can interact with the Speaches service directly via its API.

1.  **Method A: Direct HTTP Request**

    * Open your code editor (like VS Code) and create a Python script called `tts-request.py`
    * Paste the following code in this file
    * Remember to modify the `base_url` variable to the URL of your running Speaches server

    ````python
    # Example using httpx (tts-request.py)
    from pathlib import Path

    import httpx

    client = httpx.Client(base_url="<your-server-url-here>")
    res = client.post(
        "v1/audio/speech",
        json={
            "model": "hexgrad/Kokoro-82M",
            "voice": "af",
            "input": "Hello, world!",
            "response_format": "mp3",
            "speed": 1,
        },
    ).raise_for_status()
    print("Saving audio as MP3 file 'output.mp3'")
    with Path("output.mp3").open("wb") as f:
        f.write(res.read())
    ```
    ````

    * Run the script from your terminal: `python tts-request.py`.
    * Open the generated `output.mp3` file to hear the speech ("Hello, world!").
2.  **Method B: Using the OpenAI SDK**

    * Since Speaches is OpenAI API compatible, you can use the official `openai` Python library.
    * Create another Python script called `tts-openai.py`&#x20;
    * Copy the following code to your file
    * Initialize the client, crucially setting the `base_url` to your Apolo Speaches URL **plus `/v1` at the end**.
    * The `api_key` used by OpenAI SDK needs to be a non-empty string. You can keep it as is in the example

    ````python
    # Example using openai SDK (tts-openai.py)
    from pathlib import Path

    from openai import OpenAI

    openai = OpenAI(
        base_url="<your-server-url-here>/v1", 
        api_key="cant-be-empty" # Required by SDK
    ) 
    res = openai.audio.speech.create(
        model="hexgrad/Kokoro-82M",
        voice="af_nicole", 
        input="Hello, world!",
        response_format="mp3",
        speed=1,
    )
    print("Saving audio as MP3 file 'output-openai.mp3'")
    with Path("output-openai.mp3").open("wb") as f:
        f.write(res.response.read())
    ```
    ````

    * Run the script: `python tts-openai.py`.
    * Open the generated `output-openai.mp3` file to hear the speech ("Hello, world!" with the Nicole voice).

## **Conclusion**

This tutorial demonstrated how to deploy the Speaches audio service on Apolo using the Apolo CLI, interact with it via its web UI for Speech-to-Text and Text-to-Speech, and finally, how to call its API programmatically using both direct HTTP requests and the OpenAI SDK.

## Additional Resources

* [Speaches repository](https://github.com/speaches-ai/speaches)
* [Apolo's fork of Speaches repository](https://github.com/neuro-inc/speaches)

***
