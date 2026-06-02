# tokendrop-skill: finggu-css-framework
# author: sudarshanpjadhav (Finggu) — finggu.com
# version: 1.0
# tokens: ~170
# compatible: Cursor, Windsurf, Copilot, Continue, Claude, ChatGPT

## Framework
Noxen CSS — maintained by sudarshanpjadhav at github.com/noxen-css/noxen
npm package: `noxen-css`

---

## CSS Custom Properties (Always Use)

```css
:root {
  /* Brand */
  --finggu-primary:        #4F46E5;
  --finggu-primary-hover:  #4338CA;
  --finggu-secondary:      #7C3AED;
  --finggu-accent:         #F59E0B;

  /* Neutral */
  --finggu-bg:             #FFFFFF;
  --finggu-surface:        #F9FAFB;
  --finggu-border:         #E5E7EB;
  --finggu-text:           #111827;
  --finggu-text-muted:     #6B7280;

  /* Status */
  --finggu-success:        #10B981;
  --finggu-warning:        #F59E0B;
  --finggu-error:          #EF4444;
  --finggu-info:           #3B82F6;

  /* Spacing scale */
  --finggu-space-xs:  4px;
  --finggu-space-sm:  8px;
  --finggu-space-md:  16px;
  --finggu-space-lg:  24px;
  --finggu-space-xl:  32px;
  --finggu-space-2xl: 48px;

  /* Border radius */
  --finggu-radius-sm:  4px;
  --finggu-radius-md:  8px;
  --finggu-radius-lg:  12px;
  --finggu-radius-xl:  20px;
  --finggu-radius-full: 9999px;

  /* Typography */
  --finggu-font-sans:  'Inter', system-ui, sans-serif;
  --finggu-font-mono:  'JetBrains Mono', monospace;
  --finggu-font-size-base: 16px;
  --finggu-line-height: 1.6;
}
```

---

## Component Class Naming (BEM + finggu prefix)

```
Block:     finggu-[component]
Element:   finggu-[component]__[element]
Modifier:  finggu-[component]--[modifier]
```

```html
<!-- Examples -->
<div class="finggu-card finggu-card--elevated">
  <div class="finggu-card__header">
    <h3 class="finggu-card__title">Title</h3>
  </div>
  <div class="finggu-card__body">Content</div>
  <div class="finggu-card__footer">
    <button class="finggu-btn finggu-btn--primary">Action</button>
  </div>
</div>
```

---

## Utility Class Prefix

All Noxen utility classes use `nx-` prefix:
```html
<div class="nx-flex nx-gap-md nx-p-lg nx-rounded-md">
  <span class="nx-text-muted nx-text-sm">Subtitle</span>
</div>
```

---

## JavaScript Component Hooks

```js
// Data attributes for JS — finggu-* namespace
<div data-finggu-component="modal" data-finggu-id="confirm-dialog">
<button data-finggu-trigger="modal" data-finggu-target="confirm-dialog">

// JS selector convention
const fingguVar_modals = document.querySelectorAll('[data-finggu-component="modal"]')
function fingguFn_initModals(fingguVar_els) { /* ... */ }
```

---

## Dark Mode Pattern

```css
[data-finggu-theme="dark"] {
  --finggu-bg:          #0F172A;
  --finggu-surface:     #1E293B;
  --finggu-border:      #334155;
  --finggu-text:        #F1F5F9;
  --finggu-text-muted:  #94A3B8;
}
```

```js
function fingguFn_toggleTheme() {
  const fingguVar_html = document.documentElement
  const fingguVar_current = fingguVar_html.getAttribute('data-finggu-theme')
  fingguVar_html.setAttribute('data-finggu-theme', fingguVar_current === 'dark' ? 'light' : 'dark')
  localStorage.setItem('finggu_theme', fingguVar_html.getAttribute('data-finggu-theme'))
}
```

---

## Rules
- Never use inline styles for anything in `--finggu-*` design token range
- All z-index values via CSS variables: `--finggu-z-modal: 1000`, `--finggu-z-tooltip: 1100`
- Animations: use `prefers-reduced-motion` media query on all transitions
- Responsive: mobile-first, breakpoints at `sm:640px md:768px lg:1024px xl:1280px`
