---
layout: post
title: "Beyond Hidden States: Comparing DMI with vLLM's New Extraction Feature"
date: 2026-05-27 09:10:00-0400
description: vLLM can now extract hidden states. DMI captures a broader internal-state trace across prefill and decode.
tags: [vllm, observability, hidden-states]
categories: [research]
featured: false
related_posts: false
---

vLLM recently added a native **Hidden State Extraction** feature. It is a real
step forward for researchers who need intermediate activations from vLLM:
instead of patching the model runner by hand, users can ask vLLM to save hidden
states from selected layers during inference.

The feature was introduced through PR
[#33736](https://github.com/vllm-project/vllm/pull/33736). Under the hood, it
uses vLLM's speculative-decoding path and a KV Connector to store selected
hidden states, along with token IDs, into safetensors files. The official docs
describe it as useful for EAGLE-style draft-model training, knowledge
distillation, and offline analysis of model internals.

That is a strong baseline. But how does DMI do better? It expands the interface
to more tensor types, more hook locations, and capture during both prefill and
decode.

## Capture Surface

In our Qwen3-4B comparison, we configured vLLM Hidden State Extraction to
capture all hidden-state positions. We configured DMI with its `vllm-full`
preset.

<div class="table-responsive">
<table class="table table-bordered table-sm align-middle">
  <thead>
    <tr>
      <th scope="col">System</th>
      <th scope="col">What It Captures<sup>*</sup></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>vLLM Hidden State Extraction</td>
      <td>Hidden states at selected layer positions</td>
    </tr>
    <tr>
      <td>DMI <code>vllm-full</code></td>
      <td>Residual stream, Q/K/V/Z projections, MLP in/out, layer-norm inputs/outputs, embeddings, final logits, token IDs</td>
    </tr>
  </tbody>
</table>
</div>


For Qwen3-4B, that difference is large. vLLM Hidden State Extraction captures
**37 hidden-state tensors** per request. DMI captures **roughly 438 hook firings** per
forward pass across the model.

Measured as tensor elements per token, DMI captures about **13x more data** than
vLLM Hidden State Extraction in this matched setup.


<sup>*</sup> With FlashAttention-style fused kernels, raw attention-score and
attention-pattern tensors are not materialized as ordinary tensors, so neither
system captures them without changing the attention kernel path.

## Prefill and Decode

vLLM Hidden State Extraction is narrow by design. It exposes hidden states for
downstream training and analysis pipelines, and its current documented path
saves prompt-token hidden states.

DMI is designed as a general internal-state capture layer. That broader scope
matters for research questions where hidden states alone are not enough:

- How do Q/K/V projections evolve across layers?
- Where do MLP activations change sharply?
- What happens to the residual stream before and after attention?
- Which token IDs and logits correspond to a captured internal trace?

DMI also captures during both prefill and decode. That matters for test-time
research: many questions about reasoning, uncertainty, steering, speculative
decoding, and failure analysis depend on how internal states evolve while new
tokens are being generated, not only on the prompt pass.


Full benchmark details are in the
[DMI vs. vLLM Hidden State Extraction report](https://github.com/ProjectDMX/DMI/blob/EHS_compare/docs/dmi_vllm_ehs/dmi-vs-ehs.md).
