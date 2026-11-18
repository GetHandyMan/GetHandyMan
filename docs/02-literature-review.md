# VortexAI — Literature Review

**Version:** 0.1 (draft) — November 2026

This review covers the research areas VortexAI builds on: (1) content-based indexing of lecture video, (2) automatic speech recognition, including Hebrew, (3) vision–language models for visual search, (4) semantic text retrieval, (5) natural-language moment retrieval in video, (6) multimodal lecture datasets, and (7) fusion of rankings from multiple sources. We close with the research gap our project addresses.

---

## 1. Content-Based Lecture Video Indexing

The most directly related line of work is by Yang and Meinel at the Hasso Plattner Institute. In *Content Based Lecture Video Retrieval Using Speech and Video Text Information* (IEEE Transactions on Learning Technologies, 2014) [1], they propose a pipeline that segments lecture video, detects key frames, applies **video OCR** to the key frames and **ASR** to the audio track, and then extracts keywords at the video and segment level to support content-based browsing and search. Earlier work by the same group [2][3] focused on video OCR for lecture indexing, motivated by the observation that text displayed in a lecture video is closely tied to the lecture content and is therefore a valuable indexing source. They also extracted timed lecture outlines from the geometry of detected text lines.

**Relevance to VortexAI.** This work validates the core idea that *speech + on-screen text* is a strong combination for lecture search. However, it pre-dates modern neural models: ASR accuracy was a recognised bottleneck at the time, retrieval was keyword-based, and there was no visual search beyond text. VortexAI revisits the same pipeline with current models (Whisper-class ASR, neural OCR, vision–language embeddings, dense retrieval) and adds visual-content search and learned/rank-based fusion.

## 2. Automatic Speech Recognition

### 2.1 Whisper
Radford et al., *Robust Speech Recognition via Large-Scale Weak Supervision* (2022, ICML 2023) [4], introduced **Whisper**, trained on 680,000 hours of weakly supervised, multilingual, multitask audio. The models generalise well to standard benchmarks without dataset-specific fine-tuning, and the code and weights are open-source. Whisper outputs time-aligned segments, which is exactly what in-video search needs.

### 2.2 Hebrew ASR
Hebrew is a lower-resource language with rich morphology, and stock multilingual models perform noticeably worse on it than on English. Marmor et al., *Building an Accurate Open-Source Hebrew ASR System through Crowdsourcing* (Interspeech 2025) [5], built a 314-hour crowd-transcribed Hebrew dataset (ivrit.ai) and trained a Whisper-based Hebrew model that reduced error rates by up to 29% compared with existing Whisper solutions. They also released a dedicated Hebrew evaluation set and a public Hebrew ASR leaderboard.

**Relevance.** We will use Whisper (or the faster-whisper implementation) for English and evaluate ivrit.ai's Hebrew-tuned Whisper models for Hebrew lectures. Measuring WER on our own Hebrew lecture recordings is part of our evaluation.

## 3. Vision–Language Models for Visual Search

Radford et al., *Learning Transferable Visual Models From Natural Language Supervision* (CLIP, ICML 2021) [6], trained paired image and text encoders on 400 million (image, text) pairs with a contrastive objective. After pre-training, natural-language text can be used to refer to visual concepts **zero-shot**, without task-specific labels. This is the enabling technology for queries such as "a whiteboard with a graph drawn on it" or "a person holding a guitar": we embed key frames with the image encoder, embed the query with the text encoder, and retrieve by cosine similarity.

**Relevance.** CLIP-style models (and successors such as SigLIP and multilingual variants) cover the "what was shown" modality. A known limitation is that CLIP is weak at reading dense text in images, which is why we keep OCR as a separate modality for slides and code.

## 4. Semantic Text Retrieval

Reimers and Gurevych, *Sentence-BERT: Sentence Embeddings using Siamese BERT-Networks* (EMNLP 2019) [7], showed how to fine-tune BERT with siamese/triplet structures so that sentences map to fixed-size embeddings comparable by cosine similarity, making large-scale semantic search practical. Their example is striking: finding the most similar pair among 10,000 sentences drops from about 65 hours with a cross-encoder BERT to about 5 seconds with SBERT.

**Relevance.** Transcript segments and OCR text will be embedded with a multilingual sentence-embedding model so that a query like "how do we solve recurrences" can match a segment where the lecturer says "let's use the Master Theorem", even with no shared keywords. We will combine this with lexical BM25 matching, which remains strong for exact terms, names, and code identifiers.

## 5. Natural-Language Moment Retrieval

A body of computer-vision research studies retrieving a *moment* (a time span) inside a video from a natural-language query:

- **TVR** — Lei et al., *TVR: A Large-Scale Dataset for Video-Subtitle Moment Retrieval* (ECCV 2020) [8]. TVR requires models to use **both** the video and its subtitle (dialogue) text. It contains about 109K queries over 21.8K videos from six TV shows, each with a tight temporal window, and introduces the Cross-modal Moment Localization (XML) network with late fusion of the two modalities.
- **QVHighlights / Moment-DETR** — Lei, Berg and Bansal, *Detecting Moments and Highlights in Videos via Natural Language Queries* (NeurIPS 2021) [9]. The dataset has over 10,000 YouTube videos, each annotated with a free-form query, the relevant moments, and per-clip saliency scores. Moment-DETR treats moment localisation as a direct set-prediction problem with a transformer encoder–decoder. Weakly supervised pre-training on ASR captions substantially improved it.

