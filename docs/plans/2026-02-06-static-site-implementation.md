# Static Site Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build a single-page static counselling site and configure Firebase Hosting for deployment.

**Architecture:** Plain HTML + CSS single-page site with minimal JS for mobile nav toggle and smooth scroll. Hosted on Firebase Hosting with automatic SSL.

**Tech Stack:** HTML5, CSS3 (custom properties, flexbox, grid), Google Fonts (Playfair Display, Inter), Firebase Hosting.

---

### Task 1: Project scaffolding and Firebase config

**Files:**
- Create: `public/index.html` (empty boilerplate only)
- Create: `public/css/style.css` (empty file)
- Create: `firebase.json`
- Create: `.firebaserc`

**Step 1: Create directory structure**

Run: `mkdir -p public/css public/images`

**Step 2: Create Firebase config**

Create `firebase.json`:
```json
{
  "hosting": {
    "public": "public",
    "ignore": [
      "firebase.json",
      "**/.*",
      "**/node_modules/**"
    ],
    "headers": [
      {
        "source": "**/*.css",
        "headers": [{ "key": "Cache-Control", "value": "max-age=31536000" }]
      },
      {
        "source": "**/*.html",
        "headers": [{ "key": "Cache-Control", "value": "max-age=3600" }]
      }
    ]
  }
}
```

Create `.firebaserc`:
```json
{
  "projects": {
    "default": ""
  }
}
```

Note: The project ID will be filled in when the Firebase project is created.

**Step 3: Create empty HTML boilerplate**

Create `public/index.html`:
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="Sarah Mueller, LPC — Counseling, Supervision and Consulting in Portland, OR. Specializing in trauma-informed care and EMDR therapy.">
  <title>Sarah Mueller, LPC — Counseling, Supervision and Consulting</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&family=Playfair+Display:wght@400;600;700&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="css/style.css">
</head>
<body>
  <!-- Content will be added in subsequent tasks -->
</body>
</html>
```

Create `public/css/style.css` as an empty file.

**Step 4: Commit**

```bash
git add firebase.json .firebaserc public/
git commit -m "feat: scaffold project structure with Firebase config"
```

---

### Task 2: CSS foundation — reset, custom properties, base typography

**Files:**
- Modify: `public/css/style.css`

**Step 1: Write the CSS foundation**

Write `public/css/style.css` with:

```css
/* ===== Custom Properties ===== */
:root {
  --color-primary: #7C9A82;
  --color-primary-dark: #4A6B52;
  --color-bg: #FAFAF7;
  --color-bg-alt: #F2F0EB;
  --color-text: #3A3A3A;
  --color-accent: #C4A96A;
  --color-white: #FFFFFF;

  --font-heading: 'Playfair Display', Georgia, serif;
  --font-body: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;

  --max-width: 1100px;
  --nav-height: 72px;
  --section-padding: 5rem 1.5rem;
  --border-radius: 8px;
}

/* ===== Reset ===== */
*,
*::before,
*::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html {
  scroll-behavior: smooth;
  scroll-padding-top: var(--nav-height);
}

body {
  font-family: var(--font-body);
  font-size: 1rem;
  line-height: 1.7;
  color: var(--color-text);
  background-color: var(--color-bg);
  -webkit-font-smoothing: antialiased;
}

img {
  max-width: 100%;
  display: block;
}

a {
  color: var(--color-primary-dark);
  text-decoration: none;
}

a:hover {
  color: var(--color-accent);
}

h1, h2, h3 {
  font-family: var(--font-heading);
  font-weight: 600;
  line-height: 1.3;
  color: var(--color-primary-dark);
}

h1 { font-size: 2.5rem; }
h2 { font-size: 2rem; margin-bottom: 1rem; }
h3 { font-size: 1.25rem; margin-bottom: 0.5rem; }

p + p { margin-top: 1rem; }

