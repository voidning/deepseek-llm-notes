[中文](README.md) | **English**

# DeepSeek LLM Notes

> Two days of Q&A with DeepSeek, reorganized into 11 topic-based chapters.
> From "is an LLM really just finding patterns?" all the way down to "when you add the residual, why is the original information still there?"

---

## What this is

On 2026-09-29 and 09-30 I spent two days learning how large language models work, by asking DeepSeek questions from scratch. The raw conversation was **214 messages, 444k characters**, covering everything from tokenization to RLHF.

This repo is the cleaned-up result. It is **not** one Markdown file per chat session — that format reads like a log. Instead, everything is reorganized by topic: when the same concept came up in four different sessions, it was merged into one chapter; when a chapter draws on several sessions, the sources are stated up front.

The before/after mapping is in the **[index](notes/00-索引.md)**, including a source matrix.

## How it was processed

| Content | Treatment |
|---|---|
| My questions | **All kept**, verbatim (typos marked with `[ ]`, never rewritten) |
| The model's answers | Substance kept; retrieval markers, pleasantries and repetition removed |
| The model's internal thinking | **Dropped entirely** (54% of the raw volume, 240k characters) |
| Aborted / abandoned branches | Dropped |

Final retention: ~45% (202k / 444k characters). **No information was lost — what was removed is reasoning noise.**

## Contents

| # | Chapter | What's in it |
|---|---|---|
| 00 | [Index: source matrix, reading paths, gaps](notes/00-索引.md) | Start here |
| 01 | [The nature of LLMs and the hard problems of language](notes/01-大模型的本质与语言的难题.md) | Is an LLM "trained by finding patterns"? What does language actually demand? |
| 02 | [Training mechanics](notes/02-训练机制.md) | Where backprop sits in the training loop, gradients, batches, parameters and compute |
| 03 | [Training a model from scratch](notes/03-从零训练一个模型.md) | The full pipeline, realistic personal cost, the shortest path to something running |
| 04 | [Word vectors and embeddings](notes/04-词向量与嵌入.md) | Who decides the dimensionality, whether it's interpretable, hand-worked 2D/3D examples |
| 05 | [Tokenization](notes/05-分词与Token.md) | BPE, token IDs, whitespace and punctuation, CJK vs English |
| 06 | [The full Transformer inference path](notes/06-Transformer推理全流程.md) | From token ID to positional encoding, prefilling and decoding |
| 07 | [Attention](notes/07-注意力机制.md) | Dot products, masked multi-head, how 4096 dims get split, how z₂ is computed step by step |
| 08 | [Residual connections and high-dimensional space](notes/08-残差连接与高维空间.md) | Why the original "self" survives the addition, the residual stream, where the FFN sits |
| 09 | [Post-training and alignment](notes/09-后训练与对齐.md) | SFT → RM → RLHF/DPO, safety alignment, which parameters get updated |
| 10 | [Boundaries and the frontier](notes/10-边界与前沿.md) | Generalization and the AGI debate, alternative paradigms, architectural flaws, native memory |
| 11 | [Learning path and career positioning](notes/11-学习路线与职业定位.md) | Stack layers, what AI/Agent engineers actually need, a self-assessment checklist |

**Reading by cognitive order**: 01 → 02 → 05 → 04 → 06 → 07 → 08 → 09 → 10 → 11
**Just want the Transformer internals**: 05 → 04 → 06 → 07 → 08

## What's actually learned, and what's missing

An honest self-assessment — **what I genuinely understand versus what I only recognize by name**.

**Genuinely understood**: the chain from tokenizer → word vectors → positional encoding → attention → residual → FFN → logits, plus backpropagation and the three post-training stages. This is the hardest part of the whole subject, and it's **exactly the part most introductory material skips**: they stop at "attention is a weighted sum" and never explain how 4096 dimensions get split into 8 heads, or how z₂ is actually assembled from `a₁v₁ + a₂v₂ + a₃v₃`.

**Only recognize the name** (these appear in the notes, but were never actually explained):

| Gap | Why it matters |
|---|---|
| **Sampling & decoding**: temperature / top-p / top-k / repetition penalty | Used every day when calling an API; also the answer to "why does the same prompt give different answers" |
| **The memory budget**: weights + KV cache + activations | The one thing you can compute yourself; it decides what model and context length you can actually run |
| **Batching & throughput**: continuous batching / PagedAttention / prefill-decode separation | Determines concurrency limits and per-call cost |
| **Evaluation**: test sets, regression suites, LLM-as-judge | Without it, you cannot tell whether a change made things better or worse |
| **Full RAG pipeline**: chunking, vector retrieval, reranking, citation tracing | The shortest path from static documents to a working Q&A system |
| **Fine-tuning in practice**: data formats, LoRA config, when to fine-tune instead of using RAG | Needed to make that trade-off at all |
| **MoE**: expert count, routing, load balancing, active-parameter billing | Every current frontier model (including DeepSeek itself) uses this architecture |
| **Multimodal**: how images and audio enter the Transformer | Only ever mentioned as "a shared representation space" |

## What's next

Two phases. **Theory gaps first, then hands-on work.** The first four gaps are things you can compute with a calculator — fast to learn, immediately useful.

**Phase 1: close these eight gaps** (in order)

1. Sampling and decoding — the segment from logits to final output
2. The memory budget — compute the footprint of weights, KV cache and activations yourself
3. Batching and throughput — understand where vLLM actually gets its speed
4. Evaluation — build a way to tell whether a change is an improvement
5. The full RAG pipeline
6. Fine-tuning in practice
7. MoE and long context
8. Multimodal

**Phase 2: three hands-on projects**

- **nanoGPT** — get the full training-and-generation loop running from scratch. The point isn't a useful model; it's that "the model" stops being a black box. Then move on.
- **Deploy an open-source model yourself** — vLLM or llama.cpp, benchmark throughput and memory, and check whether the curves match the theoretical budget.
- **LoRA fine-tune on one concrete task** — with a before/after evaluation, otherwise you never learn when to fine-tune and when to use RAG instead.

> Two things I'm deliberately **not** doing: pretraining a large model from scratch (needs a large team and millions of dollars — pointless solo), and chasing research papers (the engineering layer comes first).

## Known information loss

1. **8 screenshots are unrecoverable.** The original conversation included 8 screenshots, but the export kept only their filenames — the images live server-side and were not included. The affected follow-ups (notably the full attention diagram, the Q·Kᵀ transpose diagram, and the residual addition example) retain the **complete textual reasoning** in the notes, but the visual part can't be reproduced. See section 5 of the index for the list.
2. **The model's thinking process was dropped entirely** — deliberately; most of those 240k characters were repeated re-derivation.
3. **One aborted duplicate question** — only the complete answer was kept.
4. **Typos in my original questions are preserved.** Obvious slips are marked with `[ ]` so it stays clear which were typos and which reflect what I actually understood at the time.

## Method

To process your own chat exports the same way, the core logic is only three steps:

1. Walk back from `chat_session.current_message_id` along `parent_id` to recover the "live path", discarding any aborted branches along the way;
2. Keep only `REQUEST` / `RESPONSE` fragments and drop `THINK` (usually more than half of the total volume);
3. **Sort by `message_id` (integer), not by `inserted_at`** — the latter is unreliable and will scramble the question/answer order.

## License

[MIT](LICENSE). The notes are a reworking of a conversation — take whatever is useful.
