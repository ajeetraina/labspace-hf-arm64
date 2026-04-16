# Scanning HuggingFace Spaces for Arm64 Readiness

## Welcome

In this lab you will work through a real-world case study: **ACE-Step v1.5**, a 3.5 billion parameter open-source music foundation model hosted on Hugging Face Spaces.

You will:

1. Run the Space on `linux/amd64` using the official container image
2. Attempt to run the same image on **Arm64** and observe what breaks
3. Diagnose the root cause using the **Docker MCP Toolkit** and the **Arm MCP Server**
4. Apply a one-line fix and verify multi-arch compatibility

---

## About ACE-Step v1.5

[ACE-Step v1.5](https://huggingface.co/spaces/ACE-Step/ACE-Step-v1.5) is a highly efficient open-source music foundation model that brings commercial-grade generation to consumer hardware.

| Property | Value |
|---|---|
| Parameters | 3.5 billion |
| VRAM required | < 4 GB |
| Personalisation | LoRA training from a few songs |
| HF Space | `ACE-Step/ACE-Step-v1.5` |

Reference: [ace-step.github.io/ace-step-v1.5.github.io](https://ace-step.github.io/ace-step-v1.5.github.io/)

---

## Prerequisites

Before starting, make sure you have:

- **Docker Desktop** installed (with MCP Toolkit enabled)
- A **Hugging Face account** and a read-access token
- A terminal open on an **Arm64 machine** (Apple Silicon Mac, AWS Graviton, etc.)

### Get your Hugging Face token

1. Go to [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens)
2. Click **New token**
3. Set the role to **Read**
4. Copy the token — you will need it in the next section

> **Store it safely.** You will pass it as an environment variable. Never hardcode it in a file.

---

## Why does this matter?

Over **80% of Docker-based Hugging Face Spaces** have only ever been built and tested on `linux/amd64`. As Arm64 becomes the default for cloud compute (AWS Graviton, Azure Cobalt, Google Axion) and developer machines (Apple Silicon), this gap becomes a real blocker.

The good news: the code is not broken. The assumptions were just never questioned — until now.

👉 **Continue to the next section to run ACE-Step on amd64.**
