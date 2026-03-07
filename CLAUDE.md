# Bold Health – Design System Rules for Claude Code

This project is a **React 18 + Tailwind CSS prototype** (CDN-based, no build step) for **Bold Health's Direct Provider Booking flow**. All React components are written inline inside HTML files using Babel standalone for JSX. Follow every rule below when implementing or modifying UI.

---

## 1. Project Structure

- All screens/pages are **self-contained HTML files** in the project root.
- React components are defined inline in `<script type="text/babel">` tags.
- No `src/` directory, no `package.json`, no build step.
- Media assets (images, videos) live in `./screens/`.

**Key files:**
| File | Purpose |
|------|---------|
| `index.html` | Main landing → enrollment flow |
| `provider direct booking.html` | Provider booking flow |
| `EC to Dashboard.html` | Enrollment → Dashboard |
| `LP to Dashboard.html` | Landing Page → Dashboard |
| `Zoom tutorial.html` | Tutorial page |

---

## 2. Tech Stack (CDN-only)

```html
<!-- React 18 -->
<script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>

<!-- JSX Transformation -->
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

<!-- Tailwind CSS -->
<script src="https://cdn.tailwindcss.com"></script>

<!-- Icons: Material Symbols Outlined -->
<link href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@20..48,100..700,0..1,-50..200">

<!-- Typography: Inter -->
<link href="https://fonts.googleapis.com/css2?family=Inter:ital,wght@0,400;0,500;0,600;0,700;0,800;1,800&display=swap">
```

---

## 3. Design Tokens

### Colors (defined in Tailwind config per HTML file)

```javascript
tailwind.config = {
  theme: {
    extend: {
      colors: {
        purple: {
          brand: '#5200D4',   // Primary CTA, links, focus rings
          dark:  '#29006A',   // Headings on light bg
          mid:   '#3E009F',   // Secondary CTAs, hover states
          light: '#EBF0FF',   // Subtle backgrounds, chips
          pale:  '#F3E8FF',   // Tag backgrounds, highlights
        },
        yellow: {
          brand: '#FFCC1A',   // Accent, hero highlights
        },
        brand: {
          purple:      '#7C3AED',
          lightPurple: '#F3E8FF',
          yellow:      '#FEF3C7',
          yellowBorder:'#FDE68A',
          green:       '#E6FFFA',
          text:        '#111827',
          muted:       '#6B7280',
        },
        ink: '#140D26',       // Primary body text
      },
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
      },
      boxShadow: {
        card: '0 2px 8px -2px rgba(0,0,0,0.05), 0 4px 12px -2px rgba(0,0,0,0.02)',
      },
      animation: {
        slideUp: 'slideUp 0.3s cubic-bezier(0.16, 1, 0.3, 1)',
        revealIn: 'revealIn 0.6s ease-out forwards',
      },
    },
  },
};
```

### Token Rules
- **IMPORTANT:** Never hardcode hex colors. Always use Tailwind token classes (e.g., `text-purple-brand`, `bg-purple-light`, `border-yellow-brand`).
- **IMPORTANT:** Never hardcode pixel values for spacing — use Tailwind's spacing scale (`p-4`, `gap-3`, `mt-6`, etc.).
- Exception: pixel values are acceptable only for font-size in `text-[14px]` when the scale doesn't have an exact match.

---

## 4. Typography

| Role | Class | Weight |
|------|-------|--------|
| Display heading | `text-[28px] font-bold leading-tight` | 700 |
| Section heading | `text-[22px] font-bold` | 700 |
| Card heading | `text-[18px] font-semibold` | 600 |
| Body (default) | `text-[16px] font-normal leading-relaxed` | 400 |
| Body small | `text-[14px] font-normal` | 400 |
| Label / caption | `text-[13px] font-medium text-brand-muted` | 500 |
| Button text | `text-[16px] font-semibold` | 600 |

- **IMPORTANT:** Never go below `text-[13px]` — body text must be ≥ 14px for readability.
- Line height: use `leading-relaxed` (1.625) for body, `leading-tight` (1.25) for headings.
- Font family is always Inter — never override.

---

## 5. Component Patterns

### Naming Conventions
- Screen components: `[Feature]Screen` (e.g., `ScheduleScreen`, `EligibilityScreen`)
- UI primitives: simple nouns (e.g., `Icon`, `Header`, `ActionButton`)
- Constants: `CONSTANT_CASE` (e.g., `US_STATES`, `GEMINI_MODEL`)

