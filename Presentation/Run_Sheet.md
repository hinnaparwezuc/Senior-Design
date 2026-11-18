# Presentation Plan

**Project:** AI-Powered Assessment Tool for Co-op Reflections (Team ReFrame, CEAS Co-op Reflection System)
**Team:** Aradana Nair, Maria Malik, Priyanshi Kulshrestha, Hinna Parwez, Mbathio Lo
**Advisors:** Dr. David Reeping, Dr. Cedrick Kwuimy
**Deck:** `Senior_Design_Progress.pptx` — slides 1 to 11 are timed, slides 12 to 18 are backup

---

## 1. Slide outline with speakers and timing

Time limit: 10 minutes (600 seconds). Each member speaks 120 seconds. Slide numbers are the position in the PPTX file.

| Slide | Title | Content cues | Visual | Speaker | Seconds |
| --- | --- | --- | --- | --- | --- |
| 1 | Senior Design Progress (title) | Project name and team. Purpose: a more personalized and meaningful co-op reflection process. State the goal in one sentence. | Title slide | Aradana Nair | 20 |
| 2 | Team and Advisors | Five names, both advisors, team contact emails. | Names and contact list | Aradana Nair | 20 |
| 3 | Project Abstract | Read the abstract. Stress the key terms: guided questions, adaptive AI, fixed prompts, authorized reflections. | Abstract text with highlights | Aradana Nair | 60 |
| 4 | User Stories | Four stories: students reflect, advisors review, supervisors submit separate evaluations, IT protects access. Say that supervisors cannot read reflections. | Four story cards | Mbathio Lo | 75 |
| 5 | D0: Proposed System Overview | Walk the diagram: web front end, access control, reflection / summary / evaluation services, UC sign-in and records, PostgreSQL, fixed prompts, adaptive AI. Every read goes through access control. | D0 block diagram | Hinna Parwez | 100 |
| 6 | Division of Work | Each member says their own row in about 20 seconds, in slide order: Hinna, Mbathio, Priyanshi, Maria, Aradana. | Five owner cards | All five (20 each) | 100 |
| 7 | Major Project Constraints | Six constraints: protect student writing, restrict reads by ownership, AI guides but does not grade, approved hosting and AI terms, synthetic data until approval, CEAS co-op scope only. Name one trade-off. | Six constraint lines | Maria Malik | 50 |
| 8 | Project Progress | Interviews complete, feedback synthesis next. Task list and Gantt chart ready. Technical sketches started. Integration decisions still pending. | Five status lines | Maria Malik | 50 |
| 9 | End-of-Term Targets | Four targets: sign-in prototype and wireframes, resolve CEAS and IT questions, record work and remaining tasks, confirm the spring plan. | Four target lines | Priyanshi Kulshrestha | 35 |
| 10 | Expected Expo Demo | Six steps (see section 3) and what plays if the demo fails. | Six-step list | Priyanshi Kulshrestha | 65 |
| 11 | Questions and Feedback | Thank the audience, invite questions. | Closing slide | Mbathio Lo | 25 |
| | | | | **Total** | **600** |

### Time per speaker

| Speaker | Slides | Seconds |
| --- | --- | --- |
| Aradana Nair | 1, 2, 3, and her part of 6 | 20 + 20 + 60 + 20 = 120 |
| Mbathio Lo | 4, 11, and her part of 6 | 75 + 25 + 20 = 120 |
| Hinna Parwez | 5 and her part of 6 | 100 + 20 = 120 |
| Maria Malik | 7, 8, and her part of 6 | 50 + 50 + 20 = 120 |
| Priyanshi Kulshrestha | 9, 10, and her part of 6 | 35 + 65 + 20 = 120 |

**Backup slides (not timed):** 12 Student Interviews · 13 What We Are Learning · 14 Meeting with Samantha Groh · 15 Technical Sketches · 16 Progress So Far (table) · 17 Open Questions · 18 Next Steps

### Hand-offs

- Aradana to Mbathio (after slide 3): "Mbathio will go through our user stories."
- Mbathio to Hinna: "Hinna will walk through the system design."
- Hinna to everyone (slide 6): "Next, each of us will say what we own."
- After the last person on slide 6: "Maria will cover our constraints and progress."
- Maria to Priyanshi: "Priyanshi will cover our term-end targets and the Expo demo."
- Priyanshi to Mbathio: "Mbathio will close and take questions."

---

## 2. Recording plan

| Item | Plan |
| --- | --- |
| Who presents | All five members, in the order and times in section 1 |
| Recording software | Zoom, recording on the local computer (PowerPoint Record Slide Show is the backup) |
| Target length | 10 minutes, no longer than the 600 seconds above |
| Where the link lives | One shared UC folder, with "anyone with the link can view" turned on |
| Who posts the links | Aradana Nair posts the deck and video links to the team spreadsheet in Week 13 |
| Test recording | Maria Malik runs a short test recording by 2026-11-20 to check audio and screen sharing |
| Screen sharing | Aradana advances the slides, and each speaker says "next slide" when done |
| Rehearsal | One full timed run with all five members before the final recording. If a section runs over, cut words, not font size. |

### Recording checklist

- Quiet room and headset for every speaker
- Close other apps and turn off notifications
- Check that the PDF and PPTX of the final deck open correctly
- Watch the first 30 seconds of the recording before sharing the link

---

## 3. Expo demo and fallback

What the Expo demo shows (from slide 10):

1. A student completes a guided reflection.
2. AI follow-up questions appear, using approved or simulated output.
3. With the AI turned off, fixed prompts keep the session working.
4. The assigned advisor reviews the reflection and its extracts.
5. A supervisor submits a separate evaluation.
6. A different student tries to open that reflection and is denied.

The demo uses synthetic data and mock identities, so it does not depend on university IT systems or approvals in the Expo room. Mbathio Lo runs the demo.

**If the demo fails:** play the 90-second recording of the same six steps, with a screenshot of each step ready on the demo laptop. Mbathio Lo records the fallback video by 2026-11-20 and keeps a copy on a USB drive and in the shared folder.

---

## 4. Open items

| Item | Owner | Expected close date |
| --- | --- | --- |
| Fix the deck issues listed in the reply (slide order, title goal statement, Samantha Groh status, email order, name spelling) | Aradana Nair | 2026-11-20 |
| Test recording to check audio | Maria Malik | 2026-11-20 |
| Record the 90-second fallback demo video | Mbathio Lo | 2026-11-20 |
| Each member practices their own part against the seconds above | All members | 2026-11-21 |
| Timed full run with all five members | Aradana Nair | 2026-11-22 |
| Post deck and video links to the team spreadsheet (check the exact Week 13 due date on Canvas) | Aradana Nair | 2026-11-24 |

---

## AI use

Claude helped draft the wording and layout of this plan. The team reviews and edits all content, and each member confirms their own AI use before submitting.
