# Voice guide: how I talk upstream

<!--
THIS IS THE PART YOU WRITE (new this week). Live mode reads this file
before any comment of yours goes out the door; eval mode ignores it
entirely, because your voice is yours and carries no gold labels.

This is not etiquette. "Be polite and concise" is advice for everyone
and therefore rules for no one. Write rules YOU need, in your own
words, each one concrete enough that the skill can hold a draft
against it and say which rule it breaks.

Three sections. Fill all three.
-->

## Who I am in threads

<!-- 2-3 lines. Who is talking when you comment on an issue: your
experience level stated plainly, what you are doing in this repo, what
readers can expect from you. This is the register your rules protect. -->

I'm a student contributor working through my first real open-source issue.
I'm learning the repo as I go, so my comments ask for confirmation where
I'm unsure rather than asserting I know the codebase. Readers can expect
me to state what I did, what I saw, and what I don't yet know, without
padding.

## Rules I write by

<!-- 3-5 rules, drafted from the lecture's slide-12 moment. Each rule
needs a wrong/right pair from your own hand: one line you might
actually have written that breaks the rule, and the line you would
post instead. The pair is what makes a rule executable; a rule without
one is a wish.

Format each rule like this:

### Rule: <short name>

<The rule, one or two sentences.>

- Wrong: "<a line that breaks it>"
- Right: "<the line to post instead>"
-->

### Rule: Promise, don't assert
On a claim comment, I say what I will do next, not what I have already
done. The repro comment comes later and carries the proof.

- Wrong: "I've reproduced this on Windows 11 and the fix is straightforward."
- Right: "I'll reproduce this on Windows 11 against the current main and post a full report with the environment and output."

### Rule: Show the artifact, not a summary of it

When reporting an error, I paste the exact line or block from the actual
run rather than describing it.

- Wrong: "It throws an error about a missing key."
- Right: "KeyError: 'redis_host' at api/routes/health.py:42"

### Rule: No unrequested timelines

I never promise a fix, a PR, or a date in a claim comment. The report is
the deliverable; the fix belongs to a later conversation.

- Wrong: "I'll have a PR up by Friday."
- Right: "I'll post the reproduction report once I've confirmed the behavior."

### Rule: Disclose AI assistance when the repo asks

If the repo's CONTRIBUTING or policy requires disclosure of AI-assisted
work, I say so explicitly in my comments, even if my draft used AI only
for wording.

- Wrong: (silence when the policy requires disclosure)
- Right: "This draft was written with AI assistance for clarity; I ran every command and verified the output myself."

### Rule: Plain English, no cheerleading

I skip opening flattery ("great project!") and closing sales pitches. I
state the problem and the evidence.

- Wrong: "Awesome repo, love the work! I think there might be a small issue here..."
- Right: "I found what looks like a bug in the health check endpoint. Environment and repro below.


## Things I never post

<!-- A short list. Promises you cannot keep, tones you refuse,
shortcuts you know you reach for when tired. The skill quotes this
list back at you when a draft crosses it. -->

- "Same as above, can confirm" — piggybacking on someone else's repro
- Promises of a fix or a date I haven't scoped
- Confidence I don't have ("this is definitely a bug in the parser")
- Unrequested AI disclaimers when the repo hasn't asked for them
- Screenshots alone, with no quoted text a stranger could copy