# Welcome Desk — System Architecture

---

## High-level architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        VISITOR TOUCHPOINTS                       │
├──────────────┬──────────────────┬────────────────────────────────┤
│  QR Code     │  WhatsApp        │  Native App (Phase 2)          │
│  → Website   │  → Chatbot       │  → Voice / Push / Offline      │
└──────┬───────┴────────┬─────────┴──────────┬─────────────────────┘
       │                │                    │
       ▼                ▼                    ▼
┌──────────────────────────────────────────────────────────────────┐
│                         SUPABASE                                  │
├──────────────┬──────────────────┬────────────────────────────────┤
│  PostgreSQL  │  Edge Functions  │  Auth (admin only)              │
│  Database    │  - WhatsApp hook │  Storage (tourism logos,        │
│              │  - Shortcut gen  │   QR codes)                     │
│              │  - Analytics log │                                 │
└──────┬───────┴────────┬─────────┴────────────────────────────────┘
       │                │
       │                ▼
       │     ┌─────────────────────┐
       │     │  External APIs       │
       │     ├─────────────────────┤
       │     │  WhatsApp Business   │
       │     │  API (Meta/Twilio)   │
       │     │                      │
       │     │  LLM API             │
       │     │  (OpenAI/Anthropic)  │
       │     │                      │
       │     │  Firebase (Phase 2)  │
       │     │  (Push notifications)│
       │     └─────────────────────┘
       │
       ▼
┌──────────────────────────────────────────────────────────────────┐
│                     ADMIN / OPERATIONS                            │
├──────────────────────────────────────────────────────────────────┤
│  Admin panel (Lovable-built, Supabase Auth)                      │
│  - Content management (cities, consulates, KYR, FAQs)            │
│  - Escalation dashboard                                          │
│  - Analytics + signals dashboard                                 │
│                                                                   │
│  Escalation channel (Slack / SMS)                                │
│  - Volunteer coordinators receive alerts                         │
│  - Respond via WhatsApp or phone                                 │
└──────────────────────────────────────────────────────────────────┘
```

---

## Data flow: Website visit

1. Visitor scans QR code
2. Browser requests `welcometo2026.org/[city]`
3. React app loads, detects browser language via `Accept-Language` header
4. App queries Supabase for city data, consulates, KYR content, fan zones in detected language
5. City page renders with task-based card layout
6. Anonymous analytics event logged to `analytics_events` table

---

## Data flow: WhatsApp message

1. Visitor sends message to Welcome Desk WhatsApp number
2. WhatsApp Business API POSTs to Supabase Edge Function webhook URL
3. Edge Function:
   a. Parses incoming message text and sender metadata
   b. Checks for emergency keywords (in all supported languages)
   c. If emergency: creates `escalations` record, sends alert to Slack/SMS, returns immediate response with hotline number
   d. If not emergency: queries `faqs` table by detected language and city
   e. If FAQ match found: returns answer
   f. If no match: calls LLM API with message + relevant KYR content as context
   g. Returns generated response
4. Edge Function POSTs response back to WhatsApp Business API
5. Visitor receives reply in WhatsApp

---

## Data flow: Emergency escalation

1. Trigger: emergency keyword, low confidence, visitor requests human, 3 failed responses
2. Edge Function creates record in `escalations` table (language, conversation history, city, timestamp)
3. Alert sent to Slack channel or SMS to on-call coordinator
4. Visitor receives: "A person who speaks [language] will respond shortly. If this is an emergency, call [number]."
5. Volunteer accesses escalation dashboard in admin panel, sees full context
6. Volunteer responds via WhatsApp (using the business number) or calls visitor directly
7. Escalation record updated with resolution status

---

## Data flow: Voice shortcut (Phase 2)

1. Visitor says "Hey Siri, FIFA help" or "OK Google, FIFA help"
2. Native app Shortcut/Action triggers:
   a. Initiates phone call to city-specific legal hotline
   b. Sends WhatsApp emergency message with pre-populated text: `EMERGENCY [city] [language]`
   c. If location permission granted: attaches GPS coordinates to WhatsApp message and escalation record
3. Escalation record created in Supabase with location data
4. Volunteer coordinator receives alert with visitor location on map

---

## Database relationships

```
cities (1) ──→ (many) consulates
cities (1) ──→ (many) emergency_contacts
cities (1) ──→ (many) fan_zones
cities (1) ──→ (many) transit_info
cities (1) ──→ (many) faqs
cities (1) ──→ (many) analytics_events
cities (1) ──→ (many) escalations

know_your_rights keyed by: state + language
emergency_contacts keyed by: city + language + contact_type
faqs keyed by: city + language + category
```

---

## Security considerations

- No visitor PII stored. Analytics are anonymous (city, language, timestamp only).
- WhatsApp messages processed in transit via Edge Function; conversation history stored only for active escalations and deleted after resolution + 30 days.
- Admin panel behind Supabase Auth (email + password). MFA recommended.
- LLM API key stored as Supabase secret, never exposed to client.
- WhatsApp Business API webhook verified via Meta signature validation.
- Know Your Rights content includes disclaimer: "This is general information, not legal advice."