.container {
  max-width: var(--max-width);
  margin: 0 auto;
  padding: 0 1.5rem;
}

/* Alternating section backgrounds */
section:nth-of-type(even) {
  background-color: var(--color-bg-alt);
}

section {
  padding: var(--section-padding);
}

.section-intro {
  text-align: center;
  max-width: 700px;
  margin: 0 auto 3rem;
}
```

**Step 2: Verify visually**

Open `public/index.html` in browser. Should see a blank warm-white page with no default margins.

**Step 3: Commit**

```bash
git add public/css/style.css
git commit -m "feat: add CSS foundation with design tokens and reset"
```

---

### Task 3: Navigation bar (HTML + CSS + mobile toggle JS)

**Files:**
- Modify: `public/index.html` — add nav markup and script
- Modify: `public/css/style.css` — add nav styles

**Step 1: Add nav HTML to index.html**

Inside `<body>`, add:
```html
<header class="nav" id="nav">
  <div class="container nav__inner">
    <a href="#hero" class="nav__brand">
      <span class="nav__name">Sarah Mueller</span>
      <span class="nav__title">, LPC</span>
    </a>
    <button class="nav__toggle" aria-label="Toggle menu" aria-expanded="false">
      <span></span>
      <span></span>
      <span></span>
    </button>
    <nav class="nav__menu">
      <a href="#about" class="nav__link">About</a>
      <a href="#services" class="nav__link">Services</a>
      <a href="#fees" class="nav__link">Fees</a>
      <a href="#resources" class="nav__link">Resources</a>
      <a href="#contact" class="nav__link">Contact</a>
    </nav>
  </div>
</header>
```

Add before `</body>`:
```html
<script>
  const toggle = document.querySelector('.nav__toggle');
  const menu = document.querySelector('.nav__menu');
  toggle.addEventListener('click', () => {
    const expanded = toggle.getAttribute('aria-expanded') === 'true';
    toggle.setAttribute('aria-expanded', !expanded);
    menu.classList.toggle('nav__menu--open');
  });
  document.querySelectorAll('.nav__link').forEach(link => {
    link.addEventListener('click', () => {
      toggle.setAttribute('aria-expanded', 'false');
      menu.classList.remove('nav__menu--open');
    });
  });
</script>
```

**Step 2: Add nav CSS**

Append to `public/css/style.css`:

```css
/* ===== Navigation ===== */
.nav {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  height: var(--nav-height);
  background: var(--color-white);
  box-shadow: 0 1px 8px rgba(0, 0, 0, 0.06);
  z-index: 1000;
  display: flex;
  align-items: center;
}

.nav__inner {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
}

.nav__brand {
  font-family: var(--font-heading);
  font-size: 1.35rem;
  color: var(--color-primary-dark);
  font-weight: 700;
}

.nav__brand:hover { color: var(--color-primary-dark); }

.nav__title {
  font-weight: 400;
  color: var(--color-primary);
}

.nav__menu {
  display: flex;
  gap: 2rem;
  align-items: center;
}

.nav__link {
  font-size: 0.95rem;
  font-weight: 500;
  color: var(--color-text);
  transition: color 0.2s;
  position: relative;
}

.nav__link:hover {
  color: var(--color-primary);
}

.nav__toggle {
  display: none;
  flex-direction: column;
  gap: 5px;
  background: none;
  border: none;
  cursor: pointer;
  padding: 4px;
}

.nav__toggle span {
  display: block;
  width: 24px;
  height: 2px;
  background: var(--color-text);
  transition: transform 0.3s, opacity 0.3s;
}

