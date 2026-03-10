# Welcome Desk — Lovable First Prompt

This is the initial prompt to paste into Lovable.dev to build the Welcome Desk framework. It establishes the full app structure, design system, routing, components, and placeholder content in one shot. Subsequent prompts (see lovable-prompts.md) add Supabase, the admin panel, and the chatbot.

**Important:** Create a new project in Lovable before pasting this. Do not use a template. Name the project "welcome-desk".

---

## Paste this into Lovable:

```
Build a mobile-first multilingual visitor information web app called "Welcome Desk" for the 2026 FIFA World Cup. This is a public resource for international visitors to US host cities. It should feel like a warm, trustworthy concierge service — not a government website, not an advocacy campaign.

DESIGN SYSTEM:
- Color palette: warm white (#FAFAF8) background, deep navy (#1B2A4A) for headings and primary text, coral (#E8654A) for the persistent emergency button and urgent actions, teal (#2A9D8F) for interactive cards and links, soft gray (#E8E6E1) for card backgrounds and dividers. 
- Typography: clean sans-serif (Inter or similar). Large body text (18px minimum) because visitors may be reading in a second language under stress.
- Border radius: 12px on cards. 24px on buttons. Rounded and approachable.
- Spacing: generous. Don't crowd the screen. White space communicates calm.
- Icons: use Lucide icons throughout. Simple line style.
- All UI text must be translatable. Never hardcode display strings — store them in a translations object keyed by language code. Start with English as the default and I'll add other languages later.

LANGUAGE DETECTION:
- On first load, detect the visitor's language from the browser's navigator.language or Accept-Language header.
- Map to one of 12 supported languages: en, es, pt, fr, de, ar, ja, ko, zh, hi, nl, pl.
- If the detected language isn't supported, fall back to English.
- Store the selected language in React state (context provider) so all components can access it.
- Show a language selector in the top header — a small globe icon that opens a dropdown with language names written in their own script (e.g., "Español", "Português", "العربية", "日本語"). Changing the language updates the entire UI immediately.

ROUTING AND PAGES:

1. LANDING PAGE (/)
A clean page with:
- Welcome Desk logo/wordmark at the top (placeholder for now — use text "Welcome Desk" in the nav brand with a small globe icon)
- A short tagline below it: "Your guide to the 2026 FIFA World Cup" (translatable)
- A subtitle: "Select your host city" (translatable)
- A grid of 11 city cards, 2 columns on mobile, 3 on tablet, 4 on desktop. Each card shows:
  - City name (bold)
  - State abbreviation below it (lighter text)
  - A subtle background gradient or color accent unique to each city
- Cities: Boston (MA), Kansas City (MO), Los Angeles (CA), Seattle (WA), Miami (FL), Atlanta (GA), Philadelphia (PA), San Francisco (CA), New York/New Jersey (NY/NJ), Houston (TX), Dallas (TX)
- Below the grid: "Need help right now?" with a coral emergency button that links to a generic emergency page.

2. CITY PAGE (/city/:slug)
The main experience. Task-based layout — NOT a scrolling content page. Show 5 tappable cards arranged vertically, each one full-width on mobile:

Card 1: "I need help now" (coral background, white text, alert-triangle icon)
- Tapping opens a dedicated help page with:
  - A large one-tap CALL button (tel: link) for the city's legal hotline. This is the most prominent element.
  - Below it: "Also alert us on WhatsApp" button (teal) that opens wa.me deep link with pre-filled text: "EMERGENCY [city] [language]"
  - Below that: local emergency number (911), police non-emergency, nearest hospital
  - All content pulled from placeholder data for now.

Card 2: "My rights" (white background, navy text, shield icon)
- Tapping opens a page with Know Your Rights content.
- Placeholder: 3-4 accordion sections (e.g., "If you are stopped by police", "If you are asked for documents", "If you are detained", "Your right to contact your consulate"). Each section expands to show a paragraph of placeholder text.

Card 3: "Find the fan zone" (white background, teal accent, map-pin icon)
- Tapping opens a page showing fan zone information.
- Placeholder: 1-2 fan zone entries with name, address, hours, and a static map placeholder image.

Card 4: "Contact my consulate" (white background, navy text, building icon)
- Tapping opens a page with a search bar at the top. Visitor types their country name and sees matching consulate results.
- Placeholder: 5-6 sample consulate entries (Argentina, England, Germany, Mexico, Japan, Brazil) with address, phone, hours.
- Search should filter in real time as the visitor types.

Card 5: "Getting around" (white background, teal accent, bus icon)
- Tapping opens a page with transit information.
- Placeholder: a paragraph about getting from the airport to the fan zone, local transit options.

PERSISTENT EMERGENCY BUTTON:
- Fixed to the bottom of the viewport on EVERY city page and subpage. Always visible. Never scrolls away.
- Coral (#E8654A) background, white text, phone icon on the left.
- Text: "Emergency Help" (translatable).
- Tapping it initiates a phone call via tel: link to the city's legal hotline number.
- Has a subtle shadow to float above page content.
- 60px tall, full width minus 16px margins on each side, centered.

WHATSAPP ENTRY POINT:
- On every city page, below the 5 task cards and above the footer: a teal button with the WhatsApp icon that says "Text us on WhatsApp" (translatable). 
- Links to wa.me/[placeholder_number]?text=Hi%20I%20am%20visiting%20[city_name]
- This is NOT as prominent as the emergency button. It's an option, not the primary action.

VOICE HELP SECTION:
- Below the WhatsApp button on each city page: a collapsible section titled "Set up voice help on your phone" (translatable).
- Detect if the device is iOS or Android (via navigator.userAgent).
- If iOS: show text "Add a Siri shortcut so you can say 'Hey Siri, FIFA help' to call for help instantly." with a button "Add to Siri" (placeholder action for now).
- If Android: show text "Add a shortcut to your home screen for one-tap emergency help." with a button "Add to Home Screen" (trigger the PWA install prompt if available, otherwise show instructions).

HEADER:
- Sticky top header on all pages.
- Left: back arrow (on subpages) or Welcome Desk wordmark (on landing).
- Center: city name (on city pages).
- Right: globe icon → language dropdown.

FOOTER:
- Minimal. "Welcome Desk is a project of Mayors for America." Translatable.
- Links: "About", "Privacy" (placeholder pages).

DATA STRUCTURE:
For now, use a local JSON data file (not Supabase yet — I'll connect that in the next prompt). Structure it like this:

cities: array of { slug, name, state, emergencyNumber, legalHotlineNumber, whatsappNumber, fanZones: [], consulates: [], transitInfo: string, knowYourRights: [] }

Create sample data for Miami and Kansas City with realistic placeholder content. Other cities can have minimal placeholder data.

MAKE IT A PWA:
- Add a web app manifest with: name "Welcome Desk", short_name "WelcomeDesk", start_url "/", display "standalone", theme_color "#1B2A4A", background_color "#FAFAF8".
- Add a basic service worker that caches the app shell and the local data file for offline access.
- The app should work offline with cached content after the first visit.

PERFORMANCE:
- No heavy images on first load. Use CSS gradients and icons instead of photos for the city cards.
- Entire app should load in under 2 seconds on a 3G connection.
- Lazy-load subpage content.

Build this as a complete, working app. I should be able to tap through every flow on my phone.
```

---

## What this prompt produces

After Lovable processes this prompt, you should have:

- A working React app with routing to all pages
- Language detection and a translation context provider
- 11 city cards on the landing page
- Full task-based city page layout for all 11 cities
- Functional subpages for each of the 5 cards (help, rights, fan zone, consulate search, transit)
- Persistent emergency call button on all city pages
- WhatsApp deep link button
- Voice help section with iOS/Android detection
- Local JSON data with Miami and Kansas City populated
- PWA manifest and basic service worker
- Responsive design that works on mobile, tablet, and desktop

## What this prompt does NOT produce

- Supabase connection (Prompt 2)
- Admin panel (Prompt 3)
- WhatsApp chatbot Edge Function (Prompt 4)
- Real translations in non-English languages (content task, done during Weeks 5–6)
- Real consulate data, fan zone data, or Know Your Rights content (content task)

## After running this prompt

1. Test on your phone. Tap through every flow.
2. Check that language detection works (change your browser language to Spanish and reload).
3. Verify the emergency button is visible and fixed on every city page.
4. Confirm the consulate search filters correctly.
5. Note anything that needs adjustment and iterate with follow-up prompts before moving to Prompt 2.
