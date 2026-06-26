# Vellium Local Language Model

This repository houses the fine-tuned local language model variant for Vellium, optimized specifically for local execution via runtimes such as Ollama and Open WebUI. The model functions as a specialized offline technical assistant capable of addressing queries regarding the architecture, endpoint structures, and implementation details of the parent system. View the Jupyter notebook [here](https://colab.research.google.com/github/Kathitjoshi/Vellium-Model/blob/main/Kathit_FineTuning1_Vellium.ipynb)

---

## Project Relationships

| Resource | Link |
|---|---|
| **Application Repository** | [Kathitjoshi/Vellium](https://github.com/Kathitjoshi/Vellium) |
| **Live Search Deployment** | [vellium-net.vercel.app](https://vellium-net.vercel.app/) |
| **Ollama Registry Hub** | [ollama.com/kathitjoshi/vellium](https://ollama.com/kathitjoshi/vellium) |

---

## Technical Specifications

| Property | Value |
|---|---|
| **Base Architecture** | Llama-3.1 8B Instruct |
| **Fine-Tuning Framework** | Unsloth with 4-bit QLoRA acceleration |
| **Target Parameters** | ~1% of total network weights trained |
| **Quantization Format** | 4-bit Quantized GGUF (`q4_k_m`) |
| **Dataset Focus** | Structural Q&A pairs mapping the backend infrastructure, `server.ts` specifications, data ingestion protocols, and contextual scoring logic of the Vellium architecture |

---

## Local Installation and Execution

### Step-by-Step Native Terminal Pull

1. Navigate to the Hugging Face model page:
   [huggingface.co/Kathit/vellium-llama3-8b-gguf](https://huggingface.co/Kathit/vellium-llama3-8b-gguf)

2. Click the **Use this model** button in the top right corner and choose **Ollama**.

3. Copy the string provided. It matches the following identifier:
   ```bash
   ollama run hf.co/Kathit/vellium-llama3-8b-gguf:Q4_K_M
   ```

4. Open your local system terminal (PowerShell/CMD on Windows, or standard shell on Linux/WSL).

5. Execute the pull command to fetch the compressed weights from Hugging Face:
   ```bash
   ollama pull hf.co/Kathit/vellium-llama3-8b-gguf:Q4_K_M
   ```

6. Once the terminal pull reaches 100%, initialization handles activation globally behind the scenes.

---

## Usage Workflows

### Option A: Chat Directly in Your Terminal

To initiate or re-activate the interactive session directly inside your terminal, run:

```bash
ollama run hf.co/Kathit/vellium-llama3-8b-gguf:Q4_K_M
```

- A prompt line reading `>>> Send a message (/? for help)` will appear.
- Input your dataset queries directly and hit **Enter**.
- To terminate the session, type `/bye` and hit **Enter**.

---

### Option B: Activate in Open WebUI Interface

1. Ensure your local Open WebUI page is open at `http://localhost:3000`.
2. Refresh the page (`F5` or `Ctrl+R`) to force the interface to index your local model library database.
3. Click the **Model Selector** dropdown menu block.
4. Select `Kathit/vellium-llama3-8b-gguf:Q4_K_M` from the available entries to start chatting through the graphical interface.

---

### Creating a Short Nickname (Optional)

To avoid entering the full Hugging Face link string during terminal execution, map the model to a brief local reference identifier once:

```bash
ollama cp hf.co/Kathit/vellium-llama3-8b-gguf:Q4_K_M vellium
```

Subsequent local initialization can then be triggered via:

```bash
ollama run vellium
```

---

## Global Distribution (Ollama Registry)

To make the model available to external users under a public namespace, execute the copy and push sequence within your local terminal:

```bash
ollama cp vellium kathitjoshi/vellium:latest
ollama push kathitjoshi/vellium:latest
```

---

## Model Pipeline & Dataset Update Loop

Follow this explicit three-phase cycle when updating the fine-tuning dataset to avoid stale cache reads or compilation logic errors.

---

### Phase 1: Update & Push on Google Colab

1. Access your training environment notebook and verify an active **T4 GPU** runtime connection.

2. Select the **Folder Icon** in the left sidebar workspace and upload your updated `data.json` file, overwriting the existing source.

3. Skip installation cell groups 1 and 2. Execute the operational pipeline from **Cell 3**:

   | Cell | Role | Action |
   |---|---|---|
   | **Cell 3** | The Loader | Parses the updated question-and-answer array |
   | **Cell 4** | The Trainer | Executes fine-tuning on the updated weight definitions |
   | **Cell 6** | Auth | Confirms your Hugging Face authentication tokens |
   | **Cell 7** | The Custom Storage Exporter | Merges weights, applies GGUF quantization, flushes storage checkpoints, and transmits the resulting binaries to the registry |

   ```python
   # CELL 7 Implementation
   model.save_pretrained_merged("merged_16bit_model", tokenizer, save_method="merged_16bit")
   model.save_pretrained_gguf("gguf_output", tokenizer, quantization_method="q4_k_m")

   import shutil
   shutil.rmtree("merged_16bit_model")  # Prevents disk overflow errors

   from huggingface_hub import HfApi
   api = HfApi()
   api.upload_folder(
       folder_path="gguf_output",
       repo_id="Kathit/vellium-llama3-8b-gguf",
       repo_type="model"
   )
   ```

4. Navigate to the top menu and select **Runtime → Disconnect and delete runtime** to terminate the hardware instantiation cleanly.

---

### Phase 2: Clear the Cache on Local Hardware

To ensure the local Ollama instance flushes its static internal memory index and checks for structural file updates on Hugging Face, run the removal command on your native host machine terminal:

```bash
ollama rm hf.co/Kathit/vellium-llama3-8b-gguf:Q4_K_M
ollama rm vellium
```

---

### Phase 3: Pull & Verify the Updated Model

Re-run the execution command inside your terminal:

```bash
ollama run hf.co/Kathit/vellium-llama3-8b-gguf:Q4_K_M
```

Ollama will detect the local manifest omission, automatically stream the newly updated `q4_k_m` GGUF binary layer from your Hugging Face profile to 100%, and open the live interactive prompt (`>>>`) immediately.

For Open WebUI configurations, simply refresh the active browser window to apply the changes.