### Icon Usage (Material Symbols)
```jsx
// Base Icon component — always use this, never raw <span>
const Icon = ({ name, className = '', fill = false }) => (
  <span
    className={`material-symbols-outlined ${className}`}
    style={{ fontVariationSettings: `'FILL' ${fill ? 1 : 0}, 'wght' 400, 'GRAD' 0, 'opsz' 24` }}
    aria-hidden="true"  // REQUIRED: icons are decorative; label the parent button instead
  >
    {name}
  </span>
);
```
- **IMPORTANT:** Never install icon libraries. All icons come from Material Symbols via Google Fonts CDN.
- **IMPORTANT:** Always set `aria-hidden="true"` on `<Icon>` — the parent interactive element must have its own accessible label.

### Button Patterns
```jsx
// Primary CTA
<button className="w-full bg-purple-brand hover:bg-purple-mid active:bg-purple-dark
  text-white text-[16px] font-semibold rounded-2xl h-14 transition-colors
  focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2
  focus-visible:outline-purple-brand disabled:opacity-40 disabled:cursor-not-allowed"
  aria-label="Continue to next step">
  Continue
</button>

// Secondary / Ghost
<button className="w-full border border-purple-brand text-purple-brand text-[16px]
  font-semibold rounded-2xl h-14 hover:bg-purple-light transition-colors
  focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2
  focus-visible:outline-purple-brand">
  Go back
</button>

// Icon button
<button className="p-2 rounded-full hover:bg-gray-100 transition-colors
  focus-visible:outline focus-visible:outline-2 focus-visible:outline-purple-brand"
  aria-label="Close dialog">
  <Icon name="close" />
</button>
```

### Input Patterns
```jsx
<label htmlFor="dob" className="block text-[14px] font-medium text-ink mb-1">
  Date of birth
</label>
<input
  id="dob"
  type="date"
  aria-required="true"
  aria-describedby="dob-hint"
  className="w-full border border-gray-200 rounded-xl px-4 h-12 text-[16px]
    text-gray-900 placeholder-gray-400 outline-none
    focus:ring-2 focus:ring-purple-brand focus:border-purple-brand
    transition shadow-sm"
/>
<p id="dob-hint" className="text-[13px] text-brand-muted mt-1">
  Format: MM/DD/YYYY
</p>
```

### Card Patterns
```jsx
<div className="bg-white rounded-2xl shadow-card p-4 border border-gray-100">
  {/* content */}
</div>
```

### Multi-step Progress Bar
```jsx
<div role="progressbar" aria-valuenow={step} aria-valuemin={1} aria-valuemax={totalSteps}
  aria-label={`Step ${step} of ${totalSteps}`}
  className="h-1 bg-gray-100 rounded-full overflow-hidden">
  <div className="h-full bg-purple-brand transition-all duration-300 rounded-full"
    style={{ width: `${(step / totalSteps) * 100}%` }} />
</div>
```

---

## 6. Figma MCP Integration

### Required Workflow (never skip)
1. Call `get_design_context` with the node ID and file key first.
2. If the response is too large, call `get_metadata` to get the structure, then re-fetch only the required nodes.
3. Call `get_screenshot` for visual reference.
4. Download assets using the localhost MCP asset URLs provided in the response.
5. Translate the Figma output (React + Tailwind) into **this project's conventions** (CDN stack, custom color tokens, Material Symbols icons, Inter font).
6. Validate the final implementation against the Figma screenshot for 1:1 fidelity.

### Asset Rules
- **IMPORTANT:** Use `localhost` MCP asset URLs directly — do not re-host or download unnecessarily.
- **IMPORTANT:** Do not create placeholder images — if an MCP asset URL exists, use it.
- Store any static assets you download in `./screens/`.
- **IMPORTANT:** Do not install icon packages. All icons are Material Symbols.

---

## 7. Accessibility Standards (WCAG 2.1 AA — Mandatory)

This is a **healthcare product**. Accessibility is non-negotiable. Every UI element must meet or exceed these standards.

### Color Contrast
- **Normal text (< 18px or < 14px bold):** minimum 4.5:1 contrast ratio.
- **Large text (≥ 18px or ≥ 14px bold):** minimum 3:1 contrast ratio.
- **UI components and graphical objects:** minimum 3:1 against adjacent colors.
- **IMPORTANT:** Never convey information by color alone — always pair with text or icon.

| Approved Combinations | Ratio |
|----------------------|-------|
| `#5200D4` on white | 8.5:1 ✅ |
| `#111827` (ink) on white | 16.1:1 ✅ |
| White on `#5200D4` | 8.5:1 ✅ |
| `#6B7280` (muted) on white | 4.6:1 ✅ |
| **AVOID:** `#FFCC1A` on white for text | 1.9:1 ❌ |

### Focus Management
- **IMPORTANT:** Never use `outline-none` without providing a visible custom focus indicator.
- Always use `focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-purple-brand` on interactive elements.
- Multi-step flows: after navigating to a new screen, move focus to the screen heading with `autoFocus` or `ref.focus()`.
- Modals: trap focus inside and restore to trigger element on close.

