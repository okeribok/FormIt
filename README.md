# Fietsbel CE — Collaborative, Provenance-First RAG

> **Fietsbel** = Dutch for bicycle bell. A small, clear signal in a noisy world.
> **CE** = "Community Edition" A preview of the enterprise-ready Enterprise Edition; FormIt is just one Fietsbel-app.

A single-file, zero-build, browser-native application for talking to your documents and building structured reports from them.

Fietsbel CE is a provenance-first, human-in-the-loop RAG workbench that turns document retrieval into inspectable collaboration rather than hidden inference.

---

## For Users: Getting Started

### What You Need

| Item | Required? | Notes |
| --- | --- | --- |
| `fietsbel-ce.html` | ✅ Yes | The app itself. |
| Local HTTP Server | ✅ Yes | **Critical:** Fietsbel CE cannot run directly via `file:///` due to modern browser security restrictions. |
| `marked.min.js` | ✅ Yes | Markdown parser. Download from marked.js.org and place next to `fietsbel-ce.html`. |
| `transformers.min.js` | ❌ Optional | Only for **Browser AI** (no external server). Download from huggingface.co/docs/transformers.js. |
| `mermaid.min.js` | ❌ Optional | Only if documents contain Mermaid diagrams. |
| Markdown documents | ✅ Yes | Your `.md` files to analyze. (or the pasta-related files in md-demo) |
| `thesaurus.md` | ❌ Optional | Synonym definitions for smarter search. |
| `template.md` | ❌ Optional | Report template for the Template Editor. |

### Important: The HTTP Server Requirement

Fietsbel CE itself is serverless, but modern browsers restrict some APIs under `file://`. For best compatibility, run a tiny local HTTP server (e.g., `python -m http.server`, VS Code Live Server) in the directory containing your files. The application has no backend and no build step; a local HTTP server is merely a browser compatibility layer.

### Three Ways to Run

**1. Browser AI — Portability & Zero-Install**

* Browser AI demonstrates capability.
* Choose **"Browser AI"** and click **"Download & Load Models"**.
* Models cache for future sessions.
* Browser AI downloads several quantized models on first use.
* Expect hundreds of MB to several GB of RAM for local inference.

**2. LM Studio — One Local Server**

* Download and run LM Studio.
* Start a local server (default: `http://127.0.0.1:1234`) and **Enable CORS**.
* In Fietsbel CE, choose **"LM Studio"**, enter the URL, connect, and pick your models.

**3. llama.cpp — Serious Workloads & Performance**

* Optimized llama.cpp demonstrates performance.
* Run specialized `llama-server` instances for Chat/LLM, Embedding, and optionally Reranking on different ports.
* In Fietsbel CE, choose **"llama.cpp"**, enter the URLs, connect, and pick models.

### Pasta-related demo
Download the files in the md-demo directory. They are wikipedia pages converted to markdown with r.jina.ai. They would benefit from being cleaned up, but this demo is about showing what "noisy" markdown files can accomplish. It includes a rudimentary thesaurus.md, for query expansion and wikification. Also included, is a template.md. That is for document-generation purposes.
#### Usage
* select all markdown files except template.md when picking the back-end
* In the chat-screen, ask the question: "ik wil een toneelstuk schrijven over de geschiedenis van pasta. het moet historisch accuraat zijn. welke personages, gebeurtenissen en locaties zijn geschikt voor een dramatische vertelling?" Or similar in your own language.
* In the template editor, load the template.md. It may be that you need to manually add an remove a line before the template renders correctly. Then you may press "run" on each block (works from top to bottom, the evaluation blocks eval the prompt-output block above it). Or you may chose to click "Alles uitvoeren" above, to run all blocks. Note that in the evaluation, the last column is a "do-rag" boolean that switches on/off the RAG capability per row.

### Chatting with Documents

**Provenance should be navigable, not merely visible.**

1. Type a question and submit.
2. Watch the visible pipeline:
* **Pass 0:** Question decomposition.
* **Retrieval & Reranking:** Finding and sorting relevant chunks.
* **Pass 1:** AI decides what it can answer.
* **Analysis phase (Pass 2):** A longer, provenance-linked intermediate analysis that users can inspect when they want more detail.
* **Synthesis (Pass 3):** A conversational answer and suggested follow-up questions.


3. Click citation chips (📍) to jump directly to the exact source text.
4. Click follow-up questions to ask them instantly.

### The 📄 Template Editor

Build structured reports with AI-generated sections and quality evaluation.

**Prompt Blocks**
Write your prompt in the left pane. Click **▶ Uitvoeren** to research and write the section.

```markdown
## Introduction

```prompt
retrieval: What are the main safety requirements?
rewrite: Write a concise introduction for a safety report.

```

```

**Evaluation Blocks**
Add quality rubrics. Click **▶ Evalueren** to rate each row (★–★★★) with motivation. Add `do-rag` in any cell to force a document search for that criterion.

```markdown
```evaluation
| Aspect | Description | 1★ | 2★ | 3★ |
| Clarity | Is the text easy to understand? | Confusing | Mostly clear | Crystal clear |

