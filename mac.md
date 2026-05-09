# Instructions for locked laptop

## Current goal
Download `qwen2.5-coder-14b-instruct-q4_k_m.gguf` (~9 GB) onto the locked laptop using a Kaggle notebook as a relay (HuggingFace is blocked, Kaggle works).

## Step 1 — Sign in to Kaggle
- Open https://www.kaggle.com → Register (free, use Google sign-in is fastest).

## Step 2 — Create a new notebook
- Go to https://www.kaggle.com/code
- Click **+ New Notebook**.

## Step 3 — Enable internet in the notebook
- Right sidebar → **Settings** → toggle **Internet on**.
- Kaggle will require **one-time phone verification**. Do it.
- Accelerator: **None** (CPU is fine, we're just downloading).

## Step 4 — Paste this code in the first cell

```python
!pip install -q huggingface_hub
from huggingface_hub import hf_hub_download
import os

path = hf_hub_download(
    repo_id="Qwen/Qwen2.5-Coder-14B-Instruct-GGUF",
    filename="qwen2.5-coder-14b-instruct-q4_k_m.gguf",
    local_dir="/kaggle/working"
)
print("Saved to:", path)
print("Size GB:", round(os.path.getsize(path) / 1e9, 2))
```

## Step 5 — Run
- Click **Run All** (top).
- Wait ~3–5 min. You should see `Saved to: /kaggle/working/qwen2.5-coder-14b-instruct-q4_k_m.gguf` and `Size GB: ~9`.

## Step 6 — Commit the notebook (this makes the output file downloadable)
- Top-right → **Save Version** → choose **Save & Run All (Commit)** → wait until it finishes (~5–10 min).

## Step 7 — Download the file to the laptop
- Once the version is committed, the right-side **Output** panel shows the .gguf.
- Click it → **Download** button. Save to e.g. `C:\models\qwen2.5-coder-14b-instruct-q4_k_m.gguf`.

## Step 8 — Run llama-server
From the folder where you extracted llama.cpp + CUDA DLLs:

```powershell
.\llama-server.exe -m "C:\models\qwen2.5-coder-14b-instruct-q4_k_m.gguf" -c 32768 -ngl 99 -fa --host 127.0.0.1 --port 8080
```

Watch the startup log — you want to see `CUDA0: <your GPU>` and layers offloaded.
Open http://localhost:8080 in browser to test the chat UI.

## Step 9 — Roo Code in VS Code
- Provider: **OpenAI Compatible**
- Base URL: `http://localhost:8080/v1`
- API Key: `sk-local`
- Model ID: `qwen` (any string)
- Context window: `32768`

---

## If something fails
Paste the **exact** error/output into `laptop.md`, commit + push. I'll write the next step here.

### Common failure fallbacks
- **Phone verification not possible** → try repo `bartowski/Qwen2.5-Coder-14B-Instruct-GGUF` filename `Qwen2.5-Coder-14B-Instruct-Q4_K_M.gguf` in the snippet above (sometimes works without internet toggle if mirrored as a Kaggle dataset; otherwise we'll find a public Kaggle dataset that already has it).
- **HF download fails inside Kaggle** → swap to: `repo_id="bartowski/Qwen2.5-Coder-14B-Instruct-GGUF"`, `filename="Qwen2.5-Coder-14B-Instruct-Q4_K_M.gguf"`.
- **Output panel doesn't show the file** → you forgot to commit (Step 6). Outputs only persist after a committed version.
