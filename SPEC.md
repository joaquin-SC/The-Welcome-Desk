# Welcome Desk — Technical Specification

**Version:** 1.0
**Date:** February 2026
**Author:** Conexión Public Engagement

---

## 1. Overview

The Welcome Desk is a multilingual visitor information platform for international FIFA World Cup 2026 visitors. It consists of a website, a WhatsApp chatbot, and a native app wrapper, built in two phases.

**Phase 1:** Website (React PWA) + WhatsApp chatbot, built on Lovable.dev with Supabase backend.
**Phase 2:** Native iOS and Android app wrapper using Capacitor, adding voice shortcuts, background location, push notifications, and offline access.

---

## 2. Phase 1 — Website + PWA + WhatsApp Chatbot

### 2.1 Website

**Platform:** Lovable.dev (React + Tailwind CSS output)
**Hosting:** Lovable deployment with custom domain
**Domain:** WelcomeTo2026.org (or similar, TBD)

**Landing page**
- Visitor selects host city from a visual grid (city name + stadium/landmark image)
- Language auto-detected from browser `Accept-Language` header
- Language selector available as override (12 languages)
- Mobile-first responsive design

**City page — task-based layout**
Each city page presents five tappable cards, not a scrolling content page:

1. **"I need help now"** — Emergency contacts, legal hotline number (one-tap call), WhatsApp emergency deep link. Persistent emergency button visible on all screens.
2. **"My rights"** — Know Your Rights content for that state, in visitor's language.
3. **"Find the fan zone"** — Fan zone locations, hours, maps.
4. **"Contact my consulate"** — Searchable by visitor's country of origin. Address, phone, hours, after-hours number.
5. **"Getting around"** — Transit maps, airport-to-venue routes, rideshare info.

**Emergency button**
- Persistent on every page, every language
- One tap initiates a phone call to the state-specific immigration legal hotline
- Simultaneously sends a WhatsApp message via deep link: `wa.me/[number]?text=EMERGENCY+[city]+[language]`
- Visible without scrolling on mobile viewport

**WhatsApp integration on website**
- Every city page includes a prominent "Text us on WhatsApp" button
- Opens WhatsApp with pre-populated message including city name
- Website is the discovery hub; WhatsApp is the action channel within it