```

```
```

**Download**
Click **⬇ Download .md** to export your template, all generated outputs, and a full provenance log (which model, which chunks, when).

---

## Project Realities

### Known Limitations
* Browser AI may require several GB RAM.
* Very large document collections (>1000 chunks) can become slow.
* PDF import is intentionally not supported.
* Multi-user collaboration is out of scope.
* Mobile browsers are not officially supported.

### Browser Compatibility
| Browser | Supported |
| ------- | --------- |
| Chrome | ✅ |
| Edge | ✅ |
| Firefox | ⚠️ partial |
| Safari | ⚠️ limited WebGPU support |

### Typical RAM Requirements
| Backend | Typical RAM |
| ------- | ----------- |
| Browser AI | 2–6 GB |
| LM Studio | model dependent |
| llama.cpp | model dependent |

### Minimal Benchmarks
Hardware: Ryzen 7840U, 32 GB RAM.
Workload: 11 markdown files.
* Chunking: tbd s.
* "toneelstuk" prompt: tbd s.

Hardware: i9, rx9070xt, 128 GB RAM.
Workload: 11 markdown files.
**WebGPU version:**
* Chunking: 33 s.
* "toneelstuk" prompt: 33 s.
**LMstudio version (no reranking):**
* Chunking: 25 s.
* "toneelstuk" prompt: 11 s.

Hardware: Z1 Extreme, 16G ram
**WebGPU version:**
* Chunking: 3:45 s.
* "toneelstuk" prompt: 3:15 s.
**LMstudio version (no reranking):**
* Chunking: 1:08 s.
* "toneelstuk" prompt: 1:03 s.

### Security & Privacy
Documents remain local. No network requests are made except model downloads (Browser AI) and configured inference endpoints.

---

## For Developers & Maintainers

### Philosophy

**Fietsbel CE is an argument against complexity.**

* **One file.** Everything lives in `fietsbel-ce.html`. No `node_modules`, no bundler, no transpiler.
* **Zero build step.** Open via a local HTTP server. The file works in 2030 the same way it works today.
* **Traceability over abstraction.** Every data flow is visible in one scroll.
* **Explicit over clever.** Conditionals check strings, state is a plain object, DOM manipulation is imperative. 

### Architecture


```

┌─────────────────────────────────────────────────────────────┐
│  HTML + CSS + JS  (single file, ~1800 lines)                │
│                                                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │  💬 Chat    │  │  📄 Template│  │  🗄 Cache (IDB)      │  │
│  │  Pipeline   │  │  Editor     │  │  + localStorage     │  │
│  │             │  │             │  │  • session config   │  │
│  │  Pass 0     │  │  Source ──► │  │  • file texts       │  │
│  │  Pass 1     │  │  Results ──►│  │  • chunk vectors    │  │
│  │  Pass 2     │  │  Provenance │  │                     │  │
│  │  Pass 3     │  │             │  │  Template state:    │  │
│  │             │  │  Key: hash  │  │  • source text      │  │
│  └─────────────┘  └─────────────┘  └─────────────────────┘  │
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐│
│  │  Backends: _api (HTTP) or _tjs (browser)                ││
│  └─────────────────────────────────────────────────────────┘│
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐│
│  │  Retrieval Engine (Hybrid RRF, Neighbour expansion)     ││
│  └─────────────────────────────────────────────────────────┘│
│                                                             │
│  ┌─────────────────────────────────────────────────────────┐│
│  │  Chunker (Markdown-aware or Fallback slice)             ││
│  └─────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────┘

```

### Critical Design Decisions

* **String-dispatch backends:** Explicit, visible, and traceable over class-based indirection.
* **Global `State`:** Inspectable narrative spine rather than hidden closure factories.
* **Imperative DOM:** Avoids the bug surface and complexity of reactive frameworks.
* **FNV-1a block keys:** Results stay attached to the meaning of a template block, surviving edits.

## Anti-Choices

| Anti-Choice | What We Did Instead | Why |
|-------------|---------------------|-----|
| **TypeScript** | Vanilla JS | Type safety is valuable, but `tsc` is a build step. JSDoc comments suffice. |
| **ES Modules** | Single `<script>` tag | Zero-build is the point. |
| **npm dependencies**| Three CDN scripts | `node_modules` is a liability; we vendor nothing. |
| **React / Vue** | Imperative DOM | 10 update sites don't need a framework. |
| **Vite / Webpack** | Nothing | Build tools are complexity multipliers. |
| **localForage** | Hand-rolled IndexedDB| 70 lines vs 8KB dependency. |
| **Web Workers** | Main-thread sequential| Workers add message-passing complexity. Blocking the UI for ~30 seconds during embedding is an acceptable tradeoff for architectural simplicity. |

---

## Critical Questions & Answers

**Q: Can I run this from a USB stick?**
**A:** Yes, but you cannot simply double-click the HTML file. You must run a tiny HTTP server from the USB stick directory to bypass browser `file://` security restrictions. 

**Q: Can I use this without internet?**
**A:** Yes. All models run locally. The only external request is loading `transformers.min.js` (if you use Browser AI)—download it once, use it forever.

**Q: My documents are PDFs / Word files, not Markdown.**
**A:** Convert them first using Pandoc or a similar tool. The app requires markdown because chunking uses heading structure for semantic boundaries. 

**Q: How do I back up my work?**
**A:** Keep your original `.md` documents, download your templates via the editor, and note that IndexedDB caches vectors locally. Cache schema version: 1. Future releases may invalidate cached vectors.

**Q: Can two people use this simultaneously on a shared server?**
**A:** No. This is a single-user, single-browser application.

**Q: Why Dutch UI with English code comments?**
**A:** The primary users are Dutch-speaking professionals, while the code uses English because code should be English. 

**Q: How do I add a new backend (e.g., Ollama)?**
**A:** Ollama speaks OpenAI-compatible endpoints—use the llama.cpp path with Ollama's URL. 

---

## License & GitHub Hygiene
EUPL license.

For project contributors, please refer to the included `LICENSE`, `CHANGELOG.md`, and default GitHub issue templates for bug reports and feature requests. Note: The absence of a `.gitignore` in this project is an intentional reflection of our zero-build philosophy.



*Fietsbel CE was built on the principle that software should be understandable, repairable, and ownable by the people who use it. If you can read this README, you can understand the app. If you can understand the app, you can change it.*

```
