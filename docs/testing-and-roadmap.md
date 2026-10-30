# Testing, limitations and roadmap

Every claim in this repository is labelled as **implemented**, **tested and observed**, or **planned**. Functional tests show that the logic works; they are not evidence of business performance.

## Tested and observed

**ManyChat funnel**
- The main path (comment → public reply → DM → follow confirmed → guide → waitlist message) has run end to end on the live account.
- The "not following yet" branch and the retry loop are configured but have not yet been triggered by a real user.

**Make → Notion sync** (with test profiles)
- ManyChat custom fields (trigger keyword, content ID) were received by Make.
- A new Instagram identity created a Lead and a linked `Material Requested` Activity.
- A repeat interaction from the same identity **updated the existing Lead instead of creating a duplicate**.
- Original acquisition fields stayed unchanged on the update path.

## Not claimed

Conversion rates, follower growth, email or waitlist volumes, revenue and ROI. These need production analytics over meaningful volumes.

## Known limitations

- The Make scenario is not yet triggered by the live funnel, so live conversations do not create CRM records yet.
- The query-then-create pattern prevents duplicates in normal use, but it is not an atomic uniqueness constraint.
- Instagram Leads (matched by Instagram User ID) and website Leads (matched by email) are not yet stitched into one person automatically.
- No failure alerting or reconciliation between ManyChat, Make and Notion yet.

## Roadmap

1. **Connect the live funnel to the CRM:** add a Make trigger right after the guide is delivered.
2. **Log every funnel step as an Activity:** comment, DM sent, follow confirmed, material requested, waitlist click. This turns the funnel into measurable conversion steps.
3. **Attribution per post:** set the trigger keyword and content ID for every post or Reel the automation runs on.
4. **Identity stitching** between Instagram and website Leads, with explicit rules for when an Instagram contact later shares an email.
5. **Reliability:** error handlers, failure alerts and a simple reconciliation check.
6. **Funnel reporting:** comment → DM → follow → guide → waitlist → website signup, by content.
7. **Consolidation:** a shared event contract and, once stable, one central webhook for Instagram and website events.
