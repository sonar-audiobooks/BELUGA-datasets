# BELUGA: Thai Audiobook Text-to-Speech Quality Assessment Benchmark

Modern TTS is near-human on standard benchmarks, yet production audiobook narration still fails on
subtle defects: a repeated syllable, a mispronounced tone, a pause in the wrong place. These are too
small to move CER and too local to move a MOS score, but a listener hears every one of them. BELUGA
labels those defects with **decision-ready binary labels** collected inside a real narration workflow,
the same keep-or-regenerate call an editor makes in production.

This page walks through **worked examples**: listen, read the script, and compare against the reference
anchor to hear why each take was rejected.

---

## Dataset structure

| split | segments | pass | correctness fail | expressiveness fail | hours |
|---|---|---|---|---|---|
| in-domain train | 7,991 | 4,811 | 1,980 | 1,200 | 13.21 |
| in-domain val | 1,151 | 689 | 289 | 173 | 1.86 |
| in-domain test | 2,295 | 1,362 | 590 | 343 | 3.84 |
| out-of-domain test | 1,380 | 712 | 275 | 393 | 3.32 |

In-domain audio is synthesised entirely with **Gemini 2.5 Flash TTS**. The out-of-domain split is held
out for cross-system generalisation and uses **Gemini 3.1 Flash TTS** and **Cartesia Sonic 3.5**, neither
of which appears in training.

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

The guideline itself ships with this dataset, with its own reference audio for every defect type:

| | |
|---|---|
| [`guidelines/qa-criteria-th.md`](guidelines/qa-criteria-th.md) | the original Thai document the QA team worked from, authoritative |
| [`guidelines/qa-criteria-en.md`](guidelines/qa-criteria-en.md) | English translation of that document |

The summary below is a condensed version of those two files.

### Correctness — pronunciation & text accuracy

1. Repeated words or syllables, for example *…ตั้งปณิธานแน่วแน่ **แน่***
2. Skipped syllables or words, for example *ครั้งแล้ว\_\_ที่ข้าพเจ้า…*
3. Wrong spelling-to-sound, for example *เกิน* read as *เกิด*
4. Wrong tone (วรรณยุกต์), for example *แน่วแน่* read as *แนวแน่*
5. ไม้ยมก (ๆ) must be read as the doubled word exactly once, for example *อื่นๆ → อื่นอื่น*
6. Parenthetical text read once, never duplicated

### Expressiveness — prosody, pacing & context

1. Reading speed too fast or too slow, or a stiff/unnatural pace
2. Inter-segment pause too long or too short
3. Prosody not matching context, for example บทบรรยาย read with dialogue emotion, or vice versa
4. Flat monotone, or overacting beyond the context
5. Wrong word emphasis, or robotic/unnatural pitch
6. Audio cut off before the sentence ends
7. Unnatural mid-sentence pauses, for example *ท่านคงไม่ทำเช่นนั้นแน่\_หากท่าน\_ได้พบเขา*

---

## Correctness failures