**"Set up voice help" section (per city page)**
- Auto-detected by device (iOS vs. Android)
- **iPhone:** "Add to Siri" button that downloads a .shortcut file. Shortcut calls legal hotline + sends WhatsApp emergency message when triggered.
- **Android:** "Add to Home Screen" button (PWA install prompt) + visual guide (3 screenshots, in visitor's language) for creating a Google Assistant voice routine.

**PWA capabilities**
- Installable to home screen
- Offline caching of Know Your Rights content via service worker
- Full-screen mode (no browser chrome)
- Web push notifications (Android; limited iOS support)

### 2.2 Supabase Backend

**Database schema**

| Table | Fields | Notes |
|---|---|---|
| `cities` | id, name, state, tourism_office_name, tourism_office_logo_url, emergency_number_local, legal_hotline_number, whatsapp_number | One row per host city |
| `consulates` | id, country, city_id, address, phone, after_hours_phone, hours, latitude, longitude | Keyed by country + city |
| `know_your_rights` | id, state, language, title, content, last_updated | Keyed by state + language |
| `emergency_contacts` | id, city_id, language, contact_type, name, phone, description | Keyed by city + language. Types: legal_hotline, police_non_emergency, hospital, consulate_general |
| `fan_zones` | id, city_id, name, address, latitude, longitude, hours, capacity, transit_notes | One per fan zone per city |
| `transit_info` | id, city_id, language, content | Transit guides per city per language |
| `faqs` | id, city_id, language, question, answer, category, sort_order | Chatbot knowledge base |
| `analytics_events` | id, city_id, language, page, event_type, timestamp | Anonymous, no PII |
| `escalations` | id, city_id, language, conversation_history, visitor_message, status, created_at, assigned_to | Escalation tracking for live operators |

**Admin panel**
- Email authentication via Supabase Auth (M4A staff only)
- Content management interface for all tables
- Bulk editing by language (update all Spanish content at once)
- Content status flags: draft / reviewed / published
- Last-updated timestamp on each entry
- Analytics dashboard: traffic by city, language, time of day
- Signals dashboard: FAQ spike detection, escalation ratio by city, underserved languages

**Edge Functions**
- WhatsApp webhook handler (see 2.3)
- Siri Shortcut file generator (dynamic per city)
- Analytics event logger

### 2.3 WhatsApp Chatbot

**API provider:** WhatsApp Business API via Meta Cloud API (or Twilio as fallback)

**Webhook flow:**
1. Visitor sends message to Welcome Desk WhatsApp number
2. WhatsApp Business API forwards message to Supabase Edge Function via webhook
3. Edge Function:
   a. Detects language from message text (LLM handles this natively)
   b. Checks for emergency keywords ("arrested," "detained," "police," "help," "emergency" — in all supported languages). If matched, triggers immediate escalation.
   c. Queries `faqs` table for matching content by city + language
   d. If no FAQ match, sends message + relevant Know Your Rights content to LLM API for response generation
   e. Returns response to WhatsApp Business API for delivery
4. If chatbot confidence is low or visitor requests a person, triggers escalation

**Chatbot system prompt requirements:**
- Interpret fragmentary, multilingual, possibly misspelled input as visitor needs
- Assume high-stress context — visitors may be scared, confused, or under duress
- Always provide the emergency hotline number in the visitor's language when the query suggests distress
- Never provide legal advice — provide Know Your Rights information and direct to legal hotline
- Respond in the same language the visitor writes in

**Escalation protocol:**
- **Triggers:** Chatbot confidence below threshold, visitor explicitly asks for a person, emergency keywords detected, three consecutive "I don't understand" responses
- **What happens:** Entry created in `escalations` table with: visitor's language, full conversation history, city, timestamp
- **Volunteer notification:** Alert sent to designated Slack channel (or SMS to on-call coordinator) with escalation details
- **Visitor experience:** "A person who speaks [language] will respond shortly. If this is an emergency, call [legal hotline number]."
- **SLA target:** Human response within 15 minutes during tournament hours

### 2.4 Phase 1 Timeline

| Week | Deliverable |
|---|---|
| 1 | Site architecture, city pages, Supabase schema, admin panel |
| 2 | Multilingual content loading, language auto-detection, mobile optimization |
| 3 | WhatsApp Edge Function, chatbot logic, webhook configuration, chatbot prompt engineering |
| 4 | Analytics dashboard, signals dashboard, QR code generation, Siri Shortcut generator |
| 5–6 | Content loading (translations, legal review), UAT with volunteer testers |
| 7 | Soft launch with one city, bug fixes |
| 8 | Full launch across participating cities |

### 2.5 Phase 1 Dependencies

| Dependency | Owner | Status |
|---|---|---|
| Lovable.dev Pro account | Conexión | Not started |
| Supabase Pro account | Conexión | Not started |
| Domain registration (WelcomeTo2026.org or similar) | Conexión | Not started |
| WhatsApp Business API account (Meta Business Manager, verified phone number) | Conexión | Not started — allow 1–2 weeks for Meta approval |
| LLM API key (OpenAI or Anthropic) | Conexión | Not started |
| Professional translation in 10–15 languages | Translation vendor | Not started |
| State-by-state legal review of Know Your Rights content (TX, FL, NY, CA minimum) | Immigration law partner | Not started |
| Immigration legal hotline partnership (staffed during tournament hours) | Legal nonprofit partner | Not started |
| City tourism office co-branding agreements | M4A host city outreach | Not started |
| QR code distribution agreements (hotels, airports, fan zones) | City tourism offices + FIFA LOCs | Not started |
| Volunteer coordinator for escalation management | M4A | Not started |
| Slack workspace or SMS system for escalation alerts | Conexión | Not started |

### 2.6 Phase 1 Budget

| Item | Cost |
|---|---|
| Lovable Pro | $25/month |
| Supabase Pro | $25/month |
| Domain | $15/year |
| WhatsApp Business API (usage-based, tournament window) | $200–500/month |
| LLM API (chatbot responses, tournament window) | $100–300/month |
| Professional translation (10–15 languages) | $15,000–20,000 |
| Legal review (4 states) | $10,000–15,000 |
| **Phase 1 total** | **~$30,000–40,000** |

---

## 3. Phase 2 — Native App Wrapper

### 3.1 Approach

Wrap the Phase 1 React codebase in a native shell using Capacitor. Same code, same backend, same chatbot. Native wrapper adds platform-specific capabilities.

**Framework:** Capacitor (by Ionic team, open source)
**Platforms:** iOS + Android
**Distribution:** Apple App Store + Google Play Store

### 3.2 Native features added

| Feature | What it does | Why it matters |
|---|---|---|
| Native Siri Shortcuts | Registered at install. "Hey Siri, FIFA help" calls legal hotline + sends emergency WhatsApp with location. No visitor setup required. | Removes the screen from the emergency pathway. |
| Google Assistant Actions | Same as Siri, for Android. "OK Google, FIFA help." | Same. |
| Background location | App can send GPS coordinates during emergency escalation even when not in foreground. | Legal hotline and volunteer coordinator know exactly where the visitor is. |
| Push notifications | Direct to lock screen. "Your match starts in 2 hours." Or: "Know your rights at today's venue." | Proactive information delivery. |
| Lock screen widget | Emergency button accessible without unlocking the phone. | Fastest possible path to help under duress. |
| Robust offline mode | Full Know Your Rights content cached locally via native storage. | Works in detention facilities, underground transit, areas with no signal. |
| App Store presence | Searchable by "FIFA 2026 visitor help" or "World Cup rights." | Discovery channel for visitors who search before arriving. |

### 3.3 Phase 2 Build Sequence

| Week | Deliverable |
|---|---|
| 6 | Set up Capacitor project wrapping Phase 1 React codebase. Test basic iOS + Android builds. |
| 7 | Implement native Siri Shortcuts and Google Assistant Actions for emergency pathway. |
| 8 | Add background location permission flow and emergency location sharing. |
| 9 | Push notification infrastructure (Firebase Cloud Messaging for Android, APNs for iOS). |
| 10 | Lock screen widget (iOS) and persistent notification shortcut (Android). Enhanced offline caching. |
| 11 | App Store and Google Play submission. Listing copy and screenshots in 15 languages. |
| 12 | App Store review, approval, soft launch. |

Note: Phase 2 weeks run in parallel with Phase 1 weeks 6–12. Phase 2 depends on the Phase 1 React codebase being stable by Week 6.

### 3.4 Phase 2 Dependencies

| Dependency | Owner | Status |
|---|---|---|
| Phase 1 React codebase stable and deployed | Conexión / Lovable | Depends on Phase 1 |
| Developer with Capacitor / React Native experience (freelance, 1–2 weeks) | Conexión | Not started |
| Apple Developer Account ($99/year) | Conexión | Not started |
| Google Play Developer Account ($25 one-time) | Conexión | Not started |
| App Store listing copy + screenshots in 15 languages | Translation vendor | Not started |
| Firebase project for push notifications | Conexión | Not started |
| Apple Push Notification service (APNs) certificate | Conexión | Not started |

### 3.5 Phase 2 Budget

| Item | Cost |
|---|---|
| Capacitor wrapper development (freelance, 1–2 weeks) | $5,000–10,000 |
| Apple Developer Account | $99/year |
| Google Play Developer Account | $25 (one-time) |
| App Store listing translations + ASO | $2,000–3,000 |
| Firebase (push notifications) | Free tier sufficient |
| **Phase 2 total** | **~$8,000–14,000** |

---

## 4. Risks and Mitigations

| Risk | Impact | Mitigation |
|---|---|---|
| WhatsApp Business API approval delayed by Meta | Chatbot unavailable at launch | Start application immediately. Fallback: web-based chat widget on site (Lovable builds natively). |
| App Store review rejection or delay | Phase 2 native app misses tournament | Submit 3+ weeks before tournament. Have PWA as functional fallback. |
| Legal hotline not staffed during tournament hours | Escalation pathway fails at the critical moment | Confirm partnership and staffing commitment before soft launch. Build fallback messaging: "No one is available. Call [consulate number] directly." |
| Complex chatbot queries exceed Edge Function capability | Visitors get wrong or unhelpful answers | Set confidence threshold conservatively. Escalate to human early rather than late. Iterate on system prompt during soft launch week. |
| Translation errors in Know Your Rights content | Visitors receive inaccurate legal information | Professional human translation only for legal content. Legal review in each state. Include disclaimer: "This is general information, not legal advice." |
| Content goes stale during tournament (fan zone hours change, hotline numbers update) | Visitors get outdated information | Admin panel with last-updated flags. Assign content manager for daily review during tournament window. |
| Low app adoption (Phase 2) | Investment in native wrapper underutilized | PWA serves as primary platform regardless. App is additive. Promote app download through fan zone signage and Welcome Desk site. |

---

## 5. Decision points for M4A

1. **Fund Phase 1 only, or Phase 1 + Phase 2?** Phase 1 is sufficient for an MVP. Phase 2 makes the emergency pathway significantly stronger. Total difference: $8,000–14,000.

2. **Which LLM provider for the chatbot?** OpenAI and Anthropic both work. Pricing is comparable. Decision may depend on existing relationships or preferences.

3. **How many cities at launch?** The platform supports all eleven but content (translations, legal review, consular directories) scales linearly per city. Launching with 4–6 cities and expanding is a reasonable scoping decision.

4. **Legal hotline partnership.** This is the single most important operational dependency. The platform is only as good as the human at the other end of the escalation. M4A needs to confirm this before committing to the build.
