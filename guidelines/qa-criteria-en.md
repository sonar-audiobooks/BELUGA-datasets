# Audiobook QA Criteria for Narration Books (v2) — English

> **This document is an English translation of the Thai original**, [`qa-criteria-th.md`](qa-criteria-th.md).
> The Thai version is the authoritative text: it is the guideline the QA team actually worked from, and
> it is the same guideline used to label every segment in this dataset. Where the translation and the
> original disagree, the original wins.
>
> **Note:** the **Consistency** section of the original guideline has been removed from both files. It
> is assessed at book/chunk level rather than per segment, so it was not used to label this dataset.
>
> Thai script lines are kept verbatim — the defects are tone-, spelling- and particle-specific and do
> not survive translation. English glosses are given in square brackets where they help.

---

### **Overview of the QA process**

QA is split into two stages so that the audio is checked before delivery:

* **Part-time QA:** performs the initial pass over the whole draft, checking and correcting it.
* **Lead QA (spot check):** once Part-time QA is finished, Lead QA spot-checks **10% of the total audio
  duration**. The sampling is not one continuous listen — it is short passages scattered across the
  whole file so that the 10% is spread out.
* **Threshold parameter:** we use **`<sampled_minutes>`** as the standard for counting defects. The
  underlying idea is that in 1 minute of audio there should be **no more than 1 defect on average**.

**Inspection approach and reject criteria for Lead QA:**

* **Collect comments first.** Because the target is 0 errors, if Lead QA hears a defect while spot
  checking — even in the very first sentence — **do not stop and reject immediately.** Keep sampling for
  a while longer so the comments are as complete as possible, so the person fixing the file can see the
  other defects too and repair everything in one round.
* **Cut-off for stopping immediately.** If during the spot check the accumulated serious defects reach
  **`<sampled_minutes>/2` occurrences** (e.g. a 10-minute spot check that has already turned up 5 defects
  early on), Lead QA may **stop checking and reject the file straight away without listening to the
  rest** — the file is considered too broken to be worth finishing.

---

