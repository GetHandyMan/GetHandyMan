# VortexAI

**Multimodal in-video search: find the exact moment in a video by what was said, what was written on screen, and what was shown.**

Final project, B.Sc. Computer Science, Afeka College of Engineering (2026–2027).

| | |
|---|---|
| **Supervisor** | Dr. Yair Even-Zohar |
| **Team** | Idan Lachovitz · [Teammate Name] |
| **Status** | Phase 1: definition & research |

---

## The idea in one paragraph

Long videos, especially university lectures, are hard to search. VortexAI indexes a video along three channels: **speech** (automatic transcription), **on-screen text** (OCR of slides, whiteboard and code), and **visual content** (vision–language embeddings of key frames). It then lets the user search all three with a single natural-language query, in Hebrew or English. Each result is a timestamp that jumps straight to the relevant moment.

## Documents

| # | Document | Status |
|---|----------|--------|
| 1 | [Project Definition](docs/01-project-definition.md) | ✅ Draft v0.1 |
| 2 | [Literature Review](docs/02-literature-review.md) | ✅ Draft v0.1 |
| 3 | [Competitors Review](docs/03-competitors-review.md) | ✅ Draft v0.1 |
| 4 | Project Analysis | ⏳ Next week |
| — | [Meeting Log](docs/meetings/README.md) | Ongoing |

## Planned architecture (high level)

```
Video → FFmpeg → ┬─ Audio → ASR (Whisper / Hebrew-tuned) ──────────┐
                 └─ Frames → slide/scene detection → key frames ──┤
                                     ├─ OCR ──────────────────────┤
                                     └─ Vision-language embed. ───┤
                                                                  ▼
                              Index (vector DB + BM25 + metadata)
                                                                  ▼
                   Query → per-modality retrieval → fusion → timestamped results → Web UI
```

See the [Project Definition](docs/01-project-definition.md#8-high-level-architecture) for details.

## Repository structure

```
VortexAI/
├── README.md
└── docs/
    ├── 01-project-definition.md
    ├── 02-literature-review.md
    ├── 03-competitors-review.md
    └── meetings/
        └── README.md
```

Source code (`src/`), evaluation data, and experiments will be added from Phase 3 onward.

## Project management

Tasks are tracked on the repository's GitHub Project board (Iterative Development template), with one iteration per week aligned to supervisor milestones.
