## Phase 1 Overview

| Attribute | Detail |
|---|---|
| **Platform** | Lovable.dev + Supabase |
| **Output** | React PWA, WhatsApp chatbot, admin panel |
| **Timeline** | 8 weeks |
| **Budget** | $30,000–40,000 |
| **Launch target** | Late May 2026 (before June 11 tournament opener) |

---

## Critical Dependencies (resolve before build starts)

- [ ] WhatsApp Business API application submitted to Meta
- [ ] Legal hotline partnership confirmed and staffed
- [ ] Translation vendor contracted
- [ ] Immigration law partner contracted for KYR review

---

## Week 1: Site Architecture & City Pages (Prompt 1)

- [ ] Create Lovable project ("welcome-desk")
- [ ] Landing page with 11 host city card grid (responsive: 2/3/4 cols)
- [ ] City page with 5 task-based cards (Help, Rights, Fan Zone, Consulate, Transit)
- [ ] Subpages for each card with placeholder content
- [ ] Persistent emergency call button (fixed bottom, coral, `tel:` link)
- [ ] Language auto-detection (browser `Accept-Language`)
- [ ] Language selector dropdown (12 languages, native script labels)
- [ ] Translation context provider (all UI strings translatable)
- [ ] Design system implemented (color palette, typography, spacing, Lucide icons)
- [ ] Local JSON data file with Miami + Kansas City populated
- [ ] PWA manifest + basic service worker for offline caching
- [ ] WhatsApp deep link button on city pages
- [ ] Voice help section with iOS/Android detection
- [ ] Header (sticky, back arrow, city name, language selector)
- [ ] Footer ("Mayors for America" attribution, About/Privacy links)
- [ ] Mobile-first testing — tap through every flow on phone

## Week 2: Supabase & Multilingual Content (Prompt 2)

- [ ] Connect Lovable project to Supabase
- [ ] Create database tables:
  - [ ] `cities`
  - [ ] `consulates`
  - [ ] `emergency_contacts`
  - [ ] `know_your_rights`
  - [ ] `fan_zones`
  - [ ] `transit_info`
  - [ ] `faqs`
  - [ ] `analytics_events`
  - [ ] `escalations`
- [ ] Wire city pages to pull from Supabase (replace local JSON)
- [ ] Consulate search/filter by country name
- [ ] Emergency contacts filtered by city + language
- [ ] KYR content filtered by state + language
- [ ] Anonymous analytics event logging (page views by city/language)
- [ ] Mobile optimization pass

## Week 3: Admin Panel (Prompt 3)

- [ ] Admin panel at `/admin` with Supabase Auth (email login)
- [ ] CRUD for all content tables (cities, consulates, emergency_contacts, KYR, fan_zones, transit_info, FAQs)
- [ ] Bulk edit by language view
- [ ] Content status badges (Draft / Reviewed / Published)
- [ ] "Last updated" timestamps on entries
- [ ] Filter and search across all tables
- [ ] Analytics tab (page views by city, traffic by language, traffic over time, date range selector)
- [ ] Signals tab (top FAQ questions, escalation count by city, missing translation alerts)

## Week 3-4: WhatsApp Chatbot (Prompts 4 & 5)

- [ ] `whatsapp-webhook` Supabase Edge Function
- [ ] Language detection via LLM
- [ ] City context extraction (from message or stored session)
- [ ] Emergency keyword detection (multilingual) → auto-escalation + hotline response
- [ ] FAQ table lookup by city + language
- [ ] LLM fallback response (system prompt, KYR context injection)
- [ ] API key stored as Supabase secret
- [ ] `generate-shortcut` Edge Function (Apple Shortcuts `.shortcut` file)
- [ ] Emergency button dual action (call + WhatsApp alert)
- [ ] "Add to Siri" button (downloads city-specific shortcut)
- [ ] Android PWA install prompt + Google Assistant routine guide
- [ ] Configure webhook URL in Meta Business Manager

## Week 4: Analytics & Distribution

- [ ] QR code generation for each city page
- [ ] Siri Shortcut generator tested end-to-end
- [ ] Analytics dashboard reviewed and validated
- [ ] Signals dashboard reviewed (FAQ ranking, escalation tracking, translation gaps)

## Weeks 5-6: Content Loading & UAT

- [ ] Professional translations loaded for all 12 languages
- [ ] Legal review completed (KYR content for relevant states)
- [ ] Real consulate data loaded for all 11 cities
- [ ] Real fan zone data loaded
- [ ] Real transit info loaded
- [ ] User acceptance testing across languages
- [ ] Accessibility review (large text, contrast, screen reader)
- [ ] Performance audit (< 2s load on 3G)

## Week 7: Soft Launch

- [ ] Select pilot city
- [ ] Deploy to production (Lovable hosting)
- [ ] QR codes printed and distributed (pilot city)
- [ ] WhatsApp number live and monitored
- [ ] Legal hotline staffed and tested
- [ ] Monitor analytics + signals dashboard
- [ ] Collect feedback and iterate

## Week 8: Full Launch

- [ ] All 11 city pages live with real content
- [ ] WhatsApp chatbot handling all supported languages
- [ ] Admin panel handed off to M4A staff
- [ ] QR codes distributed to all host cities
- [ ] Monitoring and escalation workflow confirmed
- [ ] Launch communications sent