1. **Pronunciation & Text Accuracy**

   **Primary target (zero tolerance): there must be no defects at all in this category (0 errors).**
   A passing audio file must be pronounced correctly and follow the script 100%.

   Pass/fail breakdown:

   1. **Repeated word or syllable**

      A syllable or word from the script is read more than once. This mostly comes from a Regenerate
      where the audio was not fully trimmed. Fix it with Trim Audio and adjust the audio range.

      Script: “ครั้งแล้วครั้งเล่าที่ข้าพเจ้าตั้งปณิธานแน่วแน่”
      [Time and again I resolved firmly…]

      [Correct example](https://drive.google.com/file/d/10WOzyDyjs9uIobIQosd986PT0bZ7zM2j/view?usp=drive_link) ครั้งแล้วครั้งเล่าที่ข้าพเจ้าตั้งปณิธานแน่วแน่

      [Incorrect example](https://drive.google.com/file/d/1Li_UT-Uz3fHk8fWxYfylyzak5KSbqV4w/view?usp=sharing) ครั้งแล้วครั้งเล่าที่ข้าพเจ้าตั้งปณิธานแน่วแน่ **แน่** — the final syllable is read twice.

   2. **Skipped syllable or word**

      A syllable or word from the script is skipped. This can come from the AI not understanding the
      spelling, or from a Regenerate. Fix it by prompting or regenerating.

      Script: “ครั้งแล้วครั้งเล่าที่ข้าพเจ้าตั้งปณิธานแน่วแน่”

      [Correct example](https://drive.google.com/file/d/10WOzyDyjs9uIobIQosd986PT0bZ7zM2j/view?usp=drive_link) ครั้งแล้วครั้งเล่าที่ข้าพเจ้าตั้งปณิธานแน่วแน่

      [Incorrect example](https://drive.google.com/file/d/1wetlWX2fqdy0SxABssDZP_uDVv5-PGE8/view?usp=sharing) ครั้งแล้ว\_\_ที่ข้าพเจ้าตั้งปณิธานแน่วแน่ — “ครั้งเล่า” is missing.

   3. **Misread spelling**

      A word is read with the wrong spelling relative to the script. This can come from the AI not
      understanding the spelling, or from a Regenerate. Fix it by prompting or regenerating.

      Script: “แหม จะกล่าวเช่นนั้นก็คงเกินไปหน่อย”
      [Oh, to say such a thing would be going a bit far.]

      [Correct example](https://drive.google.com/file/d/1r2EagFQ2DwLa4Hy6esJLUQs8gZ5yfLMj/view?usp=sharing) แหม จะกล่าวเช่นนั้นก็คง**เกิน**ไปหน่อย

      [Incorrect example](https://drive.google.com/file/d/1OTE5aatb1TzfQksQCZyGabyCnHrTK8Ju/view?usp=sharing) แหม จะกล่าวเช่นนั้นก็คง**เกิด**ไปหน่อย — “เกิน” (excessive) read as “เกิด” (to be born).

   4. **Wrong tone**

      A word is read with the wrong tone relative to the script. This can come from the AI not handling
      the tone marks, or from a Regenerate. Fix it by prompting or regenerating.

      Script: “ครั้งแล้วครั้งเล่าที่ข้าพเจ้าตั้งปณิธานแน่วแน่”

      [Correct example](https://drive.google.com/file/d/10WOzyDyjs9uIobIQosd986PT0bZ7zM2j/view?usp=drive_link) ครั้งแล้วครั้งเล่าที่ข้าพเจ้าตั้งปณิธาน**แน่ว**แน่

      [Incorrect example](https://drive.google.com/file/d/1R90wpp0G_BnZOek133iEAMlsDl832a8X/view?usp=sharing) ครั้งแล้วครั้งเล่าที่ข้าพเจ้าตั้งปณิธาน**แนว**แน่ — “แน่ว” read without its tone as “แนว”.

   5. **Reading the mai yamok (ๆ) repetition mark**

      The mai yamok (ๆ) is read as a repetition of the wrong word or syllable. This can come from the AI
      not understanding the mark: it may not read the repetition at all, or repeat more than twice.
      Choose the reading of the mai yamok (ๆ) that fits the context.

      Script: “รายละเอียดอื่นๆ ยังต้องนำมาประกอบกันอีก”
      [Other details still have to be put together.]

      [Correct example](https://drive.google.com/file/d/1Q1OFkgIlB9FkNdLRwf823jh2zGXQOJ7h/view?usp=sharing) รายละเอียด**อื่นอื่น** ยังต้องนำมาประกอบกันอีก — “อื่น” correctly repeated for the ๆ.

      [Incorrect example](https://drive.google.com/file/d/1bbloipwxskgm04OHs8zn0fvCoUxKimiQ/view?usp=sharing) รายละเอียด**อื่น** ยังต้องนำมาประกอบกันอีก — the ๆ is not read.

   6. **Reading text in parentheses**

      Content in parentheses ( ) must not be read twice. If it is duplicated, read it only once.

      Script: “มิสมอร์สแตน (Morstan) ห่อหุ้มร่างกายด้วยเสิ้อคลุมสีเข้ม”
      [Miss Morstan was wrapped in a dark cloak.]

      [Correct example](https://drive.google.com/file/d/1eARWTo22K0xXqqlLXG_9W967Wa7aghcI/view?usp=sharing)

      [Incorrect example](https://drive.google.com/file/d/1F6WdAQp3x64HsNZlbewR2AOhbh3-gnK1/view?usp=sharing) มิสมอร์สแตน (Morstan) ห่อหุ้มร่างกายด้วยเสิ้อคลุมสีเข้ม — the name is read both from the Thai transliteration and from the parenthesised English.

---

2. **Tone, Pacing & Context**

   **Assessment criterion:** at most **`<sampled_minutes>` defects** (an average of no more than 1 defect
   per minute sampled).

   1. **Reading speed**

      Caused by a Regenerate. Fix it with the speed control: slide right to read faster, slide left to
      read slower. If it cannot be made to sound natural and the delivery still sounds stiff, prompt or
      regenerate.

      ![Silence After Segment / Speed controls in the narration editor](images/editor-silence-speed.png)

      [Correct example](https://drive.google.com/file/d/10WOzyDyjs9uIobIQosd986PT0bZ7zM2j/view?usp=drive_link) “ครั้งแล้วครั้งเล่าที่ข้าพเจ้าตั้งปณิธานแน่วแน่”

      [Incorrect example](https://drive.google.com/file/d/16jVv5K-tp_EuilTWaOZZVosgG23ZIF4j/view?usp=sharing): too fast &nbsp;·&nbsp;
      [Incorrect example](https://drive.google.com/file/d/11Heb-P1-U59AsYvGJ3rBp0dhgRu3h6A1/view?usp=sharing): too slow

   2. **Gap between two segments (pause length)**

      Caused by Silence After Segment being set too low or too high. Fix it by adjusting that control:
      moving it left (smaller) makes the gap between segment 1 and segment 2 too short; moving it right
      makes the pause after segment 1 too long before segment 2 continues. **Caution:** keep checking the
      gaps — sometimes Silence After Segment is set to the same value but, listening to how the segments
      join up overall, the perceived gaps still differ.

      ![Two consecutive segments in the narration editor](images/editor-two-segments.png)
      ![Silence After Segment / Speed controls in the narration editor](images/editor-silence-speed.png)

      “ข้าพเจ้ากล่าวอย่างกระตือรือร้น” — segment 1
      “หากผมพอจะมีประโยชน์ใดๆ ได้บ้าง” — segment 2
      [“I said eagerly,” / “if I might be of any use…”]

      [Normal example](https://drive.google.com/file/d/1togxlMITR9RwRfVK1zentYvFlQMDWRqS/view?usp=sharing) — segment 1 Silence After Segment 0.3 (normal)

      Too long: [Incorrect example](https://drive.google.com/file/d/1togxlMITR9RwRfVK1zentYvFlQMDWRqS/view?usp=sharing) — segment 1 Silence After Segment 0.9 (gap too long)

      Too short: [Incorrect example](https://drive.google.com/file/d/1HJd2wNgEnNynvHn919t1LeNc0kFD4ZwA/view?usp=sharing) — segment 1 Silence After Segment 0.18 (gap too short)

   3. **Delivery does not match the context**

      Caused by the AI not understanding how the line should be read, or by a Regenerate producing a
      delivery whose emotion does not match the context. Fix it by prompting or regenerating. This splits
      further into:

      **Narration**

      “จากอิตาลี พวกท่านเดินทางไปเยือนเยอรมณีและฝรั่งเศส”
      [From Italy, you travelled on to Germany and France.]
      [Normal example](https://drive.google.com/file/d/1bzzv-2zC306mBZlbFX2FC-vMmH1sZrwX/view?usp=sharing)
      [Incorrect example](https://drive.google.com/file/d/1fd0dyV3a83gIk9ogo6aRtwyuGboi-eAC/view?usp=sharing)

      **Dialogue**

      “มิตรสหายเช่นนั้นจะช่วยแก้ไขข้อบกพร่องของพี่ชายผู้น่าสงสารของเธอได้มากเพียงใดหนอ”
      [How much such a friend might do to mend the faults of her poor brother.]
      [Normal example](https://drive.google.com/file/d/12HrivAp-j76TRNJFQ_5ZKNqvcLwKpofI/view?usp=sharing)
      [Incorrect example](https://drive.google.com/file/d/1RxaCMQr1NeqjW_SDG4gyLTerHmwt3XMl/view?usp=sharing) (emotion pushed beyond the context)

   4. **Emotional delivery and tone of voice**

      Caused by the AI not understanding how the line should be read, or by a Regenerate producing a
      delivery whose emotion and tone are off. Fix it by prompting or regenerating. This splits further
      into:

      [Normal example](https://drive.google.com/file/d/1Yrw4VCVZ8aIs8cTYdYxplWR8CrdFOGiY/view?usp=sharing) “ท่ามกลางฝูงชนอันมืดครึ้ม “ช่างเป็นสตรีที่เปี่ยมเสน่ห์เสียนี่กระไร\!\!” ข้าพเจ้าร้องอุทาน พลางหันไปหาเพื่อนร่วมห้อง”
      [Amid the gloomy crowd — “What a charming woman!” I exclaimed, turning to my room-mate.]

      i. **Flat, emotionless voice** (completely monotone)

      [Incorrect example](https://drive.google.com/file/d/1OCxNoIU5gzT_x-q2WyruGFJ-mUj1NCn1/view?usp=sharing)

      ii. **Overacting** (emotion pushed past what the line calls for)

      [Incorrect example](https://drive.google.com/file/d/1UeuyQyy3Uq94ivOEW-d8SahIfj1n4Yrs/view?usp=sharing)

   5. **Stress and word emphasis**

      Caused by the AI not understanding how the line should be read, or by a Regenerate producing a
      delivery that emphasises a word in a way that conflicts with the context of the same segment. Fix
      it by prompting or regenerating. This splits further into:

      i. **Emphasis on the wrong word**

      [Normal example](https://drive.google.com/file/d/1e40dpbVRy06w6JVuJhwlIAD_5zvL7CNv/view?usp=sharing) “โดยให้ที่อยู่คือโรงแรมแลงแฮม”
      [giving the address as the Langham Hotel]

      [Incorrect example](https://drive.google.com/file/d/1SDhtf99vtWC_OIdYI7_oQ6opuxGldAYB/view?usp=sharing)

      ii. **Stiff, unnatural voice** (pitch contour off — sounds like a robot reading a book)

      [Normal example](https://drive.google.com/file/d/16riHQInBqCRinuiQ5rRXMoFpxg53XE6L/view?usp=sharing) “หากเขาไม่ได้เพลิดเพลินไปกับพรอันประเสริฐข้อนี้”
      [if he did not enjoy this excellent blessing]

      [Incorrect example](https://drive.google.com/file/d/1aUUFy1aknmtd9UjwXitRneGzOpTzJBAQ/view?usp=sharing)

   6. **Audio cut off before the end of the sentence**

      Caused by Trim Audio cutting the segment exactly at its tail, so the audio is clipped and the
      listen feels like it stumbles. Fix it with Trim Audio and adjust the audio range.

      “แผนการของข้าพเจ้า ซึ่งข้าพเจ้าก็เปิดเผยต่อเขาโดยมิได้ปิดบัง”
      [my plan, which I disclosed to him without concealment]

      [Normal example](https://drive.google.com/file/d/1H_5GOugDBWvCiLmOoJ82XusQFTJbSEuA/view?usp=sharing)
      “แผนการของข้าพเจ้า ซึ่งข้าพเจ้าก็เปิดเผยต่อเขาโดยมิได้ปิดบัง”

      [Incorrect example](https://drive.google.com/file/d/1_wcNLqsninnGZ7dOfk40Ok7EN4wXutQw/view?usp=sharing)

   7. **Several unnatural pauses inside the read**

      Caused by the AI mis-reading the line, or by a Regenerate. Fix it by prompting or regenerating
      until the pausing sounds natural.

      “ท่านคงไม่ทำเช่นนั้นแน่หากท่านได้พบเขา ท่านได้รับการอบรมสั่งสอนและขัดเกลามาด้วยตำรา”
      [You would surely not do so had you met him; you were brought up and refined by books.]
      [Normal example](https://drive.google.com/file/d/11dvgpBizr3ywtIXaijnPBAN5FkqyAw-2/view?usp=sharing)

      “ท่านคงไม่ทำเช่นนั้นแน่\_หากท่าน\_ได้พบเขา ท่านได้รับการอบรมสั่งสอนและขัดเกลา\_มาด้วยตำรา”
      [Incorrect example](https://drive.google.com/file/d/1ci0nKDMLOqit3ZvUpqu3In8Cvq7yW03K/view?usp=sharing) — pauses (\_) inserted where the phrase should run on.