/* Mobile nav */
@media (max-width: 768px) {
  .nav__toggle { display: flex; }

  .nav__menu {
    position: fixed;
    top: var(--nav-height);
    left: 0;
    right: 0;
    background: var(--color-white);
    flex-direction: column;
    padding: 1.5rem;
    gap: 1.25rem;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
    transform: translateY(-110%);
    transition: transform 0.3s ease;
  }

  .nav__menu--open {
    transform: translateY(0);
  }

  .nav__toggle[aria-expanded="true"] span:nth-child(1) {
    transform: rotate(45deg) translate(5px, 5px);
  }
  .nav__toggle[aria-expanded="true"] span:nth-child(2) {
    opacity: 0;
  }
  .nav__toggle[aria-expanded="true"] span:nth-child(3) {
    transform: rotate(-45deg) translate(5px, -5px);
  }
}
```

**Step 3: Verify**

Open in browser. Nav bar should be fixed at top, links should be visible on desktop. Resize to mobile — hamburger should appear, tapping it should slide the menu down.

**Step 4: Commit**

```bash
git add public/index.html public/css/style.css
git commit -m "feat: add sticky navigation with responsive hamburger menu"
```

---

### Task 4: Hero section

**Files:**
- Modify: `public/index.html` — add hero markup after `</header>`
- Modify: `public/css/style.css` — add hero styles

**Step 1: Add hero HTML**

After the closing `</header>`, add:

```html
<section class="hero" id="hero">
  <div class="container hero__content">
    <h1>Sarah Mueller<span class="hero__lpc">, LPC</span></h1>
    <p class="hero__tagline">Counseling, Supervision and Consulting</p>
    <p class="hero__intro">Creating a stable, safe environment that facilitates healing. Specializing in trauma-informed care in Portland, Oregon.</p>
    <a href="#contact" class="btn">Get in Touch</a>
  </div>
</section>
```

**Step 2: Add hero and button CSS**

Append to `public/css/style.css`:

```css
/* ===== Hero ===== */
.hero {
  margin-top: var(--nav-height);
  padding: 6rem 1.5rem;
  background: linear-gradient(135deg, #e8f0e9 0%, #d4e4d7 40%, #c8ddd0 100%);
  text-align: center;
}

.hero__content {
  max-width: 700px;
  margin: 0 auto;
}

.hero h1 {
  font-size: 3rem;
  margin-bottom: 0.5rem;
}

.hero__lpc {
  font-weight: 400;
  color: var(--color-primary);
}

.hero__tagline {
  font-family: var(--font-heading);
  font-size: 1.25rem;
  color: var(--color-primary);
  margin-bottom: 1.5rem;
}

.hero__intro {
  font-size: 1.1rem;
  line-height: 1.8;
  margin-bottom: 2rem;
  color: var(--color-text);
}

/* ===== Button ===== */
.btn {
  display: inline-block;
  padding: 0.85rem 2rem;
  background-color: var(--color-primary);
  color: var(--color-white);
  font-weight: 600;
  font-size: 0.95rem;
  border-radius: var(--border-radius);
  transition: background-color 0.2s;
}

.btn:hover {
  background-color: var(--color-primary-dark);
  color: var(--color-white);
}

@media (max-width: 768px) {
  .hero { padding: 4rem 1.5rem; }
  .hero h1 { font-size: 2.2rem; }
}
```

**Step 3: Verify**

Open in browser. Hero should show below the nav with a soft green gradient, centered text, and a sage-green CTA button.

**Step 4: Commit**

```bash
git add public/index.html public/css/style.css
git commit -m "feat: add hero section with gradient background"
```

---

### Task 5: About / Approach section

**Files:**
- Modify: `public/index.html` — add about section after hero
- Modify: `public/css/style.css` — add about styles

**Step 1: Add about HTML**

After the hero `</section>`, add:

```html
<section id="about">
  <div class="container section-intro">
    <h2>About My Approach</h2>
    <p>I am trained in different trauma informed treatments, including <strong>EMDR Therapy</strong> (Eye Movement Desensitization and Reprocessing) and specialize in the treatment of trauma.</p>
    <p>Evidence-based methods are used to stabilize and build skills before trauma is addressed. Treatment is targeted to rebuild neurological pathways and remain within the client's window of emotional tolerance.</p>
    <p>I work to provide a stable, safe environment that facilitates healing.</p>
  </div>
</section>
```

**Step 2: Verify**

Open in browser. Section should appear below hero with centered text on a warm-white background.

**Step 3: Commit**

```bash
git add public/index.html
git commit -m "feat: add about/approach section"
```

---

### Task 6: Concerns Treated section (card grid)

**Files:**
- Modify: `public/index.html` — add services section
- Modify: `public/css/style.css` — add card grid styles

**Step 1: Add services HTML**

After the about `</section>`, add:

```html
<section id="services">
  <div class="container">
    <div class="section-intro">
      <h2>Concerns Treated</h2>
    </div>
    <div class="card-grid">
      <div class="card">Trauma &amp; PTSD</div>
      <div class="card">Anxiety</div>
      <div class="card">Depression</div>
      <div class="card">Domestic &amp; Interpersonal Violence</div>
      <div class="card">Gender Transition</div>
      <div class="card">Grief &amp; Loss</div>
      <div class="card">Life Transitions</div>
      <div class="card">Relationship Concerns</div>
      <div class="card">Stress Management</div>
    </div>
  </div>
</section>
```

**Step 2: Add card grid CSS**

Append to `public/css/style.css`:

```css
/* ===== Card Grid ===== */
.card-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1.25rem;
}