5 segments from the in-domain test split, labeled correctness fail by the production QA workflow.
Each ships with the same reference anchor its labeller had access to. See
[Reference audio](#reference-audio) for what that anchor means.

#### Example 1

<audio controls src="examples/correctness_fail/b-854660-version0.flac"></audio>
Reference: <audio controls src="examples/reference/022-classic-the-scarlet-letter-nathaniel-hawthorne_chunk0190_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-2.5-flash-tts` |
| **duration** | 3.65 s |
| **correctness / expressiveness** | `fail` / `pass` |

**Target transcript**

> สามารถพูดคุยกันได้โดยไม่ต้องเสี่ยงต่อการถูกแอบฟัง

**Why this failed:** the audio is incomplete. It skips the word **ต้อง** in the transcript.

---

#### Example 2

<audio controls src="examples/correctness_fail/g-6543-version0.flac"></audio>
Reference: <audio controls src="examples/reference/the-game-of-life-and-how-to-play_chunk0008_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-2.5-flash-tts` |
| **duration** | 2.63 s |
| **correctness / expressiveness** | `fail` / `pass` |

**Target transcript**

> เธอเคยครุ่นคิดที่จะซื้อผ้าห่มผืนใหม่

**Why this failed:** mispronounced tone. **ผ้าห่ม** (low tone) was read as **ผ้าห๊ม** (high tone).

---

#### Example 3

<audio controls src="examples/correctness_fail/b-730243-version1.flac"></audio>
Reference: <audio controls src="examples/reference/book-9e83bfee_chunk0644_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-2.5-flash-tts` |
| **duration** | 13.53 s |
| **correctness / expressiveness** | `fail` / `pass` |

**Target transcript**

> สิ่งที่สารภาพมาไม่ได้ช่วยบรรเทาความรู้สึกฉันได้หรอก แต่เผื่อมันจะช่วยอธิบายสภาพอารมณ์บางอย่างที่ฉันแสดงออกมาซึ่งไม่สามารถหาคำอธิบายอื่นใดได้

**Why this failed:** the audio skips a word. **คำอธิบายอื่นใดได้** comes out as **คำอธิบายอื่นได้**, missing ใด.

---

#### Example 4

<audio controls src="examples/correctness_fail/g-4441-version0.flac"></audio>
Reference: <audio controls src="examples/reference/book-03f435a9_chunk0008_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-2.5-flash-tts` |
| **duration** | 5.11 s |
| **correctness / expressiveness** | `fail` / `pass` |

**Target transcript**

> ทว่าบัดนี้ ข้าพเจ้ามองเห็นทรวดทรงและใบหน้าของนางได้อย่างชัดเจน

**Why this failed:** mispronounced the synonym compound **ทรวดทรง**.

---

#### Example 5

<audio controls src="examples/correctness_fail/b-787999-version0.flac"></audio>
Reference: <audio controls src="examples/reference/059-fairy-tales-grimms-fairy-tales-brothers-grimm_chunk0159_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-2.5-flash-tts` |
| **duration** | 3.18 s |
| **correctness / expressiveness** | `fail` / `pass` |

**Target transcript**

> ความริษยาและความแค้นจึงสุมอก

**Why this failed:** anaptyxis error on **ริษยา**. The correct reading inserts a short vowel (ริด-สะ-ยา), but the take collapses it to ริด-ยา, dropping the epenthetic syllable.

---

## Expressiveness failures

5 segments labeled expressiveness fail. Pronunciation and text are correct, but pacing, pausing, tone,
or emphasis did not meet the production bar. Compare each clip against its reference anchor to hear the
difference in delivery.

#### Example 1

<audio controls src="examples/expressiveness_fail/b-335269-version0.flac"></audio>
Reference: <audio controls src="examples/reference/031-sci-fi-the-time-machine-h-g-wells_chunk0003_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-2.5-flash-tts` |
| **duration** | 10.10 s |
| **correctness / expressiveness** | `pass` / `fail` |

**Target transcript**

> นักเดินทางข้ามเวลา ย้อน "คุณอาจทุ่มเถียงชักแม่น้ำทั้งห้าเพื่อเปลี่ยนสีดำเป็นสีขาวได้" ฟิลบีกล่าว

**Why this failed:** a wrong pause is inserted right at **คุณอาจ**, where the line should read straight through with no break. The whole delivery is also too monotone compared to the reference.

---

#### Example 2

<audio controls src="examples/expressiveness_fail/g-7831-version0.flac"></audio>
Reference: <audio controls src="examples/reference/sherlock-holmes-a-study-in-scarlet2_chunk0003_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-2.5-flash-tts` |
| **duration** | 4.57 s |
| **correctness / expressiveness** | `pass` / `fail` |

**Target transcript**

> เพียงแค่เล่าให้เราฟังในแบบของคุณตามที่มันเกิดขึ้นก็พอ

**Why this failed:** too many pauses. The take breaks as *เพียงแค่ (pause) เล่าให้เราฟังในแบบของคุณ (pause) ตามที่มันเกิด (pause) ขึ้นก็พอ*. It reads more smoothly with a single pause: *เพียงแค่เล่าให้เราฟังในแบบของคุณ (pause) ตามที่มันเกิดขึ้นก็พอ*.

---

#### Example 3

<audio controls src="examples/expressiveness_fail/b-856253-version0.flac"></audio>
Reference: <audio controls src="examples/reference/018-classic-silas-marner-george-eliot_chunk0153_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-2.5-flash-tts` |
| **duration** | 2.79 s |
| **correctness / expressiveness** | `pass` / `fail` |

**Target transcript**

> เอปปี้กล่าว หล่อนคอยระแวดระวังอยู่เสมอ

**Why this failed:** too fast compared to the reference audio.

---

#### Example 4

<audio controls src="examples/expressiveness_fail/b-816990-version0.flac"></audio>
Reference: <audio controls src="examples/reference/022-classic-the-scarlet-letter-nathaniel-hawthorne_chunk0001_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-2.5-flash-tts` |
| **duration** | 7.25 s |
| **correctness / expressiveness** | `pass` / `fail` |

**Target transcript**

> ดูเหมือนว่าข้าพเจ้าจะเจริญรอยตามท่าน "พี. พี. เสมียนประจำเขตวัด

**Why this failed:** the abbreviation **"พี. พี. เสมียนประจำเขตวัด"** is read too slowly and robotically.

---

#### Example 5

<audio controls src="examples/expressiveness_fail/b-832120-version7.flac"></audio>
Reference: <audio controls src="examples/reference/001-mystery-the-adventures-of-sherlock-holmes-arthur-conan-doyle_chunk0060_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-2.5-flash-tts` |
| **duration** | 4.65 s |
| **correctness / expressiveness** | `pass` / `fail` |

**Target transcript**

> มีข่าวร้ายเมื่อเช้านี้ เลสเตรดตั้งข้อสังเกต

**Why this failed:** too slow compared to the reference audio.

---

## Accepted takes

5 segments that passed both dimensions, drawn from the same in-domain test split as the failures above,
so you can hear what an acceptable take sounds like on the same books and voices.

#### Example 1

<audio controls src="examples/pass/b-722667-version1.flac"></audio>
Reference: <audio controls src="examples/reference/book-9e83bfee_chunk0148_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-2.5-flash-tts` |
| **duration** | 8.46 s |
| **correctness / expressiveness** | `pass` / `pass` |

**Target transcript**

> กอดรัดแก้วตาดวงใจไว้แนบ-อก ฮินด์ลี่เดินลงบันไดมาอย่างเชื่องช้า ส่างเมาและรู้สึกละอายแก่ใจ

---

#### Example 2

<audio controls src="examples/pass/g-5673-version0.flac"></audio>
Reference: <audio controls src="examples/reference/the-wonderful-wizard-of-oz_chunk0003_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-2.5-flash-tts` |
| **duration** | 5.61 s |
| **correctness / expressiveness** | `pass` / `pass` |

**Target transcript**

> ทว่าแรงดันมหาศาลของกระแสลมที่โหมกระหน่ำอยู่ทุกด้านของตัวบ้าน

---

#### Example 3

<audio controls src="examples/pass/b-859434-version5.flac"></audio>
Reference: <audio controls src="examples/reference/001-mystery-the-adventures-of-sherlock-holmes-arthur-conan-doyle_chunk0129_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-2.5-flash-tts` |
| **duration** | 3.32 s |
| **correctness / expressiveness** | `pass` / `pass` |

**Target transcript**

> ที่โรงเตี๊ยมเดอะคราวได้อย่างไม่มีปัญหาใด

---

#### Example 4

<audio controls src="examples/pass/g-2714-version0.flac"></audio>
Reference: <audio controls src="examples/reference/book-2b8009de_chunk0004_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-2.5-flash-tts` |
| **duration** | 3.59 s |
| **correctness / expressiveness** | `pass` / `pass` |

**Target transcript**

> ซ้ำยังเป็นนักเคมีชั้นเลิศ แต่เท่าที่ผมรู้

---

#### Example 5

<audio controls src="examples/pass/b-783874-version1.flac"></audio>
Reference: <audio controls src="examples/reference/059-fairy-tales-grimms-fairy-tales-brothers-grimm_chunk0060_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-2.5-flash-tts` |
| **duration** | 4.31 s |
| **correctness / expressiveness** | `pass` / `pass` |

**Target transcript**

> และเมื่อพวกเขาสืบเท้าเข้าไปใกล้บ้านหลังน้อย

---

## Out-of-domain examples

5 segments from the out-of-domain test split, synthesized with TTS systems never seen during
training (`cartesia/sonic` and `gemini-3.1-flash-tts-preview`), mixing pass and fail across both dimensions
and both systems. Each reference clip here is same-system (built from accepted takes of the same
TTS system as the sample), which our own experiments show matters: swapping in a cross-model reference
measurably hurts both correctness and expressiveness agreement.

#### Example 1

<audio controls src="examples/out_of_domain/o-861637-version9.flac"></audio>
Reference (same TTS system): <audio controls src="examples/reference/001-mystery-the-adventures-of-sherlock-holmes-arthur-conan-doyle_chunk0140_cartesia-sonic_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `cartesia/sonic` |
| **duration** | 2.31 s |
| **correctness / expressiveness** | `pass` / `pass` |

**Target transcript**

> และห้ามหลุดปากแก่ผู้ใดเด็ดขาด

---

#### Example 2

<audio controls src="examples/out_of_domain/o-811535-version3.flac"></audio>
Reference (same TTS system): <audio controls src="examples/reference/022-classic-the-scarlet-letter-nathaniel-hawthorne_chunk0099_gemini-3.1-flash-tts-preview_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-3.1-flash-tts-preview` |
| **duration** | 9.13 s |
| **correctness / expressiveness** | `pass` / `pass` |

**Target transcript**

> โรเจอร์ ชิลลิงเวิร์ธ จึงถือเป็นบุคลากรอันล้ำค่าที่ได้มาในเวลาไม่นาน เขาก็แสดงให้เห็นถึงความเชี่ยวชาญ

---

#### Example 3

<audio controls src="examples/out_of_domain/o-788413-version1.flac"></audio>
Reference (same TTS system): <audio controls src="examples/reference/059-fairy-tales-grimms-fairy-tales-brothers-grimm_chunk0168_cartesia-sonic_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `cartesia/sonic` |
| **duration** | 4.08 s |
| **correctness / expressiveness** | `fail` / `pass` |

**Target transcript**

> ข้าจะกลับไปที่บ้านของพ่อไม่ได้อีกแล้ว" พระองค์ตรัส

**Why this failed:** mispronounced with a final consonant aspiration, an added trailing "ti" sound that does not exist for this word in Thai.

---

#### Example 4

<audio controls src="examples/out_of_domain/o-806788-version1.flac"></audio>
Reference (same TTS system): <audio controls src="examples/reference/051-children-alices-adventures-in-wonderland-lewis-carroll_chunk0051_gemini-3.1-flash-tts-preview_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `gemini-3.1-flash-tts-preview` |
| **duration** | 4.14 s |
| **correctness / expressiveness** | `pass` / `fail` |

**Target transcript**

> ไม่ สำคัญพ่ะย่ะค่ะ ฝ่าบาททรงหมายถึงอย่างนั้นแน่นอน

**Why this failed:** too fast compared to the reference audio.

---

#### Example 5

<audio controls src="examples/out_of_domain/o-764759-version11.flac"></audio>
Reference (same TTS system): <audio controls src="examples/reference/018-classic-silas-marner-george-eliot_chunk0042_cartesia-sonic_ref.flac"></audio>

| | |
|---|---|
| **TTS system** | `cartesia/sonic` |
| **duration** | 5.66 s |
| **correctness / expressiveness** | `pass` / `fail` |

**Target transcript**

> "เปล๊า----" ช่างเกือกม้าประชดประชันอย่างขมขื่น พลางกวาดตามองคนทั้งวง

**Why this failed:** too monotone on **"เปล๊า----"**, which is dialogue and needs emotional delivery.

---

## Reference audio

Every segment ships with a reference anchor: up to 60 s of accepted takes from the same chunk,
same book, same voice, same recording session. It is not a recording of the same words. It gives an
evaluator a baseline for what correct sounds like in that context, and in our experiments it turns out
to be load-bearing for correctness rather than for expressiveness. Each example above embeds its own
reference clip inline, directly under the sample being evaluated.

