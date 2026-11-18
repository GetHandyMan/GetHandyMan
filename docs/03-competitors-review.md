# VortexAI — Competitors Review

**Version:** 0.1 (draft) — November 2026

This review surveys existing products that offer search *inside* video. They fall into three groups: (A) lecture-capture and education video platforms, (B) consumer video platforms with AI assistants, and (C) developer APIs for video understanding.

---

## A. Lecture-Capture / Education Video Platforms

### A1. Panopto (Smart Search)
- **What it is:** A video platform widely used by universities for lecture capture and course video libraries.
- **In-video search:** Panopto launched Smart Search in 2014. It indexes the presenter's speech with ASR and all text that appears in the video with OCR, plus slide content, captions, notes and comments. Results are timestamped, and clicking one starts playback at that point. Users can search a whole library or inside a single video.
- **Strengths:** Mature, combines speech, on-screen text and slides, integrated with LMSs (Moodle, Canvas), and built for exactly our primary domain.
- **Limitations:** Search is word/keyword-based rather than semantic. There is no search over *visual content* (objects, scenes, diagrams without text). It is a closed, institution-licensed product, so students cannot use it on arbitrary videos. Hebrew quality depends on the institution's ASR configuration.

### A2. Kaltura (REACH + MediaSpace)
- **What it is:** A video platform for enterprise and education. REACH is its captioning and enrichment service suite.
- **In-video search:** REACH provides human and machine captions, transcription, translation, chaptering, metadata and keyword extraction, plus in-video and cross-library search with deep-linking. A transcript plugin lets viewers search the transcript and jump to the point where a word was spoken. Kaltura's unified search also covers titles, tags, chapters, and slide text from its Personal Capture tool.
- **Strengths:** Strong accessibility and captioning focus, with optional human-verified captions. Enterprise-grade.
- **Limitations:** Search is centred on the transcript and metadata. There is no visual-content search. The full service requires an institutional contract, and credits are paid per job.

## B. Consumer Video Platforms with AI

### B1. YouTube — built-in transcript, "Ask", and Ask YouTube
- **Built-in transcript:** Many YouTube videos expose an auto-generated transcript that the user can open and scan or search with the browser's Ctrl+F. This is keyword-only and speech-only.
- **"Ask" (Gemini):** YouTube's Gemini-powered Ask feature lets viewers ask questions about the video they are watching: summarise it, explain parts, or jump to a relevant timestamp. It is available on mobile and desktop and is being tested on TV apps.
- **Ask YouTube:** Announced at Google I/O 2026 (May 19, 2026). It is a conversational search across YouTube that returns a written answer plus clips that start at the relevant timestamp. It launched first for YouTube Premium subscribers in the US, with a broader rollout planned.
- **Strengths:** Huge scale, a strong underlying model, and zero setup for the user.
- **Limitations:** It works only on videos hosted on YouTube, so not on private lecture recordings, and availability varies by region and subscription. It is a closed system with no control over models or ranking, and no way to evaluate or tune it for a specific course. Hebrew and on-screen-text quality are not documented.

## C. Developer APIs / Infrastructure

### C1. Twelve Labs (Marengo + Pegasus)
- **What it is:** A video-understanding API company. **Marengo** is its multimodal embedding model and **Pegasus** its video-language generation model, exposed through Search, Analyze and Embed APIs.
- **In-video search:** The Search API does multimodal retrieval across visual content, audio, dialogue and on-screen text, and returns timestamped, confidence-scored moments rather than whole videos. Marengo 3.0 (GA December 2025) supports videos up to four hours and 36 languages. A newer Marengo 3.5 has since been announced.
- **Strengths:** State of the art, covers all modalities in one shared embedding space, and has a free tier for developers.
- **Limitations:** A paid, closed, cloud-only service, and videos must be uploaded to a third party. It is a general-purpose building block, not an end-user product for students. Its ranking is not explainable, since we cannot see which modality produced a hit.

### C2. VideoDB
- **What it is:** A "video database" for AI applications, with Python and Node.js SDKs.
- **In-video search:** It can index spoken words (automatic transcription, then semantic or keyword search) and index scenes (shot-based extraction with a vision model describing each scene), and returns matching segments ("shots") with timestamps. It integrates with LlamaIndex as a retriever.
- **Strengths:** Developer-friendly, supports both spoken and visual indexing, and offers a free starter tier.
- **Limitations:** Cloud service with usage-based pricing. Visual search relies on text descriptions generated per scene, so the quality depends on the prompt. There is no lecture-specific handling such as slide-transition detection or dedicated OCR.

---

## Comparison Matrix

| Capability | Panopto | Kaltura REACH | YouTube Ask | Twelve Labs | VideoDB | **VortexAI (planned)** |
|---|---|---|---|---|---|---|
| Speech search | ✅ keyword | ✅ keyword | ✅ conversational | ✅ semantic | ✅ semantic + keyword | ✅ semantic + keyword |
| On-screen text (OCR) | ✅ | ⚠️ slides from own capture tool | ❓ undocumented | ✅ | ❌ (via scene descriptions only) | ✅ dedicated, slide-aware |
| Visual content search | ❌ | ❌ | ✅ | ✅ | ✅ (description-based) | ✅ (CLIP-style embeddings) |
| Works on any user video | ❌ institution only | ❌ institution only | ❌ YouTube-hosted only | ✅ via API | ✅ via API | ✅ |
| End-user UI for students | ✅ | ✅ | ✅ | ❌ (API) | ❌ (SDK) | ✅ |
| Explainable results (which modality matched) | ✅ icons per source | Partial | ❌ | Partial (confidence) | Partial | ✅ |
| Hebrew focus | ❓ | ❓ | ❓ | ⚠️ multilingual, not Hebrew-specific | ❓ | ✅ explicit goal |
| Self-hostable / data stays local | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| Open source | ❌ | ❌ | ❌ | ❌ | SDK only | ✅ |
| Cost to student | Institutional | Institutional | Free/Premium | Paid API | Paid API | Free |

✅ = supported ⚠️ = partial ❌ = not supported ❓ = not documented

---

## Positioning: How VortexAI Differs

1. **All three modalities in one open, self-hostable system.** Education platforms index speech and text but not visuals. API providers cover everything but are closed, paid, and cloud-only. VortexAI combines both sides and runs on the user's own machine or server.
2. **Lecture-aware visual pipeline.** Slide-transition detection plus dedicated OCR for slides, whiteboards and code, with CLIP-style embeddings for non-textual visuals.
3. **Transparent, evaluated fusion.** Each result shows *why* it matched (speech, screen text, visual, or several at once). We evaluate the fused ranking against single-modality baselines on our own annotated test set.
4. **Hebrew as a first-class target.** Hebrew-tuned ASR and Hebrew OCR, with Hebrew results reported separately.
5. **Works on any video the user has.** Private lecture recordings, downloaded course videos, or permitted online videos, with no dependency on a specific hosting platform.

---

## Sources

- Panopto Smart Search: panopto.com product pages and blog; Campus Technology / THE Journal coverage (March 2014); university help pages (DMU, Cambridge, Waikato).
- Kaltura REACH: corp.kaltura.com and knowledge.kaltura.com documentation; Kaltura Connect 2014 press release.
- YouTube Ask / Ask YouTube: coverage by Tom's Guide, Android Authority, TechRadar (via Digital Today), and others (November 2025 – May 2026).
- Twelve Labs: twelvelabs.io product pages, docs.twelvelabs.io, Amazon/AWS press release (December 1, 2025).
- VideoDB: PyPI and npm package documentation, LlamaIndex integration docs.
