# Kadena Community Hub — Style & Design Guide

This document defines the visual identity, color scheme, typography, and interactive design patterns for the **Kadena Community Hub** website. It is designed to be shared with developers, designers, and contributors to ensure visual consistency across all community-built pages.

---

## 1. Typography

The website leverages Google Fonts for both its primary reading text and technical/code elements.

### Fonts
*   **Primary (Sans-Serif):** `Inter`, sans-serif
    *   *Import URL:* `https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;700&display=swap`
    *   *Usage:* General body text, headings, buttons, and navigation.
*   **Technical / Code (Monospace):** `JetBrains Mono`, monospace
    *   *Import URL:* `https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;700&display=swap`
    *   *Usage:* Code snippets, smart contract terms (e.g., *Pact*, *Chainweb*), inline emphasis, and developer links.

### CSS Declaration Fallback
```css
font-family: 'Inter', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
font-family: 'JetBrains Mono', ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
```

---

## 2. Color System

The website features a cohesive dark-first design with curated color roles. The main accent colors correspond directly to different sections/pages of the ecosystem.

### Primary Accents

| Name | HEX Code | Hover HEX | Tailwind Class | Primary Role & Page Usage |
| :--- | :--- | :--- | :--- | :--- |
| **Brand Green** | `#63e038` | `#4ecf25` | `text-brand` / `bg-brand` | Hub branding, primary CTAs, miners/wallets/ecosystem navigation. |
| **DAO Orange** | `#f7931a` | `#e0820b` | `text-dao` / `bg-dao` | Governance / DAO page actions and highlights. |
| **Developer Blue** | `#3498db` | `#2980b9` | `text-dev` / `bg-dev` | Technical resources, SDKs, APIs, and dev documentation. |

### Surfaces & Backgrounds

| Name | HEX Code | Tailwind Class | Role & Usage |
| :--- | :--- | :--- | :--- |
| **Surface Light** | `#FAFAFA` | `bg-surface-light` | Page background in Light Mode. |
| **Surface Dark** | `#050505` | `bg-surface-dark` | Page background in Dark Mode (ultra-dark/deep gray). |
| **Surface Card** | `#121212` | `bg-surface-card` | Container, navbar, and card background in Dark Mode. |
| **Light Card** | `#FFFFFF` | `bg-white` | Container, navbar, and card background in Light Mode. |
| **Border Dark** | `#27272a` | `border-surface-border` | Subtle divider lines and card borders in Dark Mode. |
| **Border Light** | `#E5E7EB` | `border-gray-200` | Subtle divider lines and card borders in Light Mode. |

### Typography Colors

| Name | HEX Code | Tailwind Class | Role & Usage |
| :--- | :--- | :--- | :--- |
| **Text Main (Light)** | `#1e1f23` | `text-text-mainLight` | High-contrast main body text in Light Mode. |
| **Text Main (Dark)** | `#f5f5f5` | `text-text-mainDark` | High-contrast main body text in Dark Mode. |
| **Text Muted (Light)**| `#4B5563` | `text-gray-600` | Secondary / descriptive text in Light Mode. |
| **Text Muted (Dark)** | `#9CA3AF` | `text-gray-400` | Secondary / descriptive text in Dark Mode. |

---

## 3. Tailwind CSS Configuration

If you are building new components or pages using Tailwind CSS, you can drop this config extension directly into your `tailwind.config.js` to inherit the exact design tokens:

```javascript
module.exports = {
  darkMode: 'class', // Enables class-based dark mode toggles (html.dark)
  theme: {
    extend: {
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
        mono: ['JetBrains Mono', 'monospace'],
      },
      colors: {
        brand: {
          DEFAULT: '#63e038', /* Green */
          hover: '#4ecf25',
        },
        dao: {
          DEFAULT: '#f7931a', /* Orange */
          hover: '#e0820b',
        },
        dev: {
          DEFAULT: '#3498db', /* Blue */
          hover: '#2980b9',
        },
        surface: {
          light: '#FAFAFA',
          dark: '#050505',
          card: '#121212',
          border: '#27272a'
        },
        text: {
          mainLight: '#1e1f23',
          mainDark: '#f5f5f5',
        }
      }
    }
  }
}
```

---

## 4. Selection & Highlight Accents

The text selection (highlight) color adapts dynamically to match the context of the page:

*   **Standard Pages (Home, Ecosystem, Miners, Wallets):** 
    *   Background: `#63e038` (Green)
    *   Text: `#000000` (Black)
    *   *Tailwind:* `selection:bg-brand selection:text-black`
*   **DAO Page (`dao.html`):**
    *   Background: `#f7931a` (Orange)
    *   Text: `#000000` (Black)
    *   *Tailwind:* `selection:bg-dao selection:text-black`
*   **Developer Page (`developers.html`):**
    *   Background: `#3498db` (Blue)
    *   Text: `#FFFFFF` (White)
    *   *Tailwind:* `selection:bg-dev selection:text-white`

---

## 5. UI Layout & Animation Rules

To maintain high visual quality, follow these layout rules:

### Glassmorphic Navbar
The main navigation uses a semi-transparent glass background with a blur filter to stay readable as users scroll over text:
*   *Background:* `bg-white/95` (Light Mode) or `bg-surface-dark/95` (Dark Mode)
*   *Blur:* `backdrop-blur-md`
*   *Border:* `border-b border-gray-200` (Light Mode) or `dark:border-surface-border` (Dark Mode)

### Hover Transitions
All interactive elements (buttons, links, navigation tabs, cards) must have smooth transitions:
*   **Interactive Cards:** Cards lift slightly and change border color to highlight selection.
    *   *Classes:* `transition-all duration-300 hover:-translate-y-1 hover:border-brand` (or `hover:border-dev` / `hover:border-dao` depending on page focus).
*   **Theme Transition:** The page background and text colors transition smoothly when toggling between light and dark mode:
    ```css
    body {
        transition: background-color 0.3s, color 0.3s;
    }
    ```
