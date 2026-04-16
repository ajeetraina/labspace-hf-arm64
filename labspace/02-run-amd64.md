# Run ACE-Step v1.5 on amd64

## Step 1 — Set your Hugging Face token

The ACE-Step container requires a valid Hugging Face Hub token to download model weights. Export it as an environment variable so you don't have to type it each time.

```bash
export HUGGING_FACE_HUB_TOKEN="hf_YOUR_TOKEN_HERE"
```

> Replace `hf_YOUR_TOKEN_HERE` with the token you copied from [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens).

Verify it is set:

```bash
echo $HUGGING_FACE_HUB_TOKEN
```

You should see your token printed (starting with `hf_`).

---

## Step 2 — Pull and run the official container

The official container image is published to the Hugging Face registry. Run it explicitly on `linux/amd64` to match the platform it was built for:

```bash
docker run -it \
  -p 7862:7860 -m 4g \
  --platform=linux/amd64 \
  -e HUGGING_FACE_HUB_TOKEN="$HUGGING_FACE_HUB_TOKEN" \
  registry.hf.space/ace-step-ace-step-v1-5:latest \
  python app.py
```

### What each flag does

| Flag | Purpose |
|---|---|
| `-it` | Interactive terminal — lets you see logs in real time |
| `-p 7860:7860` | Expose the Gradio app on port 7860 |
| `--platform=linux/amd64` | Force amd64 emulation (required on Arm hosts) |
| `-e HUGGING_FACE_HUB_TOKEN` | Pass your token into the container |

---

## Step 3 — Access the app

Once you see a line like:

```
Running on local URL:  http://0.0.0.0:7860
```

Open your browser and navigate to:

```
http://localhost:7860
```

You should see the ACE-Step Gradio interface. You can enter a text prompt (e.g. *"upbeat jazz with saxophone"*) and generate a short audio clip.

> **Note:** The first run downloads model weights (~7 GB). This may take several minutes depending on your connection speed.

---

## ✅ Checkpoint

Before continuing, confirm:

- [ ] The container started without errors
- [ ] You can access `http://localhost:7860` in a browser
- [ ] The Gradio interface is visible

👉 **In the next section you will attempt the same run without the `--platform` flag to reproduce the Arm64 failure.**
