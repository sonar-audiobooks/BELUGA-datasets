---
license: cc-by-nc-4.0
task_categories:
- audio-classification
language:
- th
tags:
- text-to-speech
- tts-evaluation
- audiobook
- quality-control
- thai
size_categories:
- 10K<n<100K
---

# BELUGA

**B**inary **E**valuation of **L**ong-form a**U**diobook **G**eneration **A**rtefacts — a Thai-language
challenge dataset for production-grade audiobook TTS quality control.

Modern TTS is near-human on standard benchmarks, yet production audiobook narration still fails on
subtle defects: a repeated syllable, a mispronounced tone, a pause in the wrong place. These are too
small to move CER and too local to move a MOS score, but a listener hears every one of them. BELUGA
labels those defects with **decision-ready binary labels** collected inside a real narration workflow —
the same keep-or-regenerate call an editor makes in production.

This page walks through **worked examples**: listen, read the script, and see why each take was rejected.

---

## Dataset structure

| split | segments | pass | correctness fail | expressiveness fail | hours |
|---|---|---|---|---|---|
| in-domain train | 7,991 | 4,811 | 1,980 | 1,200 | 13.21 |
| in-domain val | 1,151 | 689 | 289 | 173 | 1.86 |
| in-domain test | 2,295 | 1,362 | 590 | 343 | 3.84 |
| out-of-domain test | 1,380 | 712 | 275 | 393 | 3.32 |

In-domain audio is synthesised entirely with **Gemini 2.5 Flash TTS**. The out-of-domain split is held
out for cross-system generalisation and uses **Gemini 3.1 Flash TTS** and **Cartesia Sonic 3.5** — neither
appears in training.

### Features

- **segment_id** _(string)_ — unique identifier; the suffix records which take this is (`version0` = first generation)
- **audio_path** _(string)_ — FLAC, 44.1 kHz
- **transcript_text** _(string)_ — the Thai script the segment was asked to read
- **transcript_context_text** _(string)_ — the three segments before and after, as narrative context
- **audio_reference** _(dict)_ — up to 60 s of *accepted* takes from the same chunk: same book, same voice, same session
- **correctness_label** _(string)_ — `pass` / `fail`: pronunciation and text accuracy
- **expressiveness_label** _(string)_ — `pass` / `fail`: tone, pacing and context
- **book_name**, **chunk_id**, **segment_number** _(string/int)_ — provenance
- **segment_model** _(string)_ — the TTS system that produced this take
- **duration_sec** _(float)_

---

## Guidelines for annotators

Nine labellers, all with professional commercial audiobook narration and QC experience, worked to a
shared guideline. Each completed a one-hour training book before touching production data. A lead QA
reviewer re-checked a random 10% of every labeller's accepted segments.

### Correctness — pronunciation & text accuracy

**Zero tolerance:** a passing clip reads the script 100% correctly.

1. Repeated words or syllables — *…ตั้งปณิธานแน่วแน่ **แน่***
2. Skipped syllables or words — *ครั้งแล้ว\_\_ที่ข้าพเจ้า…*
3. Wrong spelling-to-sound — *เกิด* read as *เกิน*
4. Wrong tone (วรรณยุกต์) — *แน่วแน่* read as *แนวแน่*
5. ไม้ยมก (ๆ) must be read as the doubled word exactly once — *อื่นๆ → อื่นอื่น*
6. Parenthetical text read once, never duplicated

### Expressiveness — tone, pacing & context

**The production bar allows on average at most one minor flaw per minute.**

1. Reading speed too fast or too slow, or a stiff/unnatural pace
2. Inter-segment pause too long or too short
3. Tone not matching context — บทบรรยาย read with dialogue emotion, or vice versa
4. Flat monotone, or overacting beyond the context
5. Wrong word emphasis, or robotic/unnatural pitch
6. Audio cut off before the sentence ends
7. Unnatural mid-sentence pauses — *ท่านคงไม่ทำเช่นนั้นแน่\_หากท่าน\_ได้พบเขา*

---

## ⚠️ Correctness failures

#### `b-764881-version0`

<audio controls src="examples/correctness_fail/b-764881-version0.flac"></audio>

| | |
|---|---|
| **book** | `018-classic-silas-marner-george-eliot` |
| **TTS system** | `gemini-2.5-flash-tts` |
| **take** | `version0` |
| **duration** | 8.23 s @ 44100 Hz |
| **correctness / expressiveness** | `fail` / `pass` |

**Target transcript**

> พวกเขาบอกว่าเป็นความเมตตาแท้ ๆ ที่คอกม้าไม่ถูกไฟไหม้จนย่างสดพวกสัตว์ใบ้ผู้น่าสงสารพวกนั้น

**What an ASR hears** (Whisper, `language=th`)

> พวกเขาบอกว่าเป็นความเมตาแทธ ที่ขออกมาไม่ทุกไฟไหม จนยางสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสสส

The ASR transcription degenerates into a run of repeated characters, which is what a stuck or looping synthesis artefact looks like from the outside.

