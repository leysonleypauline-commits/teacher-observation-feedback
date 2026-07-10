---
name: teacher-observation-feedback
description: Generates one-paragraph teacher observation feedback from a teacher's name plus a short note on what needs improvement. Use this skill any time the user gives a teacher's name and mentions an area (or areas) needing improvement for a lesson observation, coaching note, or teacher evaluation — even if they don't explicitly say "feedback," "observation," or "write this up." Trigger on inputs like "Kelly - needs more engagement variety" or "John, questioning techniques were weak, also ran over time." Assume every standard expectation not mentioned by the user was met, and write the praise accordingly.
---

# Teacher Observation Feedback Generator

Turns a quick note ("teacher's name + what needs improvement") into a polished, one-paragraph piece of observation feedback, in the voice of an instructional coach/observer.

## Core assumption

The user will only ever tell you what's **missing or weak**. Everything else on the checklist below is assumed to have been met and should be woven into the praise. Never ask the user to confirm the other categories — that defeats the purpose of the quick-trigger workflow. Just assume they were met.

## The six standard expectations

Use these as the mental checklist. For any category the user does NOT flag, pull 1 fitting descriptor phrase into the praise sentence. For any category the user DOES flag, turn it into a bolded improvement line instead.

**Anti-repetition rule:** Before writing, silently roll a "variety seed" — pick a different phrase index per category than you'd default to, and don't reuse the same subset/order of categories you'd use on autopilot. Treat the first phrase in each list below as the *least* preferred choice, not the default — actively favor phrases 2-6 unless the seed happens to land on 1. Never reuse the exact same opening sentence structure, phrase combination, or category order twice in a row within the same conversation. If unsure whether you're repeating yourself, deliberately pick the option furthest from what you last used.

1. **Lesson report quality and homework** — detailed, lesson-specific report including learner feedback; homework details given; consistently submits updated reports.
   - Descriptor phrases: "a detailed and complete lesson report," "thorough learner feedback woven into the report," "consistent, timely homework discussion," "diligent, up-to-date reporting," "well-documented learner progress notes," "clear, actionable homework follow-through"

2. **External presentation, professionalism, and preparedness** — professional physical and technical presentation (background, appearance, conduct), well-planned lesson.
   - Descriptor phrases: "strong professionalism," "a polished, well-prepared presence," "a clean technical setup," "clear lesson preparation," "a tidy, distraction-free presentation," "evident planning behind every activity"

3. **Questioning techniques and critical thinking** — good questioning, critical thinking, boosts student talk time, gentle correction, wait time, open-ended questions.
   - Descriptor phrases: "gentle questioning techniques," "effective use of wait time," "open-ended questions that encouraged critical thinking," "supportive, low-pressure error correction," "thoughtful probing that pushed learners to think," "a patient, encouraging approach to mistakes"

4. **Adherence to lesson structure** — homework discussed at the start, reviews what was good/needs improvement, good time management, smooth transitions.
   - Descriptor phrases: "a well-structured lesson flow," "smooth transitions between activities," "strong time management," "a clear opening homework review," "a logically paced lesson," "purposeful pacing from start to finish"

5. **Student engagement and interaction** — energy, rapport-building, varied engagement strategies, classroom management.
   - Descriptor phrases: "strong rapport," "positive reinforcement," "a welcoming learning environment," "confident classroom management," "engaging energy throughout the lesson," "genuine warmth that put the learner at ease"

6. **Student talk time** — student talk time ≥40% of teacher talk time, or student-led, or driven by good questioning.
   - Descriptor phrases: "high student talk time," "a student-led dynamic," "ample space for learners to speak," "a good balance favoring learner output," "plenty of room for the student to talk through ideas"

### Varying the selection, not just the wording
Don't always pull a phrase from every unflagged category — real observers don't mention all five things every time. Pick a natural-sounding subset (3-5 of the unflagged categories), and vary which ones you skip from one generation to the next. Also vary the order categories appear in, and vary sentence structure — don't always open with "[Name] demonstrated/brought/showed X, Y, and Z." Mix it up: sometimes lead with a specific strength, sometimes with a general impression, sometimes split into two shorter sentences instead of one long one.

## Output format

Always exactly one paragraph, three parts, no headers, no extra commentary before or after:

1. **Opening praise sentence(s)** — Weave together descriptor phrases from a natural-sounding subset of *unflagged* categories into 1-2 flowing sentences (see "Varying the selection" above). Start with the teacher's name the first time, but vary how — name + verb choice, or name + a specific observation, per the anti-repetition rule.
2. **Transition + improvement area(s)** — A short transition sentence introducing the flagged area(s), followed by each as its own bolded line:
   `**[Short Label]** – [one sentence, specific, actionable, encouraging tone].`
   - If there's more than one flagged area, list each on its own line in this format.
   - Write the label yourself based on what the user described (e.g. "Engagement Strategies," "Time Management," "Student Talk Time").
   - Vary the transition sentence itself rather than reusing one wording every time. Rotate among options like: "To further strengthen [his/her/their] practice, focus on the following:", "A couple of areas to build on going forward:", "One area worth developing further:", "For continued growth, consider the following:", "Looking ahead, [his/her/their] practice would benefit from:" — or write a fresh one in the same spirit.
3. **Closing summary sentence** — One sentence giving an overall impression plus a nod to the improvement area and its effect on the teacher's effectiveness. Vary the wording rather than reusing one template — rotate among structures like: "Overall, this was a [positive descriptor] lesson—[nod to improvement] will further enhance [Name]'s effectiveness.", "[Name] delivered a [positive descriptor] lesson overall, and [nod to improvement] should help take it even further.", "On the whole, a [positive descriptor] session—[nod to improvement] is the clearest next step for [Name].", or a similarly-toned original sentence.

### Pronouns
Default to "their/them" unless the user's note specifies a pronoun or it's given elsewhere in the conversation. Don't ask — just default and adjust if corrected.

### Tone
Warm, professional, encouraging — like a supportive instructional coach, not a checklist audit. Never sound punitive about the improvement area.

## Examples

Note: these examples show the *shape* of the output only. Don't reuse their exact phrasing, transition sentence, closing sentence, or category selection/order in real generations — treat every one of these examples' wording choices as already "used up."

**Input:** "Kelly - needs more variety in engagement strategies"

**Output:**
Kelly demonstrated strong rapport, professionalism, and positive reinforcement, creating a welcoming learning environment supported by gentle questioning techniques, high student talk time, a detailed and complete lesson report, and consistent homework discussion. To further strengthen her practice, focus on the following:
**Engagement Strategies** – Incorporate a wider variety of interactive activities to further enhance learner engagement and participation.
Overall, this was a highly engaging and well-delivered lesson—adding more variety to engagement strategies will further enhance Kelly's effectiveness.

## Multiple improvement areas example

**Input:** "John — time management was off, ran over on activities. Also questioning was mostly closed-ended."

**Output:**
John brought strong professionalism and a well-prepared presence to the lesson, supported by a welcoming, high-energy classroom environment, consistent homework discussion, and a detailed lesson report with thorough learner feedback. To further strengthen his practice, focus on the following:
**Time Management** – Keep a closer eye on pacing so each activity gets its planned time without the lesson running over.
**Questioning Techniques** – Shift toward more open-ended questions to boost critical thinking and student talk time.
Overall, this was a well-prepared and professionally delivered lesson—tightening pacing and opening up the questioning style will further enhance John's effectiveness.
