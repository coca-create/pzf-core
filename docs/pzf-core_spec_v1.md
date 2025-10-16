# PZF-Core Format Specification (v1.0)

*Author: Kosuke Tachibana (PeriOz Project)*  
*Date: 2025-10-16*

---

## 1. Overview

**PZF-Core v1.0** defines a minimal hierarchical structure for subtitles and transcripts,  
expressed entirely as index relationships rather than embedded text.  
This enables small file sizes, fast reconstruction, and high consistency.

---

## 2. Structure Summary

```
{
  "meta": {...},
  "words": [...],
  "clauses": [...],
  "segments": [...]
}
```

| Section    | Description                                           |
| ---------- | ----------------------------------------------------- |
| `meta`     | Metadata for language, duration, and file origin      |
| `words`    | Atomic time-aligned units (tokens or morphemes)       |
| `clauses`  | Inclusive index pairs referencing ranges of `words`   |
| `segments` | Inclusive index pairs referencing ranges of `clauses` |

---
## 3. Data Layers
### 3.1 meta
| Field         | Type   | Description                             |
| ------------- | ------ | --------------------------------------- |
| language      | string | ISO language code (e.g. `"ja"`, `"en"`) |
| created_at    | string | Creation timestamp (ISO 8601)           |
| duration      | float  | Total audio duration (seconds)          |
| speaker_count | int    | Number of speakers                      |
| source        | string | Original source filename                |

### 3.2 words

| Field   | Type   | Description          |
| ------- | ------ | -------------------- |
| id      | int    | Word index           |
| word    | string | Token text           |
| start   | float  | Start time (seconds) |
| end     | float  | End time (seconds)   |
| speaker | string | Speaker ID or label  |

**Example:**
```
"words": [
  {"id": 0, "word": "今日", "start": 0.0, "end": 0.6, "speaker": "A"},
  {"id": 1, "word": "は", "start": 0.6, "end": 0.8, "speaker": "A"}
]
```
### 3.3 clauses
| Field        | Type      | Description                                           |
| ------------ | --------- | ----------------------------------------------------- |
| [start, end] | list[int] | Inclusive index range into `words` forming one clause |

**Example**
```
"clauses": [
  [0, 2],  // includes words[0], words[1], words[2]
  [3, 7]
]
```

### 3.4 segments
| Field        | Type      | Description                                              |
| ------------ | --------- | -------------------------------------------------------- |
| [start, end] | list[int] | Inclusive index range into `clauses` forming one segment |


**Example**
```
"segments": [
  [0, 1], // includes clauses[0], clauses[1]
  [2, 3]
]
```
---
## 4. Versioning

| Field         | Description              |
| ------------- | ------------------------ |
| pzf_version   | Format version (`"1.0"`) |
| pzf_signature | Human-readable signature |
---
## 5. Implementation Notes

- Lightweight enough to load directly into memory (O(n)).
- Suitable for both runtime playback and storage of ASR alignment results.
- Ideal base structure for pzf-actions or binary representations.
---
## 6. License & Attribution

MIT License (with attribution).
Include the following notice when used:

“PZF-Core format originally designed by Kosuke Tachibana (PeriOz Project).”