.card {
  background: var(--color-white);
  padding: 1.5rem;
  border-radius: var(--border-radius);
  text-align: center;
  font-weight: 500;
  color: var(--color-primary-dark);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
  border-left: 3px solid var(--color-primary);
  transition: transform 0.2s, box-shadow 0.2s;
}

.card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.08);
}

@media (max-width: 768px) {
  .card-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 480px) {
  .card-grid {
    grid-template-columns: 1fr;
  }
}
```

**Step 3: Verify**

Open in browser. Should show 3-column grid on desktop, 2 on tablet, 1 on mobile. Cards should have white background with a sage-green left border.

**Step 4: Commit**

```bash
git add public/index.html public/css/style.css
git commit -m "feat: add concerns treated section with card grid"
```

---

### Task 7: Fees & Insurance section

**Files:**
- Modify: `public/index.html` — add fees section
- Modify: `public/css/style.css` — add fees styles

**Step 1: Add fees HTML**

After the services `</section>`, add:

```html
<section id="fees">
  <div class="container">
    <div class="section-intro">
      <h2>Fees &amp; Insurance</h2>
    </div>
    <div class="fees-grid">
      <div class="fee-card">
        <h3>Intake Session</h3>
        <p class="fee-card__price">$200</p>
      </div>
      <div class="fee-card">
        <h3>Individual Session</h3>
        <p class="fee-card__detail">50 minutes</p>
        <p class="fee-card__price">$150</p>
      </div>
      <div class="fee-card">
        <h3>Extended Session</h3>
        <p class="fee-card__detail">90 minutes</p>
        <p class="fee-card__price">$200</p>
      </div>
    </div>
    <div class="fees-info">
      <div class="fees-info__block">
        <h3>Insurance Accepted</h3>
        <ul>
          <li>Regence Blue Cross Blue Shield</li>
          <li>Providence</li>
          <li>PacificSource</li>
        </ul>
      </div>
      <div class="fees-info__block">
        <h3>Sliding Scale</h3>
        <p>Sliding scale rates are available based on income considerations. Please inquire for details.</p>
      </div>
    </div>
  </div>
</section>
```

**Step 2: Add fees CSS**

Append to `public/css/style.css`:

```css
/* ===== Fees ===== */
.fees-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1.25rem;
  margin-bottom: 3rem;
}

