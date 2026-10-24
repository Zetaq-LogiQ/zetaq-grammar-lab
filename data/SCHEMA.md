## JSON schema — write `banks/cN/<id>.json` (UTF-8, no comments)
```json
{
  "class": 6, "id": "1.1", "title": "<exact lesson title>",
  "topics": [ {"tag": "simple_past", "name": "Simple past tense"} ],
  "questions": [ ...50 objects... ]
}
```
Every question has: `"qid"` ("c<class>-<id>-<NN>" NN = 01..50), `"type"`, `"level"` (1–5), `"topic"` (a tag from
topics), `"rule"` (≤ 160 chars, friendly one-line explanation of WHY, shown after answering; may use <b>..</b>).

### spot — choose the one correct sentence
`{"type":"spot","prompt":"Which sentence is correct?","options":[s1,s2,s3],"answer":1}`
3 options. Exactly one fully correct (grammar AND punctuation). Each wrong option has ONE clear grammar error
(not a style choice). Options similar length; vary the position of the correct answer (0/1/2 evenly).
Prompt may vary: "Which sentence is correct?", "Pick the sentence with the correct tense.", etc.

### fix — red pen: tap the one wrong word, then choose the fix
`{"type":"fix","prompt":"One word is wrong. Find it and fix it.","words":["Yesterday","we","go","to","the","market."],"errors":[{"i":2,"options":["goes","went","gone"],"answer":1}]}`
`words` = the sentence split on spaces; punctuation stays attached to its word. Exactly ONE word is wrong;
replacing words[i] with options[answer] makes the sentence 100 % correct; every other word is already correct
and no other single-word change is the "obvious" fix. The wrong word itself is NOT in options. 3 options, all
real words; the 2 distractors make it still wrong. The wrong word must not carry punctuation (choose an
error word inside the sentence), and must be a single word. Errors: tense, agreement, article, pronoun,
preposition, plural, comparison, modal, conjunction, wrong word form (e.g. quick/quickly) — never spelling.
6–16 words.

### proof — paragraph with 2 or 3 errors (classes 8–10)
Same as fix but `"errors"` has 2–3 entries (sorted by i), 18–45 words, 2–3 sentences in one paragraph.
`"prompt":"Find and fix the 3 errors in this message."` (say the exact count).

### fill — choose the word for the gap
`{"type":"fill","prompt":"Fill in the gap.","before":"There are","after":"apples in the basket.","options":["much","many","a little"],"answer":1}`
Sentence = before + ___ + after. One blank; options 3 (or 4); only one fits grammatically; the others clearly do not
(not just "less natural"). `before` may be "" if the blank starts the sentence (then options are capitalised).

### build — put the tiles in order
`{"type":"build","prompt":"Make a polite request.","answer":["Could","you","please","open","the","window?"],"alts":[]}`
4–10 tiles (classes 5–6: 4–8). The app shuffles them. The answer order must be the ONLY grammatical, natural
sentence from these tiles; if another order is also fully correct, list it in `alts` (prefer to avoid that by
choosing tiles carefully — e.g. avoid free-floating adverbs like "yesterday"/"always" that can move). First tile
capitalised, last tile carries end punctuation. No tile appears twice with identical text.

### chat — choose the best reply in a chat
`{"type":"chat","prompt":"Pick the correct reply.","chat":[["Riya","Are you coming to the science fair tomorrow?"]],"options":["Yes, I coming. We go together?","Yes, I am coming. Shall we go together?","Yes, I am come tomorrow together."],"answer":1}`
1–3 short messages from friends/family/teacher (names given), then 3 possible replies by the student. The correct
reply is grammatical AND a sensible answer to the chat (tense/form matching the question). Wrong replies contain
clear grammar errors (they may also fit the conversation). Chosen by grammar, never by opinion or politeness alone.
Model good everyday conversation: greetings, requests, apologies, invitations, asking the way, phone talk.

### change — transform the sentence (classes 7–10)
`{"type":"change","prompt":"Change into passive voice.","source":"The children planted a tree.","options":["A tree is planted by the children.","A tree was planted by the children.","A tree was plant by the children."],"answer":1}`
Board-style "Do as directed": voice, reported speech, degrees of comparison, affirmative/negative/interrogative,
exclamatory↔assertive, join using …, remove 'too', use 'unless', 'no sooner … than', question tags, begin with …
The prompt states the exact instruction. Exactly one option is the standard, correct transformation keeping the
meaning; the others have a clear error (wrong tense, wrong pronoun shift, broken form).

