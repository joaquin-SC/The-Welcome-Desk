# Welcome Desk — Lovable Build Prompts

Sequenced prompts for building the Welcome Desk MVP on Lovable.dev. Each prompt builds on the previous. Run them in order.

---

## Prompt 1: Site architecture and city pages

```
Build a multilingual visitor information website for the 2026 FIFA World Cup called "Welcome Desk."

Landing page: a visual grid of 11 US host cities (Boston, Kansas City, Los Angeles, Seattle, Miami, Atlanta, Philadelphia, San Francisco, New York/New Jersey, Houston, Dallas). Each city is a tappable card with the city name. Clean, mobile-first design.

Auto-detect the visitor's language from their browser Accept-Language header and display the site in that language. Supported languages: English, Spanish, Portuguese, French, German, Arabic, Japanese, Korean, Mandarin Chinese, Hindi, Dutch, Polish. Include a language selector dropdown as an override in the header.

Each city page uses a task-based card layout with 5 tappable cards:
1. "I need help now" — emergency contacts and a one-tap call button
2. "My rights" — Know Your Rights information
3. "Find the fan zone" — locations and hours
4. "Contact my consulate" — searchable by country
5. "Getting around" — transit information

Add a persistent emergency button fixed to the bottom of every city page. Red background, white text, says "Emergency Help" in the detected language. Tapping it initiates a phone call using tel: link.

Use a warm, trustworthy color palette. Not government-looking. Not advocacy-looking. Think hospitality — like a helpful concierge service.
```

## Prompt 2: Supabase connection and database schema

```
Connect to Supabase. Create the following tables:

1. cities: id, name, state, tourism_office_name, tourism_office_logo_url, emergency_number_local, legal_hotline_number, whatsapp_number
2. consulates: id, country, city_id (FK to cities), address, phone, after_hours_phone, hours, latitude, longitude
3. emergency_contacts: id, city_id (FK), language, contact_type (enum: legal_hotline, police_non_emergency, hospital, consulate_general), name, phone, description
4. know_your_rights: id, state, language, title, content (rich text), last_updated
5. fan_zones: id, city_id (FK), name, address, latitude, longitude, hours, capacity, transit_notes
6. transit_info: id, city_id (FK), language, content (rich text)
7. faqs: id, city_id (FK), language, question, answer, category, sort_order
8. analytics_events: id, city_id (FK), language, page, event_type, timestamp (default now())
9. escalations: id, city_id (FK), language, conversation_history (jsonb), visitor_message, status (enum: open, assigned, resolved), created_at, assigned_to

Wire the city pages to pull data from these tables. The "Contact my consulate" card should have a search/filter by country name. The "I need help now" card should pull from emergency_contacts filtered by city and the visitor's detected language. The "My rights" card should pull from know_your_rights filtered by the city's state and detected language.

Log anonymous analytics events (page views by city, language, timestamp) to analytics_events. No personally identifiable information.
```

## Prompt 3: Admin panel

```
Build an admin panel accessible at /admin. Require email authentication via Supabase Auth.

The admin panel should allow M4A staff to:
- View, add, edit, and delete entries in all content tables (cities, consulates, emergency_contacts, know_your_rights, fan_zones, transit_info, faqs)
- Bulk edit by language: select a language and see all content entries for that language across all cities
- Each content entry shows a status badge: Draft, Reviewed, Published
- Each entry shows a "Last updated" timestamp
- Filter and search across all tables

Add an Analytics tab showing:
- Page views by city (bar chart)
- Traffic by language (pie chart)
- Traffic over time (line chart)
- Date range selector

Add a Signals tab showing:
- Top FAQ questions this week (ranked list)
- Escalation count by city (bar chart)
- Languages with traffic but no translated content (alert list)
```

## Prompt 4: WhatsApp chatbot Edge Function

```
Create a Supabase Edge Function called "whatsapp-webhook" that handles incoming WhatsApp messages.

The function should:
1. Accept POST requests with WhatsApp message payload (sender number, message text, timestamp)
2. Detect the language of the incoming message using the LLM
3. Extract the city context (from the message or from a stored session)
4. Check for emergency keywords: "arrested", "detained", "police", "help", "emergency", "jail", "ICE", "scared", "taken", "stopped" — and their translations in all supported languages. If matched, immediately create an escalation record and return an emergency response with the legal hotline number.
5. Query the faqs table for a matching answer by city + language
6. If no FAQ match, call the OpenAI API (or Anthropic API) with:
   - System prompt: "You are a helpful visitor assistant for the 2026 FIFA World Cup. You help international visitors find information about their host city. You respond in the visitor's language. You never provide legal advice. When a visitor seems distressed or mentions police/detention, always provide the emergency legal hotline number. Be warm, calm, and clear."
   - User message: the visitor's message
   - Context: relevant Know Your Rights content for the city's state in the detected language
7. Return the response as JSON: { reply: string, language: string, escalated: boolean }

Also create an Edge Function called "generate-shortcut" that accepts a city_id parameter and returns a downloadable Apple Shortcuts .shortcut file configured to call that city's legal hotline number.

Store the OpenAI/Anthropic API key as a Supabase secret, not in the code.
```

## Prompt 5: WhatsApp integration and emergency UX

```
Add a WhatsApp deep link button to every city page, below the emergency button. The button should say "Text us on WhatsApp" in the visitor's detected language and link to: https://wa.me/[city_whatsapp_number]?text=Hi%20I%20am%20visiting%20[city_name]

The emergency button should now do two things when tapped:
1. Initiate a phone call to the city's legal hotline number (tel: link)
2. Open a second action: "Also send an emergency WhatsApp" linking to wa.me/[number]?text=EMERGENCY%20[city]%20[language]

Add a "Set up voice help" section below the main cards on each city page. Auto-detect the visitor's device:
- If iOS: show an "Add to Siri" button that downloads the city-specific .shortcut file from the generate-shortcut Edge Function
- If Android: show an "Add to Home Screen" prompt for the PWA, plus a brief visual guide (3 steps with icons) for setting up a Google Assistant routine
```

---

## Notes

- Run prompts 1–5 in order. Each builds on the previous.
- After each prompt, review the output, test on mobile, and iterate with follow-up prompts as needed.
- Content (translations, legal review, consulate data) is loaded into Supabase separately from the build — the prompts create the structure, not the content.
- The WhatsApp webhook URL will need to be configured in the Meta Business Manager dashboard once the Edge Function is deployed.
