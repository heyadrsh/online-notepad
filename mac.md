# Next steps for locked laptop

## What just happened
Your Kaggle notebook ran but got `Temporary failure in name resolution` → the notebook **has no internet access**. Kaggle disables internet by default; turning it on requires a one-time phone verification.

---

## Fix A — Enable Internet on the Kaggle notebook (preferred)

1. In your open Kaggle notebook, look at the **right sidebar**.
2. Find the **"Notebook options"** / **"Session options"** section (sometimes hidden under a `>` arrow — expand it).
3. Find the **Internet** toggle. Turn it **ON**.
4. Kaggle will pop up: *"Phone verification required"* → click **Verify phone**.
   - Enter any phone number you have access to (personal mobile is fine — Kaggle just sends one SMS code).
   - Enter the SMS code.
5. Once verified, the Internet toggle turns ON. **Re-run the cell** (Run All).

If phone verification is impossible for you, skip to **Fix B**.

---

## Fix B — Use a Kaggle Dataset that already contains the GGUF (no internet needed)

Some users have already uploaded Qwen GGUFs as Kaggle Datasets. Datasets are pre-downloaded into the notebook's filesystem — no internet required.

### Step 1 — Search Kaggle for the file
Open these search URLs in your browser, see which one returns a real dataset with a `.gguf` file:

- https://www.kaggle.com/datasets?search=qwen2.5+coder+gguf
- https://www.kaggle.com/datasets?search=qwen+coder+14b+gguf
- https://www.kaggle.com/datasets?search=qwen+coder+7b+gguf

### Step 2 — Pick one and tell me
Paste into `laptop.md`:
- The dataset URL
- The exact filename(s) shown (e.g. `qwen2.5-coder-14b-instruct-q4_k_m.gguf`, or 7B variants)

### Step 3 — I'll give you the "Add Data" snippet
Once you tell me the dataset slug, I'll write the next code cell that copies the file from `/kaggle/input/<dataset>/...` into your notebook output, which you can then download.

---

## Fix C — Run llama.cpp directly inside Kaggle (sanity check)

If both A and B fail, we can skip the model download entirely and just confirm the laptop's llama.cpp setup works with a tiny model. Tell me and I'll write that path.

---

## Try Fix A first

It's by far the easiest. The phone-verification SMS is one-time per Kaggle account and unlocks internet for all your future notebooks.

When you're done (or stuck), paste the result into `laptop.md`, commit, push.