.fee-card {
  background: var(--color-white);
  padding: 2rem 1.5rem;
  border-radius: var(--border-radius);
  text-align: center;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.fee-card__price {
  font-family: var(--font-heading);
  font-size: 2rem;
  font-weight: 700;
  color: var(--color-primary-dark);
  margin-top: 0.5rem;
}

.fee-card__detail {
  color: var(--color-primary);
  font-size: 0.9rem;
}

.fees-info {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
  max-width: 700px;
  margin: 0 auto;
}

.fees-info__block h3 {
  color: var(--color-primary-dark);
  margin-bottom: 0.75rem;
}

.fees-info__block ul {
  list-style: none;
}

.fees-info__block li {
  padding: 0.3rem 0;
  padding-left: 1rem;
  position: relative;
}

.fees-info__block li::before {
  content: '';
  position: absolute;
  left: 0;
  top: 50%;
  width: 6px;
  height: 6px;
  background: var(--color-primary);
  border-radius: 50%;
  transform: translateY(-50%);
}

@media (max-width: 768px) {
  .fees-grid { grid-template-columns: 1fr; max-width: 320px; margin: 0 auto 3rem; }
  .fees-info { grid-template-columns: 1fr; }
}
```

**Step 3: Verify**

Open in browser. Three fee cards in a row on desktop, stacked on mobile. Insurance and sliding scale info below.

**Step 4: Commit**

```bash
git add public/index.html public/css/style.css
git commit -m "feat: add fees and insurance section"
```

---

### Task 8: Resources section

**Files:**
- Modify: `public/index.html` — add resources section
- Modify: `public/css/style.css` — add resources styles

**Step 1: Add resources HTML**

After the fees `</section>`, add:

```html
<section id="resources">
  <div class="container">
    <div class="section-intro">
      <h2>Resources</h2>
    </div>
    <div class="resources-grid">
      <div class="resource-block">
        <h3>Local Crisis Lines</h3>
        <ul>
          <li>Clackamas County Crisis Line: <a href="tel:5036558585">503-655-8585</a></li>
          <li>Lines for Life: <a href="tel:8002738255">800-273-TALK</a></li>
          <li>Multnomah County Crisis Line: <a href="tel:5039884888">503-988-4888</a></li>
          <li>Portland Women's Crisis Line: <a href="tel:5032355333">503-235-5333</a></li>
          <li>Sexual Assault Resource Center: <a href="tel:5036405311">503-640-5311</a></li>
          <li>Washington County Crisis Line: <a href="tel:5032919111">503-291-9111</a></li>
        </ul>
      </div>
      <div class="resource-block">
        <h3>National Organizations</h3>
        <ul>
          <li><a href="https://www.isst-d.org/" target="_blank" rel="noopener">ISSTD</a> &mdash; International Society for the Study of Trauma &amp; Dissociation</li>
          <li><a href="https://www.nami.org" target="_blank" rel="noopener">NAMI</a> &mdash; National Alliance for Mental Illness</li>
          <li><a href="https://www.oaasisoregon.org" target="_blank" rel="noopener">OAASIS</a> &mdash; Advocates and Survivors in Service</li>
          <li><a href="https://www.rainn.org" target="_blank" rel="noopener">RAINN</a> &mdash; Rape, Abuse, Incest National Network</li>
          <li><a href="https://www.sidran.org" target="_blank" rel="noopener">Sidran Institute</a> &mdash; Traumatic Stress Education and Advocacy</li>
        </ul>
      </div>
      <div class="resource-block">
        <h3>Local Support Groups</h3>
        <ul>
          <li><a href="https://www.portlandsupportgroups.com" target="_blank" rel="noopener">Portland Support Groups</a></li>
        </ul>
      </div>
      <div class="resource-block">
        <h3>Helpful Apps</h3>
        <ul>
          <li>Breathe2Relax</li>
          <li>Gratitude Journal</li>
          <li>Mindfulness</li>
          <li>PTSD Coach</li>
          <li>Simply Being</li>
          <li>T2 MoodTracker</li>
          <li>Take a Break</li>
        </ul>
      </div>
    </div>
  </div>
</section>
```

**Step 2: Add resources CSS**

Append to `public/css/style.css`:

```css
/* ===== Resources ===== */
.resources-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
}