> [!NOTE]
> **Why this was rejected:** _<!-- TODO: annotator explanation -->_

#### `b-721118-version0`

<audio controls src="examples/correctness_fail/b-721118-version0.flac"></audio>

| | |
|---|---|
| **book** | `book-9e83bfee` |
| **TTS system** | `gemini-2.5-flash-tts` |
| **take** | `version0` |
| **duration** | 31.51 s @ 44100 Hz |
| **correctness / expressiveness** | `fail` / `pass` |

**Target transcript**

> ก็ตอบสนองอย่างฉับไวรุนแรงเสียจนในที่สุด เพื่อความโล่งอกอย่างบอกไม่ถูกของกระผม มันก็ได้ปลุกกระผมให้ตื่นขึ้น และสิ่งใดเล่าที่เป็นต้นตอทำให้เกิดเสียงโกลาหลอึกทึกเช่นนั้น สิ่งใดกันที่สวมบทบาทเป็นยาเบซในเหตุการณ์ทะเลาะวิวาทครั้งนี้ เพียงแค่กิ่งของต้นสนฟิร์ที่กระทบกับหน้าต่างบานเกล็ดยามที่ลมพายุพัดหวนคร่ำครวญ และทำให้ลูกสนแห้งๆ ของมันรัวกระทบกับบานกระจก!

**What an ASR hears** (Whisper, `language=th`)

> ก็ตอบสนองอย่าง ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ ๆ

A 31.5 s take whose ASR output collapses into repeated ๆ — the longest segment in this sample set.

> [!NOTE]
> **Why this was rejected:** _<!-- TODO: annotator explanation -->_

#### `b-721381-version2`

<audio controls src="examples/correctness_fail/b-721381-version2.flac"></audio>

| | |
|---|---|
| **book** | `book-9e83bfee` |
| **TTS system** | `gemini-2.5-flash-tts` |
| **take** | `version2` |
| **duration** | 1.61 s @ 44100 Hz |
| **correctness / expressiveness** | `fail` / `pass` |

**Target transcript**

> บทที่ 4

**What an ASR hears** (Whisper, `language=th`)

> บอดทิสี

A chapter heading. Numerals are a recurring weak point: the script is *บทที่ 4*.

> [!NOTE]
> **Why this was rejected:** _<!-- TODO: annotator explanation -->_

#### `b-865574-version0`

<audio controls src="examples/correctness_fail/b-865574-version0.flac"></audio>

| | |
|---|---|
| **book** | `059-fairy-tales-grimms-fairy-tales-brothers-grimm` |
| **TTS system** | `gemini-2.5-flash-tts` |
| **take** | `version0` |
| **duration** | 2.52 s @ 44100 Hz |
| **correctness / expressiveness** | `fail` / `pass` |

**Target transcript**

> ” ฮันส์ตอบ “ไปหาเกรเทล

**What an ASR hears** (Whisper, `language=th`)

> handstop ไปหา Great Hill

Proper nouns crossing script boundaries (ฮันส์ / เกรเทล) in Grimms' Fairy Tales.

> [!NOTE]
> **Why this was rejected:** _<!-- TODO: annotator explanation -->_


---

## ⚠️ Expressiveness failures

#### `b-727547-version1`

<audio controls src="examples/expressiveness_fail/b-727547-version1.flac"></audio>

| | |
|---|---|
| **book** | `book-9e83bfee` |
| **TTS system** | `gemini-2.5-flash-tts` |
| **take** | `version1` |
| **duration** | 2.73 s @ 44100 Hz |
| **correctness / expressiveness** | `pass` / `fail` |

**Target transcript**

> บทที่ 23

Measured at **2.6 characters/second** against a dataset median of 10.5 — the slowest expressiveness failure in the test split.

> [!NOTE]
> **Why this was rejected:** _<!-- TODO: annotator explanation -->_

#### `b-814590-version0`

<audio controls src="examples/expressiveness_fail/b-814590-version0.flac"></audio>

| | |
|---|---|
| **book** | `022-classic-the-scarlet-letter-nathaniel-hawthorne` |
| **TTS system** | `gemini-2.5-flash-tts` |
| **take** | `version0` |
| **duration** | 5.75 s @ 44100 Hz |
| **correctness / expressiveness** | `pass` / `fail` |

**Target transcript**

> ไม่ต้องการให้กระผมช่วยบำรุงขวัญและพละกำลัง เพื่อให้พร้อมสำหรับการเทศนาเลือกตั้งของท่านหรือขอรับ"

Measured at **16.5 characters/second**, the fastest band in the test split.

> [!NOTE]
> **Why this was rejected:** _<!-- TODO: annotator explanation -->_

#### `b-809740-version0`

<audio controls src="examples/expressiveness_fail/b-809740-version0.flac"></audio>

| | |
|---|---|
| **book** | `022-classic-the-scarlet-letter-nathaniel-hawthorne` |
| **TTS system** | `gemini-2.5-flash-tts` |
| **take** | `version0` |
| **duration** | 8.52 s @ 44100 Hz |
| **correctness / expressiveness** | `pass` / `fail` |

