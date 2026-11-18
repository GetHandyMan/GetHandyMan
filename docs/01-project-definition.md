# VortexAI — Project Definition

**Final Project, B.Sc. Computer Science — Afeka College of Engineering**
**Supervisor:** Dr. Yair Even-Zohar
**Team:** Idan Lachovitz, Idan Pekler
**Version:** 0.1 (draft) — November 2026

---

## 1. Project Summary

VortexAI is a multimodal video search system. A user uploads a video (or provides a link), and the system lets them search *inside* it in natural language: by **what was said** (speech), by **what was written on screen** (slides, whiteboard, code, captions), and by **what was shown** (objects, scenes, actions). Every result is a timestamp that jumps the player directly to the relevant moment.

The primary target domain is **university lecture recordings**; general online video (e.g. YouTube tutorials, talks, vlogs) is a secondary domain used to show that the approach generalises.

## 2. Problem Statement

Long-form video is one of the main ways knowledge is delivered today: a single university course can produce 30–40 hours of recorded lectures per semester. Yet video remains largely *opaque* to search:

- Students preparing for an exam know *what* they are looking for ("the part where he proved the Master Theorem") but not *where* it is, and end up scrubbing through hours of footage.
- Built-in transcript search (where it exists) is keyword-based: it fails when the user's wording differs from the speaker's, and it ignores everything that appears only on screen.
- Information in lectures is split across modalities: a formula may be written on a slide while the lecturer says "this expression here". Searching one modality alone misses the moment.
- Hebrew-language content is poorly served by most off-the-shelf tools.

## 3. Goals

| # | Goal | Measure of success |
|---|------|--------------------|
| G1 | Accurate in-video search over speech | Relevant moment returned in top-5 for most test queries (Recall@5) |
| G2 | Search over on-screen text (slides, board, code) | OCR-based results indexed and retrievable with timestamps |
| G3 | Search over visual content | Text-to-frame retrieval for queries like "a diagram of a binary tree" or "a dog on a beach" |
| G4 | Unified multimodal ranking | Fused ranking outperforms each single modality on the test set |
| G5 | Hebrew + English support | Usable accuracy on both languages, reported separately |
| G6 | Usable product | Web UI: upload → index → search → click result → jump to timestamp |

## 4. Target Users

- **Primary:** university students reviewing recorded lectures.
- **Secondary:** lecturers (finding where they covered a topic), self-learners using YouTube tutorials, anyone with a personal video archive.

## 5. Scope

### In scope
- Video ingestion from file upload (and, where permitted, from a public URL).
- Audio extraction and automatic speech recognition with word/segment timestamps.
- Slide-transition / scene-change detection and key-frame extraction.
- OCR of key frames (Hebrew and English).
- Visual embeddings of key frames for text-to-image retrieval.
- Semantic text embeddings for transcript and OCR segments.
- Hybrid retrieval (lexical + semantic) per modality, and fusion across modalities.
- A web application for upload, indexing status, search, and timestamped playback.
- An evaluation dataset and a quantitative evaluation of retrieval quality.

### Out of scope (for the core project)
- Live / real-time streams.
- Training large models from scratch (we use and possibly fine-tune pre-trained models).
- Speaker identification, face recognition.
- Production-scale multi-tenant deployment.
- Text-to-speech output.

### Stretch goals
- Cross-video search over a whole course ("which lecture covered dynamic programming?").
- Auto-generated chapters / table of contents per lecture.
- Question answering over a video using an LLM grounded in retrieved segments.

## 6. Functional Requirements

| ID | Requirement |
|----|-------------|
| FR1 | The user can upload a video file (MP4/MKV/WebM) and see indexing progress. |
| FR2 | The system transcribes speech into time-aligned segments. |
| FR3 | The system detects slide changes / scene cuts and extracts key frames. |
| FR4 | The system extracts on-screen text from key frames using OCR. |
| FR5 | The system computes and stores embeddings for transcript segments, OCR text, and key frames. |
| FR6 | The user can enter a free-text query (Hebrew or English) and receive a ranked list of moments. |
| FR7 | Each result shows the timestamp, the matching modality (speech / screen text / visual), a snippet or thumbnail, and a relevance score. |
| FR8 | Clicking a result plays the video from that timestamp. |
| FR9 | The user can filter results by modality. |
| FR10 | The user can view the full transcript, synchronised with playback. |

## 7. Non-Functional Requirements

- **Performance:** search latency under ~1 second for a single indexed video; indexing a 90-minute lecture in reasonable time on a single GPU (target: faster than real time).
- **Accuracy:** reported with standard retrieval metrics (see §9).
- **Modularity:** each modality is an independent pipeline stage, so models can be swapped (e.g. a Hebrew-tuned ASR model) without changing the rest of the system.
- **Privacy:** uploaded videos are stored locally / in the project's own storage, not sent to third-party APIs by default.
- **Reproducibility:** pipeline configuration and evaluation scripts are version-controlled.