### Interactive Element Requirements
- **Touch targets:** minimum 44×44px for all interactive elements (buttons, checkboxes, radio, links).
- Use `<button>` for actions — never `<div onClick>` or `<span onClick>`.
- All form controls must have a visible `<label>` (not just placeholder text).
- Placeholder text is supplemental only — never use it as the sole label.

### ARIA Requirements

| Element | Required attributes |
|---------|-------------------|
| Icon (decorative) | `aria-hidden="true"` |
| Icon button | `aria-label="[action]"` on the `<button>` |
| Progress bar | `role="progressbar"`, `aria-valuenow`, `aria-valuemin`, `aria-valuemax`, `aria-label` |
| Modal/dialog | `role="dialog"`, `aria-modal="true"`, `aria-labelledby` pointing to heading |
| Loading spinner | `role="status"`, `aria-label="Loading…"` |
| Accordion | `aria-expanded`, `aria-controls` on trigger; `id` on panel |
| Multi-select list | `role="group"` wrapping checkboxes with `aria-labelledby` |
| Error messages | `aria-live="polite"` on error container; `aria-invalid="true"` on invalid input |
| Dynamic content | `aria-live="polite"` for non-urgent updates; `aria-live="assertive"` only for critical alerts |

### Keyboard Navigation
- All interactive elements reachable by `Tab` key in logical DOM order.
- Modals: `Escape` closes them.
- Accordions: `Space`/`Enter` toggles; arrow keys optional but nice.
- Custom select/dropdown: full arrow-key navigation with `role="listbox"`.
- No keyboard trap outside modals.

### Screen Reader Considerations
- Heading hierarchy must be maintained: `h1` → `h2` → `h3` (never skip levels).
- Every page/screen must have exactly one `<h1>` that describes the screen purpose.
- Images: meaningful `alt` text (not "image" or filename); purely decorative images use `alt=""`.
- Form submission errors: announce with `aria-live="assertive"` and move focus to the first error.

### Reduced Motion
```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```
- **IMPORTANT:** Always include this media query in the `<style>` block of any page with animations.

---

## 8. World-Class Design Principles

These principles guide all UI decisions. Internalize them — don't just follow them mechanically.

### Visual Hierarchy
- One clear primary action per screen. Everything else supports it.
- Use size, weight, and color — not decoration — to establish hierarchy.
- White space is active design. Don't fill space; use it to create breathing room.

### Spatial Rhythm
- 4px base unit. Use multiples: 4, 8, 12, 16, 24, 32, 48.
- Consistent padding inside cards: `p-4` (16px) or `p-5` (20px) for comfort.
- Section separation: `mb-6` (24px) between major sections, `mb-3` (12px) between related items.

### Color with Purpose
- Purple (`#5200D4`): trust, action, brand. Use for primary CTAs, links, active states.
- Yellow (`#FFCC1A`): energy, optimism. Use as accent on dark backgrounds only (never for text on white).
- Gray: structure and hierarchy. `#111827` body, `#6B7280` secondary, `#E5E7EB` borders, `#F9FAFB` backgrounds.
- Red: errors and destructive actions only (`text-red-600`, `border-red-400`).
- Green: success and positive confirmation (`text-green-600`).
- **Never use color for pure decoration** — every color must carry meaning.

### Motion & Animation
- Animate to communicate: slide-up modals feel layered; fade-ins feel content appearing.
- Duration: 150ms for micro-interactions (hover, toggle), 300ms for panel transitions, 600ms for hero reveals.
- Easing: `cubic-bezier(0.16, 1, 0.3, 1)` for entrances (fast out), `ease-out` for reveals.
- **IMPORTANT:** Never animate layout properties (width, height) — use transform and opacity only.

### Content & Copy
- Healthcare copy must be warm, clear, and reassuring — not clinical or bureaucratic.
- Sentence case for all UI copy (not Title Case for body, not ALL CAPS).
- Action buttons use active verbs: "Check coverage", "Schedule visit", "Continue" — not "Next" or "OK".
- Error messages: explain what happened and what to do. Never blame the user.

### Mobile-First
- Design for 375px viewport width first. All layouts must work at 320px minimum.
- Use `sm:` breakpoints to enhance on larger screens.
- Full-width buttons on mobile (`w-full`), constrained on desktop (`sm:max-w-sm`).
- Bottom navigation / sticky CTAs preferred on mobile.

### Emotional Design (Healthcare Context)
- Every interaction should feel **safe, trustworthy, and empowering**.
- Progress indicators reduce anxiety — always show where users are in a flow.
- Loading states must be informative: show what's happening ("Finding your matches…").
- Confirmation screens celebrate the user's action ("Your appointment is booked!").
- Use real doctor names, photos, and credentials — not generic "Dr. Smith".