**Target transcript**

> ต้องเผชิญกับภาพความผิดบาปและความอัปยศของเพื่อนมนุษย์ก่อนที่สังคมจะเน่าเฟะจนถึงขั้นหัวเราะเยาะ

Contains a **1.3 s** mid-clip pause.

> [!NOTE]
> **Why this was rejected:** _<!-- TODO: annotator explanation -->_

#### `b-767841-version1`

<audio controls src="examples/expressiveness_fail/b-767841-version1.flac"></audio>

| | |
|---|---|
| **book** | `018-classic-silas-marner-george-eliot` |
| **TTS system** | `gemini-2.5-flash-tts` |
| **take** | `version1` |
| **duration** | 23.24 s @ 44100 Hz |
| **correctness / expressiveness** | `pass` / `fail` |

**Target transcript**

> หล่อนชะงักไปในทันที เพราะก๊อดฟรีย์กำลังวางหมวกของเขาลงด้วยมืออันสั่นเทา ก่อนจะหันมาหาหล่อนด้วยใบหน้าซีดเผือด 
และจ้องมองด้วยสายตาว่างเปล่าชอบกล ราวกับเขาเห็นหล่อนก็จริง ทว่าเห็นเป็นเพียงส่วนหนึ่งของภาพที่หล่อนไม่อาจมองเห็นได้

A 23.2 s take containing **five** pauses longer than 0.35 s.

> [!NOTE]
> **Why this was rejected:** _<!-- TODO: annotator explanation -->_


---

## ✅ Accepted takes

For contrast, four segments that passed both dimensions — two in-domain, two out-of-domain.

#### `b-832158-version0`

<audio controls src="examples/pass/b-832158-version0.flac"></audio>

| | |
|---|---|
| **book** | `001-mystery-the-adventures-of-sherlock-holmes-arthur-conan-doyle` |
| **TTS system** | `gemini-2.5-flash-tts` |
| **take** | `version0` |
| **duration** | 4.83 s @ 44100 Hz |
| **correctness / expressiveness** | `pass` / `pass` |

**Target transcript**

> แม็กคาร์ธีผู้พ่อต้องจบชีวิตลงด้วยน้ำมือของแม็กคาร์ธีผู้ลูกน่ะสิ

> [!NOTE]
> **Why this was rejected:** _<!-- TODO: annotator explanation -->_

#### `b-720989-version0`

<audio controls src="examples/pass/b-720989-version0.flac"></audio>

| | |
|---|---|
| **book** | `book-9e83bfee` |
| **TTS system** | `gemini-2.5-flash-tts` |
| **take** | `version0` |
| **duration** | 6.27 s @ 44100 Hz |
| **correctness / expressiveness** | `pass` / `pass` |

**Target transcript**

> แต่เดี๋ยวนี้แค่หลุดเสียงหัวเราะคิกคักนิดเดียวก็พอแล้วที่จะถูกไล่ตะเพิดไปเข้ามุม

> [!NOTE]
> **Why this was rejected:** _<!-- TODO: annotator explanation -->_

#### `o-848451-version1`

<audio controls src="examples/pass/o-848451-version1.flac"></audio>

| | |
|---|---|
| **book** | `018-classic-silas-marner-george-eliot` |
| **TTS system** | `cartesia/sonic` |
| **take** | `version1` |
| **duration** | 4.48 s @ 44100 Hz |
| **correctness / expressiveness** | `pass` / `pass` |

**Target transcript**

> แหม ก็ต้องเป็นหนูสิคะพ่อ” เอปปี้ตอบกลั้วเสียงหัวเราะ

> [!NOTE]
> **Why this was rejected:** _<!-- TODO: annotator explanation -->_

#### `o-729203-version1`

<audio controls src="examples/pass/o-729203-version1.flac"></audio>

| | |
|---|---|
| **book** | `book-9e83bfee` |
| **TTS system** | `cartesia/sonic` |
| **take** | `version1` |
| **duration** | 5.46 s @ 44100 Hz |
| **correctness / expressiveness** | `pass` / `pass` |

**Target transcript**

> และมีอยู่จริงท่ามกลางพวกเรา! วันที่หล่อนถูกฝัง หิมะตกลงมา

> [!NOTE]
> **Why this was rejected:** _<!-- TODO: annotator explanation -->_


---

## Reference audio

Every segment ships with a reference anchor: up to 60 s of **accepted** takes from the same chunk —
same book, same voice, same recording session. It is not a recording of the same words. It gives an
evaluator a "what correct sounds like here" baseline, and in our experiments it turns out to be load-bearing
for correctness rather than for expressiveness.

<audio controls src="examples/reference/chunk_0046_ref.flac"></audio>

_Reference anchor for `b-764881-version0` (Silas Marner)._

---

## Loading

```python
from datasets import load_dataset

ds = load_dataset("sonar-audiobooks/BELUGA")
print(ds["test"][0]["transcript_text"])
```

---

## Reference

```bibtex
<!-- TODO: citation -->
```
