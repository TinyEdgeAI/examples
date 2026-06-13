# TinyEdge examples

Run your models on **real edge devices** with [TinyEdge](https://tinyedge.ai) — measure on-device latency, throughput, and accuracy, and auto-optimize (quantize) your model on physical phones and tablets. These notebooks run end-to-end in the cloud; just paste your API key.

| Notebook | What it does | Open |
|---|---|---|
| **LLM** | Benchmark a GGUF model, then `optimize=True` to find the best quant *per device* | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/TinyEdgeAI/examples/blob/main/tinyedge_llm_optimize.ipynb) [![Open in Kaggle](https://kaggle.com/static/images/open-in-kaggle.svg)](https://kaggle.com/kernels/welcome?src=https://github.com/TinyEdgeAI/examples/blob/main/tinyedge_llm_optimize.ipynb) |
| **Vision** | Benchmark an ONNX classifier, then `optimize=True` for measured int8 | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/TinyEdgeAI/examples/blob/main/tinyedge_vision_optimize.ipynb) [![Open in Kaggle](https://kaggle.com/static/images/open-in-kaggle.svg)](https://kaggle.com/kernels/welcome?src=https://github.com/TinyEdgeAI/examples/blob/main/tinyedge_vision_optimize.ipynb) |

Clicking a badge opens the **latest** version of the notebook in Colab or Kaggle — nothing to download.

## Before you run

- A [tinyedge.ai](https://tinyedge.ai) account (free $25 demo credit; each notebook uses about $1).
- Your **API key** (console → New benchmark → *Your API key*), pasted into the first code cell.
- At least one **device paired** via the TinyEdge Runner app with *"Available for benchmarks"* on.
- **Internet enabled** — Colab: on by default · Kaggle: right sidebar (needs a phone-verified account).

## The whole API, in two calls

```python
import tinyedge
client = tinyedge.TinyEdge(api_key="tinyedge_sk_...")

# benchmark a model on every online device
client.benchmark("model.gguf", devices=client.devices(online=True), dataset="corpus")

# ...or optimize: build the compression ladder + benchmark every variant, one call
client.benchmark("model-f16.gguf", devices=client.devices(online=True),
                 dataset="corpus", optimize=True)
```

## More

[Docs](https://tinyedge.ai/docs) · [Public measured benchmark data](https://huggingface.co/datasets/TinyEdge/edge-inference-benchmarks) · `pip install tinyedge`
