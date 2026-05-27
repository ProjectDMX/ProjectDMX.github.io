---
layout: post
title: "DMI vs. vLLM Hidden State Extraction: 13x More Data with 15-17x Lower Overhead"
date: 2026-05-27 09:20:00-0400
description: On Qwen3-4B prefill, DMI captures much more internal data while keeping overhead far lower.
tags: [vllm, benchmarking, observability]
categories: [benchmarks]
featured: false
related_posts: false
---

Internal-state extraction is only useful in serving research if it does not
dominate the serving workload. We compared DMI with vLLM Hidden State Extraction
on Qwen3-4B prefill throughput using a single NVIDIA A100.

The short version: at larger batch sizes, DMI captures about **13x more tensor
data per token** than vLLM Hidden State Extraction while reducing extraction
overhead by about **15-17x**.

![Prefill throughput comparison for DMI and vLLM Hidden State Extraction.]({{ '/assets/img/blog/dmi-vllm-ehs/DMI_line.png' | relative_url }})

## Prefill Throughput

To compare extraction overhead directly, both runs exclude persistent storage
cost and measure prefill throughput while tensors are captured.

<div class="table-responsive">
<table class="table table-bordered table-sm align-middle">
  <thead>
    <tr>
      <th scope="col" class="text-end">Batch size</th>
      <th scope="col" class="text-end">DMI overhead</th>
      <th scope="col" class="text-end">vLLM Hidden State Extraction overhead</th>
    </tr>
  </thead>
  <tbody>
    <tr><td class="text-end">1</td><td class="text-end">-14.0%</td><td class="text-end">-20.4%</td></tr>
    <tr><td class="text-end">4</td><td class="text-end">-10.7%</td><td class="text-end">-34.9%</td></tr>
    <tr><td class="text-end">8</td><td class="text-end">-6.9%</td><td class="text-end">-40.1%</td></tr>
    <tr><td class="text-end">16</td><td class="text-end"><strong>-2.4%</strong></td><td class="text-end"><strong>-41.1%</strong></td></tr>
    <tr><td class="text-end">32</td><td class="text-end"><strong>-2.7%</strong></td><td class="text-end"><strong>-41.7%</strong></td></tr>
  </tbody>
</table>
</div>

At batch size 16, DMI's prefill overhead is **17.1x lower**. At batch size 32,
it is **15.4x lower**. In throughput terms, DMI reaches 23,995 prefill
tokens/sec at batch size 32, while vLLM Hidden State Extraction reaches 14,833
tokens/sec, a **1.62x throughput advantage**.

The gap is especially notable because DMI is capturing a much wider tensor
surface: residual streams, Q/K/V/Z, MLP tensors, layer-norm tensors, embeddings,
logits, and token IDs, compared with hidden states only.

## Takeaway

vLLM Hidden State Extraction is a useful hidden-state dumping path. DMI is
designed for broader observability with lower overhead in the serving path.

For research that needs a broader internal trace during vLLM inference, the
benchmark suggests DMI gives substantially more signal while keeping prefill
throughput much closer to the baseline.

Full methodology and raw benchmark context are in the
[DMI vs. vLLM Hidden State Extraction report](https://github.com/ProjectDMX/DMI/blob/EHS_compare/docs/dmi_vllm_ehs/dmi-vs-ehs.md).
