# My Response — Technical Writer's Perspective

**Context:** I'm joining this conversation as the technical writer on the team. I want to make sure both perspectives are heard, and help us find a path forward that works for everyone.

---

Hey both — thanks for raising this. I want to quickly summarise what I'm hearing and suggest a way forward.

**What the developer is pointing out** is real: polling means the app keeps asking "are we done yet?" every few seconds. As more clinicians use the system, this creates a flood of unnecessary requests, slows down downstream tools, and makes the system harder to scale. The webhook solution is already built — it's just not documented or officially available to integrators yet.

**What the PM is protecting** is also valid: the UI release is close, the team's focus is limited, and there's no loud customer complaint forcing our hand right now.

---

**Here's what I'd suggest as a practical middle ground:**

The webhook is already working in the backend. The only thing missing is *documentation*. 
I can write and publish the webhook documentation this week — it doesn't require engineering time or delay the UI release. Once it's documented, integrators can start adopting it immediately, and we reduce polling load before it becomes a real incident.
I'll draft the webhook section of the developer guide today. Can the developer spare 30 minutes to review it for accuracy before I publish?

---

