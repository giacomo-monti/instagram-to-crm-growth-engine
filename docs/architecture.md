# Architecture and data model

## 1. Where this engine sits

Two ingestion paths write to one shared CRM. This repository covers the Instagram path; the website path is documented in the [Website-to-CRM Lifecycle Events Engine](https://github.com/giacomo-monti/website-to-crm-lifecycle-events-engine).

```mermaid
flowchart LR
    IG[Instagram audience] --> MC[ManyChat funnel]
    MC -.->|planned trigger| M1[Make: Instagram scenario]
    M1 --> CRM[(Shared Notion CRM<br/>Leads + Activities)]
    WEB[Website built with Lovable] -->|JSON events| M2[Make: website scenario]
    M2 --> CRM
    CRM -.-> BI[Future funnel reporting]
```

## 2. Conversational funnel (ManyChat)

```mermaid
flowchart TD
    A[Comment on a selected post or Reel] --> B[Public reply: DM sent]
    A --> C[Opening DM with eBook button]
    C --> D{Follows the account?}
    D -->|yes| E[Deliver the guide]
    E --> F[Early-access waitlist message]
    D -->|no| G[Ask to follow + 'I've followed' button]
    G --> H{Follows the account?}
    H -->|yes| E
    H -->|no| I["'Couldn't confirm yet' + 'Check again' button"]
    I --> H
```

**Design notes**

- The follow check runs twice: once when the person asks for the eBook, and again each time they confirm they have followed. Nobody is sent to a dead end.
- The public reply makes the interaction visible on the post, and the DM moves the conversation to a private, actionable channel.
- The waitlist message comes after the value has been delivered, never before.

## 3. CRM sync (Make → Notion)

```mermaid
sequenceDiagram
    participant MC as ManyChat
    participant MK as Make
    participant NL as Notion: Leads
    participant NA as Notion: Activities
    MC->>MK: contact data + attribution fields
    MK->>NL: query by Instagram User ID
    alt no Lead found
        MK->>NL: create Lead (with original acquisition fields)
    else Lead exists
        MK->>NL: update Lead (contact fields, last interaction)
    end
    MK->>NA: create Activity "Material Requested" linked to the Lead
```

- **Identity key:** Instagram User ID. The ManyChat Contact ID is stored as a secondary identifier. No email is required to create or match a Lead.
- **Attribution:** ManyChat custom fields carry a trigger keyword and a content ID (e.g. a Reel code). They become the Lead's *original acquisition* fields on creation and are copied onto every Activity.

## 4. CRM data model (shared with the website engine)

Simplified view: key fields only.

```mermaid
erDiagram
    LEADS ||--o{ ACTIVITIES : has
    CONTENT ||--o{ ACTIVITIES : "attributed to (future)"
    LEADS {
        string lead_id
        string instagram_user_id
        string manychat_contact_id
        string email
        string source
        string stage
        string lead_temperature
        date first_interaction
        date last_interaction
        string acquisition_keyword
        string acquisition_content_id
    }
    ACTIVITIES {
        string activity_id
        string channel
        string activity_type
        datetime timestamp
        string source_keyword
        string content_id
        string event_id
        number score_value
    }
    CONTENT {
        string content_id
        string content_type
        string cta
        string status
    }
```

- **Leads** represent the person. **Activities** represent every touchpoint and lifecycle event. One Lead has many Activities.
- **Stage** and **Lead Temperature** (Cold, Warm, Hot, Customer) give the sales view; Activities give the history behind it.
- The **Content** database is kept for future content-level attribution. Today the content ID is stored as text on Leads and Activities.
- **Score Value** is a configured weight per Activity (currently 1), not a validated predictive scoring model.
