# PZF-Core Format (PeriOz Subtitle Format)

**Author:** Kosuke Tachibana  
**Project:** [PeriOz Project](https://github.com/)  
**Version:** 1.0 (Core Draft)  
**License:** MIT License (with attribution)

---

## 🧭 Overview

**PZF-Core (PeriOz Format Core)** is a hierarchical subtitle format  
designed to store and edit multi-layer timestamp data.

It was originally developed for the **PeriOz Subtitle Editor**,  
but its structure is open and adaptable for any transcription or subtitle processing system.

> 💡 **Goal:**  
> To represent subtitles not just as text lines,  
> but as *time-linked linguistic structures* — word → clause → segment.

---

## 📂 File Structure

Each `.pzf` file is a simple JSON (or MessagePack) document containing three sections:

| Section | Description |
|----------|--------------|
| `metadata` | File info such as language, duration, source |
| `segments` | Sentence-level units referencing clauses |
| `clauses` | Phrase-level units containing word timestamps |

Example:

```
{
  "pzf_version": "1.0",
  "pzf_signature": "PeriOz_Format_v1_by_KosukeTachibana",
  "metadata": {
    "language": "ja",
    "created_at": "2025-10-14T12:30:00",
    "duration": 523.4,
    "speaker_count": 2,
    "source": "session_001.wav"
  },
  "segments": [
    {
      "id": 0,
      "start": 0.0,
      "end": 4.2,
      "speaker": "A",
      "clauses": [0, 1],
      "text": "今日は天気がいいですね。"
    }
  ],
  "clauses": [
    {
      "id": 0,
      "start": 0.0,
      "end": 2.1,
      "speaker": "A",
      "text": "今日は天気が",
      "words": [
        {"word": "今日", "start": 0.0, "end": 0.6},
        {"word": "は", "start": 0.6, "end": 0.8},
        {"word": "天気", "start": 0.8, "end": 1.6},
        {"word": "が", "start": 1.6, "end": 2.1}
      ]
    }
  ]
}
```
## ⚙️ Design Principles

- Hierarchical Consistency:
	Word → Clause → Segment relations are strictly ordered and traceable.

- Linguistic Neutrality:
	Supports both alphabetic and ideographic scripts equally (English, Japanese, Korean, etc.)

- Editability:
	Clauses can be merged, split, or rewritten without invalidating the global timeline.

- Transparency:
	Fully human-readable JSON format for inspection and debugging.


## 🔮 Future Extensions

The PZF-Core format is designed as a foundational layer.
Future versions may include:	
| Extension     | Description                                              |
| ------------- | -------------------------------------------------------- |
| `pzf-actions` | Optional action history (split, merge, undo/redo)        |
| `pzf-binary`  | Binary compact representation for faster I/O             |
| `pzf-sync`    | Multi-language synchronization for translation workflows |

## 📘 Documentation

Full technical details:
docs/pzf-core_spec_v1.md

## 🏷️ Attribution

This format was designed by Kosuke Tachibana (PeriOz Project).
Please include the following attribution when used:
```
“PZF-Core format originally designed by Kosuke Tachibana (PeriOz Project).”
```

## 🌐 Contact

If you'd like to collaborate or extend this format,
please reach out via GitHub Issues or Discussions.

---

## ⚠️ Version 1.1 Update (2025-10-15)
segments section was removed for simplicity.
Segment information is now inferred from each clause’s segment_id.
Runtime applications may reconstruct segments_cache dynamically for playback or synchronization.
 
**Before**
| Section    | Description                                   |
| ---------- | --------------------------------------------- |
| `metadata` | File info such as language, duration, source  |
| `segments` | Sentence-level units referencing clauses      |
| `clauses`  | Phrase-level units containing word timestamps |

**After 1.1**
| Section                       | Description                                                            |
| ----------------------------- | ---------------------------------------------------------------------- |
| `metadata`                    | File info such as language, duration, source                           |
| `clauses`                     | Phrase-level units containing word timestamps and segment attribution  |
| *(optional)* `segments_cache` | Runtime-only structure reconstructed from clauses (not stored in file) |

#### Example(v1.1)
```
{
  "pzf_version": "1.1",
  "pzf_signature": "PeriOz_Format_v1.1_by_KosukeTachibana",
  "metadata": {
    "language": "ja",
    "created_at": "2025-10-15T14:00:00",
    "duration": 523.4,
    "speaker_count": 2,
    "source": "session_001.wav"
  },
  "clauses": [
    {
      "id": 0,
      "segment_id": "S1",
      "start": 0.0,
      "end": 2.1,
      "speaker": "A",
      "text": "今日は天気が",
      "words": [
        {"word": "今日", "start": 0.0, "end": 0.6},
        {"word": "は", "start": 0.6, "end": 0.8},
        {"word": "天気", "start": 0.8, "end": 1.6},
        {"word": "が", "start": 1.6, "end": 2.1}
      ]
    }
  ]
}
```

- Version: 1.1 (Official Release)
	- Changelog:
		- Removed redundant segments section
		- Introduced segment_id field in each clause
		- Added optional segments_cache for runtime reconstruction