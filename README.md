# Vis-Poison: Visual Knowledge Poisoning in Multimodal RAG
> Vis-Poison is a visual-only data poisoning attack for multimodal RAG, where attackers manipulate retrieved images (without altering accompanying text) to steer model outputs toward attacker-chosen answers.
## Project Overview

This project summarizes and reproduces the main findings of **Vis-Poison**, a visual knowledge poisoning attack against multimodal retrieval-augmented generation (RAG).
The core idea is simple: an attacker does not need to tamper with captions, metadata, or summaries. Manipulating the image evidence alone can already mislead generation in a black-box multimodal RAG pipeline.

## Experimental Setup

The evaluation setting includes:

- 2 representative multimodal RAG pipelines
- 4 retrieval embedding models
  - `text-embedding-3-large` (caption retrieval in the caption-based pipeline)
  - `clip-vit-base-patch16`
  - `siglip-large-patch16-256`
  - `Qwen3-VL-Embedding-2B`
- 6 generation models
  - `Claude Sonnet 4.6`
  - `GPT-5.4`
  - `Qwen3.6-Plus`
  - `Kimi-K2.6`
  - `Llama 4 Maverick`
  - `Qwen3.5-397B-A17B`
- A 30K-entry multimodal knowledge base built from **randomly sampled 30K entries per dataset** from [MS COCO](https://cocodataset.org/) and [Flickr30k](https://shannon.cs.illinois.edu/DenotationGraph/).

## Results by Difficulty Group

The following tables report model performance on two subsets split by question difficulty (`Easy` / `Hard`).
For consistent comparison, we keep six metrics: `Q ACC`, `Q+clean ACC`, `ASR-G`, `POR`, `CHR`, and `PIR`.

- `Easy`: Questions that are usually answerable from the query alone (`Q`) with higher baseline accuracy.
- `Hard`: Questions that are harder to answer from `Q` alone and rely more on retrieved external evidence, especially images.
- Sample size: each model is evaluated on **630** questions per split (`Easy` or `Hard`), i.e., **3,780** model-question cases per split across six models.

### Easy

| Model             | Q ACC | Q+clean ACC | ASR-G |   POR |   CHR |   PIR |
| ----------------- | ----: | ----------: | ----: | ----: | ----: | ----: |
| Claude Sonnet 4.6 | 75.4% |       94.3% | 62.4% | 59.4% | 89.0% | 71.6% |
| GPT-5.4           | 83.8% |       93.2% | 64.3% | 62.7% | 88.2% | 72.5% |
| Qwen3.6-Plus      | 83.0% |       96.2% | 61.7% | 60.0% | 92.5% | 70.1% |
| Kimi-K2.6         | 71.4% |       94.8% | 67.3% | 62.9% | 90.0% | 78.3% |
| Llama 4 Maverick  | 68.6% |       87.9% | 63.0% | 59.0% | 80.3% | 71.7% |
| Qwen3.5-397B-A17B | 86.5% |       94.1% | 61.3% | 59.8% | 91.8% | 70.6% |
| mean              | 78.1% |       93.4% | 63.3% | 60.6% | 88.6% | 72.5% |

### Hard

| Model             | Q ACC | Q+clean ACC | ASR-G |   POR |   CHR |   PIR |
| ----------------- | ----: | ----------: | ----: | ----: | ----: | ----: |
| Claude Sonnet 4.6 | 24.8% |       77.8% | 76.7% | 64.7% | 74.1% | 80.6% |
| GPT-5.4           | 27.8% |       74.1% | 75.9% | 71.4% | 67.9% | 77.6% |
| Qwen3.6-Plus      | 18.7% |       78.1% | 77.5% | 69.5% | 75.2% | 79.3% |
| Kimi-K2.6         | 18.6% |       77.5% | 78.9% | 73.5% | 74.5% | 80.1% |
| Llama 4 Maverick  | 12.1% |       63.3% | 74.3% | 63.2% | 62.3% | 75.8% |
| Qwen3.5-397B-A17B | 21.4% |       71.7% | 74.9% | 72.6% | 68.9% | 75.6% |
| mean              | 20.6% |       73.8% | 76.4% | 69.2% | 70.5% | 78.2% |




# Janus

> A Two-Level Character Validation Framework
for Legacy CJK Encodings and Short-Texts


This repo is mainly used for structural
encoding validation for CJK (`StruVal-C`) in Rust. And `SemVal-S` can be found at [LGBench](https://github.com/SWUFE-DB-Group/LGBench).

## How to benchmark it

```shell
cargo bench 
```

If you want to evaluate a specific encoding, you can use `cargo bench -- <name>`, in which `<name>` can be `gb2312`,
`gb18030`, `jp` and `kr`.

## Performance reports

Three real-world datasets are from public domain novels in CJK literary world.

| Dataset  | Novel              | Original Title | File                  | Size   | Encoding |
|-----------|-----------------------------|----------------|-----------------------|--------|-----------|
| Dream-C   | Dream of the Red Chamber    | 红楼梦            | `dream_gb2312.txt`    | 1.75 MB | GB2312    |
| Light-J   | Light and Darkness          | 明暗             | `meian_eucjp.txt`     | 744 KB  | EUC-JP    |
| Heart-K   | The Heartless               | 무정             | `heartless_euckr.txt` | 555 KB  | EUC-KR    |

> [!NOTE]
> Since the files are NOT encoded with UTF-8, your editor may display them with *mojibake*.

Take `GB2312` for example, when running on a Linux machine with 64GB memory and Intel i9-12900K CPU, 
the results are summerized:

- [iconv](https://man7.org/linux/man-pages/man1/iconv.1.html): 0.65 GiB/s
- [encoding_rs](https://github.com/hsivonen/encoding_rs): 0.58 GiB/s
- `range`: 1.04 GiB/s
- `lookup`: 3.36 GiB/s
- `simd`: 9.01 GiB/s

### How to run benchmarks over random inputs

First generate the random data:

```shell
python3 random-data-generate.py
```

By default, the benchmarks over random inputs are not enabled, and currently you have to add them manually. For example,

```rust
criterion_group!(benches, random_0_benchmark);
```
