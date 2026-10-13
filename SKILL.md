---
name: portfolio-website
description: Build a personal portfolio/landing page website with self-introduction, skills, artwork gallery, and contact info. Covers style negotiation, image integration, scroll animations, glassmorphism.
category: software-development
triggers:
  - user wants to build a personal homepage
  - user wants a portfolio site
  - user wants to showcase artwork or projects
  - build a personal website
  - make a landing page for myself
---

# Personal Portfolio Website

Build a single-file HTML/CSS/JS personal portfolio site for the user.

## Workflow

### 1. Gather User Info

Ask in Chinese (user is Chinese-speaking). Collect:

- **Name**  
- **Status** (e.g. 准大一, 学生, 设计师)  
- **Major/Field**  
- **Skills/Interests**  
- **One-line tagline**  
- **Contact info** (微信, QQ, GitHub, 邮箱 — ask which to include)  
- **Artwork or project images** to showcase  
- **Style preference** (科技感, 简洁, 毛玻璃, 暗色/亮色)

### 2. Build the Page

Use a **single self-contained HTML file** with embedded CSS and JS.

#### Recommended Section Structure

```
sections:
  - Hero:       name (glitch or 3D extrusion) + tagline + badges
  - About:      bio text + interest cards (glassmorphism)
  - Works:      image gallery grid (if user has artwork)
  - Skills:     tag cloud
  - Contact:    contact links with click-to-copy (see references/click-to-copy.md)
  - Footer:     copyright
```

#### Effects Reference

| Effect | Implementation | Notes |
|--------|---------------|-------|
| Glitch text (colorful) | CSS text-shadow (RGB split) + keyframe shake | Ask first — some users find it "乱七八糟还在蹦" |
| 3D extrusion (monochrome) | 4-layer stacked text-shadow with theme CSS vars | Clean alternative when user rejects colorful glitch |
| Glassmorphism | backdrop-filter: blur() + semi-transparent bg | |
| Scroll reveal | IntersectionObserver, translateY(40px) to 0 | |
| Parallax bg (translateY) | Fixed bg-layer with translateY(scroll * 0.08) | ⚠️ Causes bottom bleed on long pages — prefer no-parallax fixed bg with `inset: 0` |
| Floating particles | Canvas circles + connection lines | |
| Smooth scroll | scrollIntoView({ behavior: smooth }) | |
| Navbar auto-hide | Hide on scroll down, show on scroll up | |
| Gradient text | linear-gradient + background-clip: text | |

#### Background Image

**Safe approach (recommended)** — fixed bg that always covers the full page:

```css
.bg-layer {
  position: fixed;
  inset: 0;
  z-index: -2;
  background: url('./image.jpg') center / cover no-repeat;
}
```

- No parallax transform on scroll (avoids bottom-edge bleed on long pages)
- `background-size: cover` auto-fills whatever the viewport size is
- Overlay with `<div class="bg-overlay">` using linear-gradient tint
- For local files: `file:///E:/path/to/image.jpg`

**Avoid the translateY parallax approach** — `translateY(scroll * 0.08)` pushes the bg down as user scrolls. On pages longer than ~1.5 viewports, the bottom of the page will show empty space. If parallax is absolutely required, use `background-attachment: fixed` or a much larger container (height: 300%+, top: -100%) but warn the user about the tradeoff.

#### Local Image Paths on Windows

```html
<img src="file:///E:/path/to/image.jpg" loading="lazy">
```

### 3. Style Negotiation (Chinese-speaking users)

- Ask: what style do you want? Offer options
- If user wants bright bg with clouds/landscape: light overlay (0.15-0.25 opacity), dark text (#0f172a)
- If user says too dark: reduce overlay opacity
- If user says too simple: add more effects (particles, parallax, reveal)

### 4. Artwork Gallery

- 3-column grid, 1-col on mobile
- Hover: card lifts up, image zooms
- Glassmorphism borders matching site theme

### 5. Document / Certificate Gallery

For document-style content (certificates, PDF screenshots, credentials) where readability matters more than visual grid:

- **Vertical single-column layout** (`flex-direction: column`) with `max-width: 700px; margin: 0 auto`
- **No aspect-ratio cropping** — use `max-width: 100%; height: auto; max-height: 70vh; object-fit: contain` so images show full content
- Add padding around the image (`.cert-img-wrapper { padding: 20px }`) for breathing room
- Bottom label strip (`.cert-label`) with the document title
- Same lightbox click-to-enlarge as artwork (`onclick="openLightbox(this)"`)
- Hover: subtle lift (-4px) and scale (1.02) — less aggressive than artwork cards

### 6. Contact: Click-to-Copy

Chinese social handles (微信, QQ) are hard to select on mobile. Always make them click-to-copy:

```html
<a href="javascript:void(0)" onclick="copyText('ZZX274155583')" class="copy-link">
  <span class="copy-icon">📋</span> 💬 微信: ZZX274155583
</a>
```

The copy function + toast notification is in `references/click-to-copy.md`. Include:
- 📋 copy icon (dimmed by default, full on hover — signals tap-ability)
- Toast notification: bottom-center pill showing "✅ 已复制：xxx", auto-hides after 2s
- Fallback to `document.execCommand('copy')` for browsers without `navigator.clipboard`
- CSS for `.copy-link`, `.copy-icon`, `#toast`, `#toast.show`

### 7. Output

Save to `C:\Users\<user>\index.html` and tell user the path.

## Pitfalls

- **Glitch text is polarizing**: The colorful RGB-shift + shake effect can feel "messy and bouncy" to some users. Offer monochrome 3D extrusion as the alternative (see effects-snippets.md). 
- **Missing section tags**: When inserting sections between existing ones, verify `<section>` tags are present
- **Chinese path encoding**: MSYS /e/... in terminal, native E:\\... in Python
- **Parallax bg causes bottom bleed on long pages**: The old `translateY` approach pushes the bg down as user scrolls; on content-heavy pages the bottom shows blank. Fix: use `position: fixed; inset: 0; background-size: cover` with NO parallax JS.

- Adding new gallery sections mid-page: When inserting a new section (e.g. certificates) between existing ones: (1) add CSS for the new section + grid, (2) add responsive rules in @media queries, (3) add nav link, (4) renumber all subsequent section labels, (5) reuse existing onclick=openLightbox for click-to-enlarge.
- Contact links on mobile are hard to copy: Don't use plain href=# for 微信/QQ — mobile users can't long-press to select. Always implement click-to-copy via navigator.clipboard.writeText() with a toast confirmation (see references/click-to-copy.md).
- file:// canvas restrictions: Browser tool can display but not canvas-extract local images
- **OCR on Windows**: Installing Tesseract via choco may fail (permission). EasyOCR model downloads may be network-blocked. Fallback: ask user to type content manually.