.resource-block {
  background: var(--color-white);
  padding: 2rem;
  border-radius: var(--border-radius);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.resource-block h3 {
  color: var(--color-primary-dark);
  margin-bottom: 1rem;
  padding-bottom: 0.5rem;
  border-bottom: 2px solid var(--color-primary);
}

.resource-block ul {
  list-style: none;
}

.resource-block li {
  padding: 0.4rem 0;
  font-size: 0.95rem;
}

.resource-block a {
  color: var(--color-primary-dark);
  text-decoration: underline;
  text-decoration-color: var(--color-primary);
  text-underline-offset: 2px;
}

.resource-block a:hover {
  color: var(--color-primary);
}

@media (max-width: 768px) {
  .resources-grid { grid-template-columns: 1fr; }
}
```

**Step 3: Verify**

Open in browser. Four resource blocks in a 2x2 grid on desktop, single column on mobile. Links should be underlined and clickable.

**Step 4: Commit**

```bash
git add public/index.html public/css/style.css
git commit -m "feat: add resources section with crisis lines and organizations"
```

---

### Task 9: Contact section

**Files:**
- Modify: `public/index.html` — add contact section
- Modify: `public/css/style.css` — add contact styles

**Step 1: Add contact HTML**

After the resources `</section>`, add:

```html
<section id="contact" class="contact">
  <div class="container">
    <div class="section-intro">
      <h2>Contact</h2>
    </div>
    <div class="contact-grid">
      <div class="contact-info">
        <div class="contact-item">
          <h3>Phone</h3>
          <a href="tel:9718080449">971-808-0449</a>
        </div>
        <div class="contact-item">
          <h3>Email</h3>
          <a href="mailto:sarah@sarahmueller.net">sarah@sarahmueller.net</a>
        </div>
        <div class="contact-item">
          <h3>Location</h3>
          <p>1235 SE Division St, Ste 106C<br>Portland, OR 97202</p>
        </div>
      </div>
      <div class="contact-map">
        <iframe
          title="Office location"
          src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d2796.123456789!2d-122.6554!3d45.5045!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x5495a0a3e0a0a0a1%3A0x1234567890abcdef!2s1235+SE+Division+St%2C+Portland%2C+OR+97202!5e0!3m2!1sen!2sus!4v1700000000000"
          width="100%"
          height="300"
          style="border:0; border-radius: 8px;"
          allowfullscreen=""
          loading="lazy"
          referrerpolicy="no-referrer-when-downgrade">
        </iframe>
      </div>
    </div>
  </div>
</section>
```

Note: The Google Maps embed URL will need to be verified/corrected during implementation. Generate a proper embed URL for "1235 SE Division St, Ste 106C, Portland, OR 97202" from Google Maps.

**Step 2: Add contact CSS**

Append to `public/css/style.css`:

```css
/* ===== Contact ===== */
.contact {
  background-color: var(--color-bg) !important;
}

.contact-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 3rem;
  align-items: start;
}

.contact-item {
  margin-bottom: 2rem;
}

.contact-item h3 {
  color: var(--color-primary-dark);
  margin-bottom: 0.5rem;
}

.contact-item a {
  font-size: 1.1rem;
  color: var(--color-primary-dark);
  text-decoration: underline;
  text-decoration-color: var(--color-primary);
  text-underline-offset: 2px;
}

.contact-item a:hover {
  color: var(--color-primary);
}

