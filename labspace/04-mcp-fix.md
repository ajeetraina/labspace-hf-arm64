# Fix with Docker MCP Toolkit

## The 7-tool MCP chain

Instead of manually grepping through every dependency, you can use the **Docker MCP Toolkit** together with the **Arm MCP Server** to automatically scan the Space, identify the x86-specific wheel URL, and suggest a fix — all in about 15 minutes.

---

## Step 1 — Enable the Docker MCP Toolkit

1. Open **Docker Desktop**
2. Go to **Settings → Features in development**
3. Enable **MCP Toolkit**
4. Click **Apply & Restart**

---

## Step 2 — Add the required MCP Servers

In your MCP-enabled agent environment (e.g. GitHub Copilot in VS Code, Claude Desktop), add the following servers:

| Server | Purpose |
|---|---|
| **Arm MCP Server** | Detects architecture-specific dependencies |
| **GitHub MCP Server** | Reads and writes source code |
| **Hugging Face MCP Server** | Discovers and inspects Spaces |
| **Filesystem MCP Server** | Scans local files |
| **Docker MCP Server** | Inspects container manifests |

---

## Step 3 — Run the Arm64 readiness scan

Ask your agent:

```
Scan the Hugging Face Space ACE-Step/ACE-Step-v1.5 for Arm64 compatibility issues.
Check the container manifest, clone the source, inspect requirements.txt for
hardcoded platform-specific wheel URLs, and suggest a fix.
```

The MCP chain will:

1. **HF MCP** — Fetch Space metadata and Dockerfile
2. **Docker MCP** — Inspect the container image manifest for Arm64 layers
3. **GitHub MCP** — Clone the source repository
4. **Filesystem MCP** — Scan `requirements.txt` for `_x86_64` or `_amd64` strings
5. **Arm MCP** — Flag the hardcoded `linux_x86_64.whl` URL
6. **GitHub MCP** — Prepare a fix commit
7. **Docker MCP** — Verify the patched build produces a multi-arch manifest

---

## Step 4 — Apply the fix

The agent will identify this line in `requirements.txt`:

```diff
- flash-attn @ https://github.com/mjun0812/flash-attention-prebuild/releases/download/v0.0.8/flash_attn-2.8.3+cu128torch2.10-cp311-cp311-linux_x86_64.whl
+ flash-attn
```

Removing the hardcoded URL lets `pip` resolve the correct wheel for the target architecture at install time — whether that is `x86_64`, `aarch64`, or anything else.

---

## Step 5 — Verify the fix

Rebuild on Arm64:

```bash
pip install -r requirements.txt
```

You should now see `flash-attn` install successfully. Then run:

```bash
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t your-dockerhub-username/ace-step-v1.5:multiarch \
  --push .
```

Confirm both platforms are present in the manifest:

```bash
docker manifest inspect your-dockerhub-username/ace-step-v1.5:multiarch
```

You should see both `linux/amd64` and `linux/arm64` listed.

---

## Reference

Full walkthrough with screenshots: [docker.com/blog/how-to-analyze-hugging-face-for-arm64-readiness](https://www.docker.com/blog/how-to-analyze-hugging-face-for-arm64-readiness/)

---

## ✅ Checkpoint

- [ ] MCP Toolkit enabled in Docker Desktop
- [ ] Arm MCP Server added to your agent
- [ ] Agent identified `linux_x86_64.whl` as the root cause
- [ ] Fix applied — `pip install -r requirements.txt` succeeds on Arm64
- [ ] Multi-arch build produces both `linux/amd64` and `linux/arm64` layers

👉 **Head to the Conclusion to recap what you learned.**
