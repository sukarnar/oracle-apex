# Modern APEX Skin

A clean, modern stylesheet for the **Oracle APEX Universal Theme**: indigo brand color, Inter font, soft shadows, rounded regions, a frosted header, pill-style navigation, refined Interactive Reports and Grids, dark mode, and a set of utility classes.

It is layered **on top of** the Universal Theme. Nothing in the theme itself is modified, so APEX upgrades remain safe.

---

## Contents

| File | Purpose |
|------|---------|
| `modern-apex.css` | The stylesheet: design tokens, Universal Theme variable overrides and component refinements |
| `README.md` | This guide |

---

## Requirements

- Oracle APEX with the **Universal Theme** (theme 42)
- Recommended base style: **Vita**, or **Redwood Light** on APEX 23.1+
- A modern browser. The skin uses `color-mix()` and `backdrop-filter`; older browsers fall back gracefully.

---

## Installation

### 1. Upload the stylesheet

**Shared Components → Static Application Files → Create File**, then upload `modern-apex.css`.

> To share the skin across several apps, upload it to **Static Workspace Files** instead and reference it with `#WORKSPACE_FILES#`.

### 2. Attach it to a Theme Style (recommended)

1. **Shared Components → Themes → Universal Theme → Styles**
2. Copy your base style (Vita or Redwood Light) and name the copy **Modern**.
3. In **File URLs**, enter:
   ```
   #APP_FILES#modern-apex#MIN#.css
   ```
4. Set **Modern** as the **Current** style.

### Alternative: global CSS file URL

If you don't need theme-style switching, go to **Shared Components → User Interface Attributes → CSS → File URLs** and enter:
```
#APP_FILES#modern-apex#MIN#.css
```

### 3. (Optional) Minified version

Upload a minified copy named `modern-apex.min.css`. When the application isn't in debug mode, APEX resolves `#MIN#` to `.min` automatically.

---

## Customizing the brand

All colors, radii, shadows and fonts are defined as tokens in the `:root` block at the top of the file. Changing the brand usually means editing only these lines:

```css
:root {
  --brand-500: #4f46e5;   /* primary */
  --brand-600: #4338ca;   /* primary hover */
  --brand-50:  #eef2ff;   /* light tint for highlights */
  --accent-500: #0ea5e9;

  --radius:    10px;      /* general corner radius */
  --radius-lg: 14px;      /* regions, cards, dialogs */

  --font: "Inter", system-ui, sans-serif;
}
```

The tokens are mapped onto the Universal Theme's own variables (`--ut-palette-primary`, `--ut-component-border-radius`, and so on), so built-in components pick up the brand automatically.

> **Note:** Universal Theme variable names vary slightly between APEX versions. To check which ones your version uses, inspect `:root` in your browser devtools. The class-based rules in the file keep the look consistent either way.

---

## Fonts in restricted / on-prem environments

The stylesheet loads Inter from Google Fonts. If your servers can't reach external CDNs:

1. Download the Inter `.woff2` files and upload them to Static Application Files.
2. Replace the `@import` line at the top of `modern-apex.css` with:

```css
@font-face {
  font-family: "Inter";
  src: url("#APP_FILES#fonts/Inter-Regular.woff2") format("woff2");
  font-weight: 400;
  font-display: swap;
}
@font-face {
  font-family: "Inter";
  src: url("#APP_FILES#fonts/Inter-SemiBold.woff2") format("woff2");
  font-weight: 600;
  font-display: swap;
}
```

> `#APP_FILES#` is substituted only in CSS loaded through APEX's File URLs. If the substitution doesn't happen in your setup, use a relative path (`fonts/Inter-Regular.woff2`) or the full static file URL.

---

## Dark mode

Dark mode activates when the class `theme-dark` is on `<body>`.

**Toggle button (Dynamic Action → Execute JavaScript Code):**

```javascript
document.body.classList.toggle('theme-dark');
apex.storage.setCookie(
  'APEX_DARK',
  document.body.classList.contains('theme-dark') ? 'Y' : 'N'
);
```

**Restore on page load (Page 0 or User Interface → JavaScript → Execute when Page Loads):**

```javascript
if (apex.storage.getCookie('APEX_DARK') === 'Y') {
  document.body.classList.add('theme-dark');
}
```

To persist the setting server-side instead, store it as a user preference (`APEX_UTIL.SET_PREFERENCE`) and add the class through a page-level body attribute.

---

## Utility classes

Add these in a region's **Appearance → CSS Classes** attribute:

| Class | Effect |
|-------|--------|
| `m-stat` | Large KPI number styling for metric tiles |
| `m-glass` | Frosted, semi-transparent panel |
| `m-accent-top` | Brand-colored top border |
| `m-flat` | Removes the region shadow |

Example: a dashboard KPI tile → Static Content region with CSS Classes `m-stat m-accent-top`.

---

## What gets styled

- **Base:** typography, links, page background
- **Header & navigation:** frosted header, side tree nav with pill-style active item
- **Regions & cards:** rounded surfaces, subtle borders, hover lift on cards
- **Buttons:** default, `--hot`, `--danger`, `--success`, `--noUI`, focus ring
- **Form fields:** text, textarea, select, date picker, combobox, popup LOV, error text
- **Reports:** Classic Report, Interactive Report, Interactive Grid (hover rows, toolbars, rounded containers; column headers keep the native Vita style)
- **Alerts & badges:** colored left edge by status, pill badges
- **Dialogs:** rounded modal, blurred overlay
- **Login page:** soft gradient background, elevated login card
- **Accessibility:** visible focus rings; transitions are disabled under `prefers-reduced-motion`

---

## Rolling out across multiple apps

1. Build and tune the skin in a dedicated **master theme app**.
2. In each consuming app, subscribe its Universal Theme to the master app's theme (**Shared Components → Themes → Create → From the Repository / Subscribe**).
3. Publish changes from the master app to push style updates to all subscribers.

---

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| Styles don't apply | Confirm the File URL is on the *current* theme style; clear browser cache; check the Network tab for a 404 |
| Some colors unchanged | That component uses a UT variable with a different name in your version. Inspect it and add the override to `:root` |
| Font looks like system default | The Google Fonts CDN is blocked. Self-host Inter (see above) |
| Theme Roller overrides my colors | Theme Roller saves its own variables. Either reset them in Theme Roller or move your token values into Theme Roller's Custom CSS |
| `#MIN#` file not found | Upload a `modern-apex.min.css`, or remove `#MIN#` from the URL |