@media (max-width: 768px) {
  .contact-grid { grid-template-columns: 1fr; }
}
```

**Step 3: Verify**

Open in browser. Contact info on left, map on right. Stacks vertically on mobile. Phone and email links should be clickable.

**Step 4: Commit**

```bash
git add public/index.html public/css/style.css
git commit -m "feat: add contact section with map embed"
```

---

### Task 10: Footer

**Files:**
- Modify: `public/index.html` — add footer after contact section
- Modify: `public/css/style.css` — add footer styles

**Step 1: Add footer HTML**

After the contact `</section>`, before the `<script>` tag, add:

```html
<footer class="footer">
  <div class="container">
    <p>&copy; 2026 Sarah Mueller, LPC. All rights reserved.</p>
  </div>
</footer>
```

**Step 2: Add footer CSS**

Append to `public/css/style.css`:

```css
/* ===== Footer ===== */
.footer {
  background: var(--color-primary-dark);
  color: rgba(255, 255, 255, 0.8);
  text-align: center;
  padding: 2rem 1.5rem;
  font-size: 0.9rem;
}
```

**Step 3: Verify**

Open in browser. Dark green footer at the bottom with white copyright text.

**Step 4: Commit**

```bash
git add public/index.html public/css/style.css
git commit -m "feat: add footer"
```

---

### Task 11: CSS fade-in scroll animations

**Files:**
- Modify: `public/css/style.css` — add animation styles
- Modify: `public/index.html` — add IntersectionObserver script

**Step 1: Add animation CSS**

Append to `public/css/style.css`:

```css
/* ===== Scroll Animations ===== */
.fade-in {
  opacity: 0;
  transform: translateY(20px);
  transition: opacity 0.6s ease, transform 0.6s ease;
}

.fade-in.visible {
  opacity: 1;
  transform: translateY(0);
}
```

**Step 2: Add class `fade-in` to each section's inner content**

Add `class="fade-in"` to: the about `.section-intro`, the services `.card-grid`, the fees `.fees-grid`, each `.resource-block`, and the `.contact-grid`.

**Step 3: Add IntersectionObserver to the script block**

Add to the existing `<script>` block:

```javascript
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.classList.add('visible');
    }
  });
}, { threshold: 0.1 });

document.querySelectorAll('.fade-in').forEach(el => observer.observe(el));
```

**Step 4: Verify**

Open in browser, scroll down. Elements should fade in as they enter the viewport.

**Step 5: Commit**

```bash
git add public/index.html public/css/style.css
git commit -m "feat: add scroll-triggered fade-in animations"
```

---

### Task 12: Final polish and responsive testing

**Files:**
- Modify: `public/index.html` — add favicon link, Open Graph meta tags
- Modify: `public/css/style.css` — any responsive fixes

**Step 1: Add meta tags to `<head>`**

Add Open Graph tags after the existing `<meta>` tags:

```html
<meta property="og:title" content="Sarah Mueller, LPC — Counseling in Portland, OR">
<meta property="og:description" content="Trauma-informed counseling, supervision and consulting. Specializing in EMDR therapy.">
<meta property="og:type" content="website">
<meta property="og:url" content="https://www.sarahmueller.net">
```

**Step 2: Review full page responsive behaviour**

Test at these widths: 1200px, 768px, 480px, 375px. Verify:
- Nav collapses to hamburger at 768px
- Card grids reflow correctly
- Text remains readable
- No horizontal overflow
- Touch targets are large enough on mobile

**Step 3: Commit**

```bash
git add public/index.html public/css/style.css
git commit -m "feat: add Open Graph meta tags and responsive polish"
```

---

### Summary

| Task | Description |
|------|-------------|
| 1 | Project scaffolding and Firebase config |
| 2 | CSS foundation — reset, custom properties, base typography |
| 3 | Navigation bar with mobile hamburger menu |
| 4 | Hero section with gradient background |
| 5 | About / Approach section |
| 6 | Concerns Treated card grid |
| 7 | Fees & Insurance section |
| 8 | Resources section |
| 9 | Contact section with map |
| 10 | Footer |
| 11 | Scroll fade-in animations |
| 12 | Final polish and responsive testing |
