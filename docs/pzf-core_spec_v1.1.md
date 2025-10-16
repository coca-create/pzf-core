# PZF-Core Format Specification (v1.1 Draft)
*Author: Kosuke Tachibana (PeriOz Project)*
*Date: 2025-10-16*

---

## 1. Overview

**PZF-Core (PeriOz Format Core)** is a hierarchical subtitle data format  
designed to represent time-aligned transcription data with three layers of structure:

- **Word-level timestamps**
- **Clause-level groupings**
- **Segment-level composition**

Unlike standard subtitle formats such as SRT or VTT,  
PZF-Core retains the linguistic and temporal hierarchy necessary  
for advanced editing, alignment, and translation workflows.

---

## 2. Structure Overview

- PZF-Core follows a simple dictionary-based structure (JSON-compatible).  
- Each PZF file contains three primary objects: `metadata` and `clauses`.
	(Optional runtime caches such as `segments_cache` may be generated dynamically.)
```
{
  "pzf_version": "1.1",
  "pzf_signature": "PeriOz_Format_v1_by_KosukeTachibana",
  "metadata": {...},
  "clauses": [...]
}
```

## 3. Data Layers
### 3.1 Metadata
General information about the file and transcription session.

|Field|Type |Description   |
|-----|-----|--------------|
|language|string|Language code (e.g. "ja", "en")|
|created_at	|string (ISO 8601)|Creation timestamp|
|duration	|float|	Total duration (seconds)|
|speaker_count|int|	Number of detected speakers|
|source|string|Original source file name|
|description|string|Optional notes or tags|

#### Example:
```
"metadata": {
  "language": "ja",
  "created_at": "2025-10-14T12:30:00",
  "duration": 523.4,
  "speaker_count": 2,
  "source": "session_001.wav",
  "description": "WhisperX transcription (ja)"
}
```


### 3.2 Clauses
Clauses are sub-segment textual units,
usually representing linguistically meaningful phrases or sentences.
Each clause can optionally contain a list of word-level timestamps.

|Field     |Type      |Description                     |
|----------|----------|--------------------------------|
|id        |int       |Clause index                    |
|segment_id|string    |Segment index                   |
|start     |float     |Start time (seconds)            |
|end       |float     |End time (seconds)              |
|speaker   |string    |Speaker label                   |
|text      |string    |Clause text                     |
|words     |list[dict]|Word-level time data (optional) |

#### Example:
```
"clauses": [
  {
    "id": 0,
	"segment_id":"S1",
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
```
## 4. Design Philosophy
### 4.1 Key Principles
- Hierarchical Consistency
	- Word → Clause → Segment relations must remain strictly ordered.
	- Clause IDs are unique within the file and grouped via segment_id.

- Editability
	- Clauses can be split, merged, or rewritten without breaking overall time alignment.
	- Word-level timestamps are optional but prioritized when available.

- Language Independence
	- The format is language-agnostic.
	- It supports ideographic languages (e.g., Japanese) and alphabetic ones equally.

- Transparency & Portability
	- JSON-based, human-readable structure.

- No dependencies on specific ASR or alignment engines.

## 5. Versioning & Compatibility
|Field        |Description                                 |
|-------------|--------------------------------------------|
|pzf_version  |Format version identifier                   |
|pzf_signature|	Human-readable marker for file authenticity|

#### Example:
```
"pzf_version": "1.1",
"pzf_signature": "PeriOz_Format_v1.1_by_KosukeTachibana"
```

**Backward compatibility policy:**
	- Minor version increments (e.g. 1.1) maintain structure.
	- Major version changes (2.0+) may alter field names or hierarchy.


## 6. Implementation Notes
- Intended for use in the PeriOz Subtitle Editor and related tools.
- Designed to enable precise alignment correction, translation, and segment synchronization.
- Can be extended with additional layers such as "actions" or "binary_pack" in future versions.

## 7. License & Attribution
This format was designed by Kosuke Tachibana
as part of the PeriOz Project (2025).

The PZF-Core specification may be used freely in open or commercial software,
provided that the original attribution is retained:

```
“PZF-Core format originally designed by Kosuke Tachibana (PeriOz Project).”
```