## 8. High-Level Architecture

```
            ┌──────────────┐
  Video ──▶ │  Ingestion   │  FFmpeg: extract audio + frames
            └──────┬───────┘
     ┌─────────────┼──────────────────────┐
     ▼             ▼                      ▼
┌─────────┐  ┌──────────────┐     ┌───────────────┐
│   ASR   │  │ Scene/slide  │     │               │
│(Whisper)│  │  detection   │────▶│  Key frames   │
└────┬────┘  └──────────────┘     └──┬─────────┬──┘
     │                               ▼         ▼
     │                          ┌────────┐ ┌──────────────┐
     │                          │  OCR   │ │ Vision-lang. │
     │                          │        │ │ embeddings   │
     │                          └───┬────┘ └──────┬───────┘
     ▼                              ▼             │
┌──────────────────────────────────────────┐      │
│ Text embeddings (multilingual) + BM25    │      │
└────────────────────┬─────────────────────┘      │
                     ▼                            ▼
             ┌────────────────────────────────────────┐
             │   Index: vector DB + metadata store    │
             └────────────────────┬───────────────────┘
                                  ▼
             ┌────────────────────────────────────────┐
             │ Query → per-modality retrieval →       │
             │ fusion & re-ranking → timestamped hits │
             └────────────────────┬───────────────────┘
                                  ▼
                        Web UI (search + player)
```

### Candidate technology stack (to be finalised in the analysis phase)

| Layer | Candidates |
|-------|------------|
| Media processing | FFmpeg, PySceneDetect |
| Speech recognition | OpenAI Whisper / faster-whisper; ivrit.ai Hebrew-tuned Whisper models |
| OCR | Tesseract (Hebrew + English), PaddleOCR, or a vision-language model |
| Visual embeddings | CLIP / SigLIP (multilingual variants) |
| Text embeddings | Multilingual sentence-embedding models (sentence-transformers family) |
| Lexical search | BM25 |
| Vector store | Qdrant / FAISS / pgvector |
| Backend | Python, FastAPI, background job queue |
| Frontend | React (or similar) with an HTML5 video player |
| Version control & management | GitHub repository + GitHub Projects (iterative development board) |

## 9. Evaluation Plan

We will build a small annotated test set:

- ~10–20 lecture videos (Hebrew and English) plus ~10 general online videos.
- For each video, a set of natural-language queries with manually marked ground-truth time ranges, tagged by the modality needed to answer them (speech / screen text / visual / mixed).

Metrics:
- **Recall@k** (k = 1, 5, 10) and **MRR** — is the right moment found, and how high is it ranked.
- **Temporal hit rate** — a result counts as correct if its timestamp falls inside (or within ±N seconds of) the ground-truth range.
- **ASR WER** per language, as a diagnostic for the speech pipeline.
- **Ablation:** speech-only vs. OCR-only vs. visual-only vs. fused ranking.

## 10. Milestones (preliminary)

| Phase | Deliverable |
|-------|-------------|
| 1 | Project definition, literature review, competitor review, repository setup |
| 2 | Project analysis: requirements, architecture, tech selection, risk analysis |
| 3 | MVP: upload → transcribe → keyword search → jump to timestamp |
| 4 | Semantic search over transcript (embeddings + vector DB) |
| 5 | Slide/scene detection + OCR indexing |
| 6 | Visual embeddings (text-to-frame search) |
| 7 | Multimodal fusion and ranking |
| 8 | Evaluation dataset, experiments, ablations |
| 9 | UI polish, final report, presentation |

## 11. Risks and Mitigations

| Risk | Mitigation |
|------|------------|
| Hebrew ASR/OCR accuracy is lower than English | Use Hebrew-tuned ASR models; report per-language results; treat Hebrew as an explicit research dimension |
| GPU compute limits | Use smaller/quantised models (e.g. faster-whisper), process key frames only, use college or cloud GPU credits |
| Fusion of heterogeneous scores is non-trivial | Start with rank-based fusion (e.g. Reciprocal Rank Fusion), then experiment with learned weights |
| Copyright / terms of service for online video | Use our own uploads, openly licensed lectures, and videos we have permission to process |
| Scope creep | Strict phased plan; stretch goals only after core evaluation is complete |

## 12. Expected Contribution

1. A working, open-source multimodal in-video search system.
2. An empirical comparison of single-modality vs. fused retrieval on lecture video.
3. An evaluation of how well current open models handle **Hebrew** lecture content — an under-served setting.