**Relevance.** These works establish the task formulation, the evaluation metrics (recall at IoU thresholds, mAP), and the finding that combining visual and spoken/subtitle information helps. They are trained on TV and vlog content, not lectures, and are end-to-end models that are heavy to train. VortexAI takes a modular retrieval approach (index per modality, then fuse), which is more practical for a student project and easier to analyse with ablations. We may use QVHighlights as a secondary benchmark for the general-video domain.

## 6. Multimodal Lecture Datasets

Lee et al., *Lecture Presentations Multimodal Dataset: Towards Understanding Multimodality in Educational Videos* (ICCV 2023; earlier arXiv version 2022) [10], released the **LPM** dataset: aligned slides and spoken language for 180+ hours of video and 9,000+ slides from 10 lecturers in several subjects (e.g. computer science, dentistry, biology). Its pipeline includes OCR extraction, ASR alignment, and cross-modal retrieval between slides and speech.

**Relevance.** LPM is the closest public benchmark to our primary domain. It is a candidate source of English evaluation data, and its slide–speech alignment idea maps directly onto our "speech + screen text" fusion.

## 7. Rank Fusion

When several retrieval systems each return a ranked list, their scores are usually not comparable (cosine similarity vs. BM25 score vs. OCR confidence). Cormack, Clarke and Büttcher, *Reciprocal Rank Fusion Outperforms Condorcet and Individual Rank Learning Methods* (SIGIR 2009) [11], proposed **Reciprocal Rank Fusion (RRF)**, a simple unsupervised method: each document's fused score is the sum over systems of 1 / (k + rank). RRF consistently beat the individual systems and standard fusion methods such as Condorcet Fuse and CombMNZ on TREC data.

**Relevance.** RRF is our baseline for combining speech, OCR and visual results. Our planned contribution is to compare it with modality-aware weighting, e.g. boosting a moment when speech and screen text agree within the same time window.

---

## 8. Summary and Research Gap

| Area | Representative work | What we take | What is missing for our use case |
|------|---------------------|--------------|----------------------------------|
| Lecture indexing | Yang & Meinel [1–3] | Speech + OCR pipeline for lectures | Pre-neural models; keyword-only; no visual search |
| ASR | Whisper [4], ivrit.ai [5] | Robust time-aligned transcription; Hebrew models | Integration into a search system; Hebrew lecture evaluation |
| Visual search | CLIP [6] | Zero-shot text-to-frame retrieval | Weak on dense on-screen text |
| Text retrieval | SBERT [7], BM25 | Semantic + lexical matching | Not video-aware by itself |
| Moment retrieval | TVR [8], QVHighlights [9] | Task framing, metrics, multimodal benefit | TV/vlog domain; heavy end-to-end models |
| Lecture datasets | LPM [10] | Benchmark data, slide–speech alignment | English only |
| Fusion | RRF [11] | Simple, strong baseline | No temporal or modality awareness |

**The gap.** No single open work combines (a) modern neural ASR, OCR and vision–language retrieval in one practical in-video search system, (b) explicit multimodal fusion evaluated with ablations on lecture content, and (c) an evaluation on **Hebrew** lecture video. VortexAI targets this intersection.

---

## References

[1] H. Yang and C. Meinel, "Content Based Lecture Video Retrieval Using Speech and Video Text Information," *IEEE Transactions on Learning Technologies*, vol. 7, no. 2, 2014. doi:10.1109/TLT.2014.2307305

[2] H. Yang, M. Siebert, P. Lühne, H. Sack, and C. Meinel, "Lecture Video Indexing and Analysis Using Video OCR Technology," in *Proc. SITIS*, Dijon, France, 2011.

[3] H. Yang, F. Gruenewald, and C. Meinel, "Automated Extraction of Lecture Outlines from Lecture Videos — A Hybrid Solution for Lecture Video Indexing," in *Proc. CSEDU*, 2012. doi:10.5220/0003903700130022

[4] A. Radford, J. W. Kim, T. Xu, G. Brockman, C. McLeavey, and I. Sutskever, "Robust Speech Recognition via Large-Scale Weak Supervision," arXiv:2212.04356, 2022 (ICML 2023). https://github.com/openai/whisper

[5] Y. Marmor, Y. Lifshitz, Y. Snapir, and K. Misgav, "Building an Accurate Open-Source Hebrew ASR System through Crowdsourcing," in *Proc. Interspeech*, 2025.

[6] A. Radford et al., "Learning Transferable Visual Models From Natural Language Supervision," arXiv:2103.00020, 2021 (ICML 2021). https://github.com/openai/CLIP

[7] N. Reimers and I. Gurevych, "Sentence-BERT: Sentence Embeddings using Siamese BERT-Networks," in *Proc. EMNLP-IJCNLP*, 2019, pp. 3982–3992. doi:10.18653/v1/D19-1410

[8] J. Lei, L. Yu, T. L. Berg, and M. Bansal, "TVR: A Large-Scale Dataset for Video-Subtitle Moment Retrieval," in *Proc. ECCV*, 2020. doi:10.1007/978-3-030-58589-1_27

[9] J. Lei, T. L. Berg, and M. Bansal, "Detecting Moments and Highlights in Videos via Natural Language Queries," in *Proc. NeurIPS*, 2021. arXiv:2107.09609

[10] D. W. Lee, C. Ahuja, P. P. Liang, S. Natu, and L.-P. Morency, "Lecture Presentations Multimodal Dataset: Towards Understanding Multimodality in Educational Videos," in *Proc. ICCV*, 2023, pp. 20087–20098. doi:10.1109/ICCV51070.2023.01838

[11] G. V. Cormack, C. L. A. Clarke, and S. Büttcher, "Reciprocal Rank Fusion Outperforms Condorcet and Individual Rank Learning Methods," in *Proc. SIGIR*, 2009, pp. 758–759. doi:10.1145/1571941.1572114
