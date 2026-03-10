# Phase 2 — Native App Wrapper

See [SPEC.md](../docs/SPEC.md) Section 3 for full details.

## Summary

| Attribute | Detail |
|---|---|
| **Framework** | Capacitor (wrapping Phase 1 React codebase) |
| **Platforms** | iOS + Android |
| **Distribution** | Apple App Store + Google Play |
| **Timeline** | Weeks 6–12 (parallel with Phase 1) |
| **Budget** | $8,000–14,000 (additional to Phase 1) |
| **Dependency** | Phase 1 React codebase stable by Week 6 |

## Native features added

- [ ] Siri Shortcuts — registered at install, no visitor setup
- [ ] Google Assistant Actions — same for Android
- [ ] Background location — GPS coordinates during emergency escalation
- [ ] Push notifications — via Firebase (Android) and APNs (iOS)
- [ ] Lock screen widget — emergency button without unlocking phone
- [ ] Robust offline mode — full KYR content cached locally
- [ ] App Store presence — searchable by visitors before arrival

## Milestones

- [ ] Week 6: Capacitor project setup, basic iOS + Android builds
- [ ] Week 7: Siri Shortcuts + Google Assistant Actions for emergency pathway
- [ ] Week 8: Background location + emergency location sharing
- [ ] Week 9: Push notification infrastructure
- [ ] Week 10: Lock screen widget, enhanced offline caching
- [ ] Week 11: App Store + Google Play submission, listing translations
- [ ] Week 12: Review, approval, soft launch

## Critical dependencies

- [ ] Phase 1 codebase stable and deployed
- [ ] Developer with Capacitor experience contracted
- [ ] Apple Developer Account ($99/year)
- [ ] Google Play Developer Account ($25)
- [ ] Firebase project created
- [ ] App Store listing copy translated in 15 languages
