# Sarah Mueller Counselling — Static Site Design

## Overview

Convert the existing WordPress site at www.sarahmueller.net to a static single-page site hosted on Firebase Hosting. The site serves as a professional brochure for a counselling practice in Portland, OR.

## Tech Stack

- **Plain HTML + CSS** — no framework, no build step
- **Firebase Hosting** — free tier, automatic SSL, CDN, simple CLI deploy
- **Google Fonts** — Playfair Display (headings), Inter (body)
- **Minimal JS** — hamburger menu toggle and smooth scroll only

## File Structure

```
smueller_counselling/
├── firebase.json
├── .firebaserc
└── public/
    ├── index.html
    ├── css/
    │   └── style.css
    └── images/
```

## Page Sections (top to bottom)

1. **Navigation** — Sticky top bar. "Sarah Mueller, LPC" left, anchor links right (About, Services, Fees, Resources, Contact). Hamburger menu on mobile.
2. **Hero** — Full-width calm banner. Name, tagline ("Counseling, Supervision and Consulting"), welcoming intro. Soft gradient or nature-inspired background.
3. **About / Approach** — Trauma-informed treatment philosophy, EMDR specialization, therapeutic approach narrative.
4. **Concerns Treated** — Grid/card layout of treatment areas: Trauma/PTSD, Anxiety, Depression, Domestic/Interpersonal Violence, Gender Transition, Grief and Loss, Life Transitions, Relationship Concerns, Stress Management.
5. **Fees & Insurance** — Session types and rates (Intake $200, 50-min $150, 90-min $200). Accepted insurance (Regence BCBS, Providence, PacificSource). Sliding scale note.
6. **Resources** — Crisis lines, local support groups, national organizations, smartphone apps. Organized under subheadings.
7. **Contact** — Phone (971-808-0449), email (sarah@sarahmueller.net), address (1235 SE Division St, Ste 106C, Portland OR 97202). Optional embedded Google Maps.
8. **Footer** — Copyright line.

## Visual Design

### Colour Palette

| Role              | Colour    | Hex       |
|-------------------|-----------|-----------|
| Primary           | Sage green| `#7C9A82` |
| Primary dark      | Deep forest| `#4A6B52`|
| Background        | Warm white| `#FAFAF7` |
| Section alt       | Soft cream| `#F2F0EB` |
| Body text         | Charcoal  | `#3A3A3A` |
| Accent            | Muted gold| `#C4A96A` |

### Typography

- **Headings:** Playfair Display (serif) — warm, professional
- **Body:** Inter (sans-serif) — clean, readable

### Style Notes

- Generous white space and padding
- Alternating section backgrounds (warm white / soft cream) for visual rhythm
- Rounded corners on cards
- Subtle section transitions (no harsh dividers)
- Smooth scroll between sections
- Gentle CSS-only fade-in animations on scroll
- Fully responsive — mobile-first design

## Hosting & Deployment

- Firebase Hosting via `firebase deploy`
- Custom domain configured in Firebase console
- SSL certificates managed and auto-renewed by Firebase
- DNS: point domain A/CNAME records to Firebase

## Content Source

All content preserved from existing WordPress site at www.sarahmueller.net. No dynamic features (forms, comments, CMS). Contact via phone and email only.
