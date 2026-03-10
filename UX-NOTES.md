# Welcome Desk — UX Strategy Notes

**Date:** February 2026
**Context:** Evaluation of the Welcome Desk MVP spec from a UX strategy perspective.

---

## Core user journey

A FIFA visitor lands in a US host city. They don't speak English. They scan a QR code. They need to know: where's the fan zone, what are their rights, who do they call if something goes wrong.

Three critical moments: **discovery** (finding the resource), **comprehension** (understanding it in their language), **action** (getting help when they need it).

---

## Design decisions

### Language: auto-detect first, selector second

Do not show English by default. Auto-detect from the browser `Accept-Language` header and serve the page in that language immediately. Language selector is an override, not the primary mechanism. If a Portuguese-speaking visitor sees English with a dropdown, that's a failure state.

### City pages: task-based, not content-based

Five tappable cards, not five sections on a scrolling page. One task, one screen. A visitor in distress doesn't scroll.

1. "I need help now"
2. "My rights"
3. "Find the fan zone"
4. "Contact my consulate"
5. "Getting around"

### Emergency button: persistent, one-tap

Visible on every page, every language, without scrolling. One tap = phone call to legal hotline. Do not route emergencies through the chatbot.

### Website is the hub, WhatsApp is the action layer

QR codes go to the website. The website prominently features WhatsApp entry points. Two taps: scan, tap. Don't treat them as parallel paths.

### Voice shortcuts: native where possible, instructional where not

iPhone: downloadable Siri Shortcut, registered automatically in Phase 2.
Android: home screen shortcut (PWA) + visual setup guide for Google Assistant routine.
Phase 2 native app: both registered at install, no visitor setup required.

---

## Chatbot prompt engineering notes

### Input conditions

Visitors in stressful situations don't type well-formed questions. They type fragments: "police stop me," "lost passport," "where game." The chatbot system prompt must:

- Interpret fragmentary, multilingual, possibly misspelled input
- Assume high-stress context
- Always surface the emergency hotline number when distress is detected
- Never provide legal advice — provide information and direct to hotline
- Respond in the visitor's language

### Emergency keywords

Maintain a list of emergency trigger words in all supported languages: arrested, detained, police, help, emergency, jail, ICE, scared, taken, stopped. When detected, bypass FAQ matching and trigger escalation immediately.

---

## Escalation UX

### What triggers it

- Emergency keywords detected
- Chatbot confidence below threshold
- Visitor explicitly asks for a person
- Three consecutive failed responses

### What the visitor experiences

1. Immediate message: "A person who speaks [language] will respond shortly."
2. Emergency number displayed: "If this is an emergency, call [number] now."
3. No dead air — chatbot continues to attempt helpful responses while waiting for human.

### What the volunteer sees

- Visitor's language
- Full conversation history
- City
- Timestamp
- If Phase 2: GPS location

### SLA target

Human response within 15 minutes during tournament hours (match days, 8am–midnight local time per city).

---

## Admin panel UX

11 cities × 15 languages × 5+ content types = 800+ content entries. The admin panel must support:

- Bulk editing by language
- Content status flags (draft / reviewed / published)
- Last-updated timestamps
- Signals dashboard (FAQ spikes, escalation ratios, underserved languages)

Do not treat the admin panel as an afterthought. Budget a dedicated Lovable session for CMS design.

---

## Analytics that matter

Beyond raw traffic:

- Which Know Your Rights questions spike during which matches?
- Which cities have the highest chatbot-to-escalation ratio?
- Which languages are underserved (high traffic, no translation)?
- What time of day do emergency escalations cluster?

Surface these as a signals dashboard, not buried in raw data.
