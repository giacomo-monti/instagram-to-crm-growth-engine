# Design decisions and trade-offs

### D1 — A follow as the value exchange for a free resource
The eBook is free; what I ask in return is a follow. It grows the audience without adding a form inside the DM.
**Trade-off:** people who don't want to follow drop out. Clear messaging and a retry loop reduce the friction for those who do want the guide.

### D2 — Two-step follow verification with a retry loop
A single check would leave a dead end for someone who follows a moment later. The "I've followed" and "Check again" buttons re-run the same condition until it passes.
**Trade-off:** a slightly longer conversation, in exchange for not losing people who are willing to complete the step.

### D3 — Public reply plus private DM
The public reply shows other viewers that commenting gets a response. The DM is where the resource and the next steps are delivered.

### D4 — Separate Leads (people) from Activities (touchpoints)
One person can interact many times, across Instagram and the website. Leads stay clean and deduplicated, while Activities keep the full history.

### D5 — Identity by platform ID, not email
The Instagram scenario matches Leads by Instagram User ID and also stores the ManyChat Contact ID. Identity resolution does not depend on the person typing an email, so the funnel stays short.
**Trade-off:** matching an Instagram Lead to a website Lead (which is matched by email) needs an explicit identity-stitching rule. See the roadmap.

### D6 — Preserve the original acquisition attribution
The acquisition keyword and content ID are written only when the Lead is created. Later interactions never overwrite them; they are recorded on each Activity instead. This keeps "what brought this person in" separate from "what they did next".

### D7 — Notion as a lightweight CRM
Chosen to validate the acquisition model quickly, with records that are easy to read and edit.
**Trade-off:** Notion is not a high-throughput event store. If volume or reporting needs grow, a dedicated database or CRM is the natural next step.

### D8 — Keep ingestion paths separate while iterating
The Instagram and website scenarios stay independent until a shared event contract is stable. A single central webhook for both channels is a later consolidation, not a starting assumption.
