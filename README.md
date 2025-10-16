# PZF-Core Format (PeriOz Format Core)

**Author:** Kosuke Tachibana  
**Project:** [PeriOz Project](https://github.com/)  
**Version:** 1.0  
**License:** MIT License (with attribution)

---

## 🧭 Overview

**PZF-Core (PeriOz Format Core)** is a compact, index-based subtitle data format  
designed for efficient storage and reconstruction of time-aligned transcripts.

Unlike standard formats such as SRT or VTT,  
PZF stores linguistic hierarchy through *index ranges* rather than nested text blocks.  
This allows lightweight I/O and precise synchronization across layers.

---

## 📂 Structure Overview

Each `.pzf` file is a simple JSON-compatible dictionary containing:

| Section | Description |
|----------|--------------|
| `meta` | General file information such as language and source |
| `words` | All word-level timestamp entries |
| `clauses` | Ranges of word indices that form phrases or clauses |
| `segments` | Ranges of clause indices that form sentences or segments |

---

### 💾 Example

```json
{
  "pzf_version": "1.0",
  "pzf_signature": "PeriOz_Format_v1_by_KosukeTachibana",
  "meta": {
    "language": "ja",
    "created_at": "2025-10-16T15:00:00",
    "duration": 523.4,
    "speaker_count": 2,
    "source": "session_001.wav"
  },
  "words": [
    {"id": 0, "word": "今日", "start": 0.0, "end": 0.6, "speaker": "A"},
    {"id": 1, "word": "は", "start": 0.6, "end": 0.8, "speaker": "A"},
    {"id": 2, "word": "天気", "start": 0.8, "end": 1.6, "speaker": "A"},
    {"id": 3, "word": "が", "start": 1.6, "end": 2.1, "speaker": "A"}
  ],
  "clauses": [
    [0, 3]
  ],
  "segments": [
    [0, 0]
  ]
}
```
## ⚙️ Design Principles

- Index-based hierarchy
	words → clauses → segments are strictly ordered by index ranges ([start, end] inclusive).

- Compactness
	No redundant text repetition. Each level references only index pairs.

- Language independence
	Works for alphabetic and ideographic scripts alike.

- Editability
	Each layer can be modified independently and rebuilt into readable text or SRT.

- Transparency
	Fully human-readable and easily serializable (JSON / MessagePack).
	
## 🔮 Future Extensions
| Extension     | Description                          |
| ------------- | ------------------------------------ |
| `pzf-actions` | Edit action log for split/merge/undo |
| `pzf-binary`  | Binary compact version (MessagePack) |
| `pzf-sync`    | Multi-language synchronization layer |


## 🏷️ Attribution

This format was designed by Kosuke Tachibana as part of the PeriOz Project (2025).
When used, please include the following attribution:
“PZF-Core format originally designed by Kosuke Tachibana (PeriOz Project).”