---

## 9. Accessibility Checklist (before marking any UI task complete)

- [ ] Color contrast ≥ 4.5:1 for all text
- [ ] All interactive elements have visible focus indicators
- [ ] All buttons have descriptive labels (via text or `aria-label`)
- [ ] Icons are `aria-hidden="true"` (decorative) or have accompanying text
- [ ] All form inputs have associated `<label>` elements
- [ ] Heading hierarchy is correct (h1 → h2 → h3)
- [ ] Dynamic content changes announced with `aria-live`
- [ ] Touch targets are ≥ 44×44px
- [ ] Page/screen has a single `<h1>` describing the current context
- [ ] Images have descriptive `alt` text (or `alt=""` if decorative)
- [ ] Modals trap focus and restore on close
- [ ] `prefers-reduced-motion` CSS is included when animations are present
- [ ] No information conveyed by color alone

---

## 10. Example: Accessible Screen Shell

```jsx
const ExampleScreen = () => (
  <div className="min-h-screen bg-white font-sans flex flex-col">
    {/* Skip link for keyboard users */}
    <a href="#main-content"
      className="sr-only focus:not-sr-only focus:fixed focus:top-4 focus:left-4
        bg-purple-brand text-white px-4 py-2 rounded-xl z-50 font-semibold">
      Skip to main content
    </a>

    {/* Header */}
    <header className="sticky top-0 z-10 bg-white/80 backdrop-blur border-b border-gray-100 px-4 h-14 flex items-center">
      <button aria-label="Go back" className="p-2 -ml-2 rounded-full hover:bg-gray-100
        focus-visible:outline focus-visible:outline-2 focus-visible:outline-purple-brand">
        <Icon name="arrow_back" />
      </button>
      {/* Progress */}
      <div className="flex-1 mx-4"
        role="progressbar" aria-valuenow={2} aria-valuemin={1} aria-valuemax={8}
        aria-label="Step 2 of 8">
        <div className="h-1 bg-gray-100 rounded-full">
          <div className="h-full bg-purple-brand rounded-full transition-all" style={{ width: '25%' }} />
        </div>
      </div>
    </header>

    {/* Main content */}
    <main id="main-content" className="flex-1 px-5 pt-8 pb-32 max-w-sm mx-auto w-full">
      <h1 className="text-[26px] font-bold text-ink leading-tight mb-2">
        Tell us about your coverage
      </h1>
      <p className="text-[16px] text-brand-muted mb-8 leading-relaxed">
        We'll verify your benefits to personalize your care.
      </p>

      {/* Form */}
      <form aria-label="Coverage information" noValidate>
        <div className="mb-5">
          <label htmlFor="state" className="block text-[14px] font-medium text-ink mb-1">
            State of residence <span aria-hidden="true" className="text-red-500">*</span>
            <span className="sr-only">(required)</span>
          </label>
          <select id="state" aria-required="true"
            className="w-full border border-gray-200 rounded-xl px-4 h-12 text-[16px]
              text-gray-900 outline-none focus:ring-2 focus:ring-purple-brand
              focus:border-purple-brand bg-white transition shadow-sm appearance-none">
            <option value="">Select your state</option>
          </select>
        </div>

        {/* Error (shown conditionally) */}
        <div role="alert" aria-live="assertive" className="mb-4">
          {/* Error messages rendered here */}
        </div>
      </form>
    </main>

    {/* Sticky CTA */}
    <div className="fixed bottom-0 left-0 right-0 bg-white border-t border-gray-100 px-5 py-4 pb-safe">
      <button type="submit"
        className="w-full bg-purple-brand hover:bg-purple-mid active:bg-purple-dark
          text-white text-[16px] font-semibold rounded-2xl h-14 transition-colors
          focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2
          focus-visible:outline-purple-brand disabled:opacity-40 disabled:cursor-not-allowed">
        Check my coverage
      </button>
    </div>
  </div>
);
```

---

## 11. Figma Design Fidelity Rules

- **1:1 visual parity is the goal.** If the implementation looks different from the Figma screenshot, fix it.
- Map Figma auto-layout to Flexbox (`flex`, `flex-col`, `gap-*`).
- Map Figma frame padding to Tailwind `p-*` / `px-*` / `py-*`.
- Map Figma corner radius to Tailwind `rounded-*` (e.g., 16px → `rounded-2xl`).
- Map Figma shadows to `shadow-card` or Tailwind shadow utilities.
- Map Figma text styles to the typography table in Section 4.
- Map Figma color styles to the token table in Section 3.
- **IMPORTANT:** After implementation, always describe how you validated against the Figma screenshot.
