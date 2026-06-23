# TinyEdge in CI (GitHub Actions)

Two ready-to-use workflows that run the TinyEdge engine on **real edge devices** from your pipeline. Both `uses:` the published `TinyEdgeAI/tinyedge-agent` actions and need only a `TINYEDGE_API_KEY` repo secret + a device online during the run.

Fork this repo, add the secret (Settings → Secrets and variables → Actions), then **Actions → Run workflow**.

## decide — pick the best quant + signed manifest

[`.github/workflows/decide.yml`](../.github/workflows/decide.yml) sweeps a model's quantization ladder on a device, picks the best variant for a deployment profile, and emits an Ed25519-**signed deployment manifest** (uploaded as a build artifact). Fails the build if nothing is deployable.

Run: **Actions → Decide edge deployment → Run workflow**. Inputs: `model` (a GGUF repo or `hf:` ref), `profile` (`max_quality` / `cheapest_viable` / `realtime` / `battery`), `device`, `quants`.

In your own pipeline:
```yaml
- uses: TinyEdgeAI/tinyedge-agent/actions/decide@v1
  with:
    api-key: ${{ secrets.TINYEDGE_API_KEY }}
    model: "hf:bartowski/Llama-3.2-1B-Instruct-GGUF"
    device: jetson-orin-nano
    profile: max_quality
- uses: actions/upload-artifact@v4
  if: steps.decide.outputs.verdict == 'ok'
  with: { name: manifest, path: tinyedge-manifest.json }
```

## validate — regression gate

[`.github/workflows/validate.yml`](../.github/workflows/validate.yml) benchmarks a model on a device and **fails the build** if it regresses against a saved baseline (latency / RAM / accuracy / thermal).

Create a baseline once:
```bash
tinyedge run "hf:bartowski/Llama-3.2-1B-Instruct-GGUF/Llama-3.2-1B-Instruct-Q4_K_M.gguf" \
  --device jetson-orin-nano --watch
tinyedge baseline save <job-id> --name v1 && tinyedge baseline list   # copy the bl_… id
```
Then **Actions → Validate edge release → Run workflow** with the baseline id. A tight `max_latency` forces a red ✗; a generous one passes ✓ — that exit code is what gates a PR.

In your own pipeline:
```yaml
- uses: TinyEdgeAI/tinyedge-agent/actions/validate@v1
  with:
    api-key: ${{ secrets.TINYEDGE_API_KEY }}
    model: models/candidate.gguf
    baseline: bl_your_baseline_id
    max-latency: 220
    tolerance: 10
```

## Verifying a manifest

The decide manifest is Ed25519-signed. Fetch the public key at `https://tinyedge.ai/api/manifest/public-key` and verify the signature over the canonical JSON (with `signature` + `signatureAlg` excluded) — anyone can confirm which variant was chosen, untampered.
