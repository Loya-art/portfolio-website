# Portfolio Effects Snippets

Reusable CSS/JS patterns for personal portfolio websites.

## Glitch Text Effect (Colorful — ask first, some users dislike this)

```css
.glitch {
  font-size: clamp(3.5rem, 9vw, 7.5rem);
  font-weight: 900;
  color: #0f172a;
  text-shadow:
    0.04em 0 0 #00c8ff,
    -0.04em -0.02em 0 #a855f7,
    0.02em 0.04em 0 #ec4899;
  animation: glitch-shake 5s infinite linear alternate-reverse;
}

@keyframes glitch-shake {
  0%, 94%, 100% { transform: translate(0); }
  95% { transform: translate(-3px, 1px); }
  96% { transform: translate(3px, -1px); }
  97% { transform: translate(-1px, 2px); }
  98% { transform: translate(1px, -2px); }
}
```

## Monochrome 3D Extrusion (clean alternative — use when user rejects colorful glitch)

Theme-aware stacked text-shadows for a 3D extrusion effect without color or movement:

```css
:root {
  --shadow-3d-1: rgba(0,0,0,0.10);
  --shadow-3d-2: rgba(0,0,0,0.07);
  --shadow-3d-3: rgba(0,0,0,0.05);
  --shadow-3d-4: rgba(0,0,0,0.03);
}
body.dark {
  --shadow-3d-1: rgba(255,255,255,0.08);
  --shadow-3d-2: rgba(255,255,255,0.06);
  --shadow-3d-3: rgba(255,255,255,0.04);
  --shadow-3d-4: rgba(255,255,255,0.02);
}
.glitch {
  font-size: clamp(3.5rem, 9vw, 7.5rem);
  font-weight: 900;
  color: var(--text);
  text-shadow:
    1px 1px 0 var(--shadow-3d-1),
    2px 2px 0 var(--shadow-3d-2),
    3px 3px 0 var(--shadow-3d-3),
    4px 4px 0 var(--shadow-3d-4);
  letter-spacing: -2px;
  line-height: 1;
}
```

No animation needed — the 4-layer depth gives a premium 3D feel on its own.

## Scroll Reveal (IntersectionObserver)

```js
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.classList.add('visible');
    }
  });
}, { threshold: 0.15, rootMargin: '0px 0px -50px 0px' });

document.querySelectorAll('.reveal').forEach(el => observer.observe(el));
```

```css
.reveal {
  opacity: 0;
  transform: translateY(40px);
  transition: opacity 0.8s cubic-bezier(0.16, 1, 0.3, 1),
              transform 0.8s cubic-bezier(0.16, 1, 0.3, 1);
}
.reveal.visible {
  opacity: 1;
  transform: translateY(0);
}
.reveal-delay-1 { transition-delay: 0.1s; }
.reveal-delay-2 { transition-delay: 0.2s; }
.reveal-delay-3 { transition-delay: 0.3s; }
.reveal-delay-4 { transition-delay: 0.4s; }
```

## Floating Particle Network (Canvas)

```js
const canvas = document.getElementById('particles-canvas');
const ctx = canvas.getContext('2d');
const COUNT = 60;
const particles = [];

for (let i = 0; i < COUNT; i++) {
  particles.push({
    x: Math.random() * canvas.width,
    y: Math.random() * canvas.height,
    vx: (Math.random() - 0.5) * 0.3,
    vy: (Math.random() - 0.5) * 0.3 - 0.1,
    r: Math.random() * 2.5 + 1,
    alpha: Math.random() * 0.4 + 0.1
  });
}

function drawParticles() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  for (const p of particles) {
    p.x += p.vx; p.y += p.vy;
    // wrap around edges
    if (p.x < 0) p.x = canvas.width;
    if (p.x > canvas.width) p.x = 0;
    if (p.y < 0) p.y = canvas.height;
    if (p.y > canvas.height) p.y = 0;
    ctx.beginPath();
    ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2);
    ctx.fillStyle = `rgba(0, 200, 255, ${p.alpha})`;
    ctx.fill();
  }
  // connections
  for (let i = 0; i < particles.length; i++) {
    for (let j = i + 1; j < particles.length; j++) {
      const dx = particles[i].x - particles[j].x;
      const dy = particles[i].y - particles[j].y;
      const dist = Math.sqrt(dx*dx + dy*dy);
      if (dist < 120) {
        ctx.beginPath();
        ctx.moveTo(particles[i].x, particles[i].y);
        ctx.lineTo(particles[j].x, particles[j].y);
        ctx.strokeStyle = `rgba(0, 200, 255, ${0.08 * (1 - dist / 120)})`;
        ctx.lineWidth = 0.5;
        ctx.stroke();
      }
    }
  }
  requestAnimationFrame(drawParticles);
}
drawParticles();
```

## Parallax Background — ⚠️ AVOID (causes bottom bleed)

**Do not use `translateY` parallax on the background.** When the page scrolls, `translateY(scroll * 0.08)` pushes the bg down. On pages longer than ~1.5 viewports, the bottom section will show blank space — the user has to scroll back up to see the bg again.

❌ **Don't do this:**
```css
.bg-layer {
  position: fixed;
  top: -10%; left: -10%;
  width: 120%; height: 120%;
  z-index: -2;
  background: url('image.jpg') center / cover no-repeat;
  will-change: transform;
}
```
```js
// This breaks on long pages:
bgLayer.style.transform = `translateY(${window.pageYOffset * 0.08}px)`;
```

✅ **Safe fixed background (recommended):**
```css
.bg-layer {
  position: fixed;
  inset: 0;
  z-index: -2;
  background: url('./pexels-padrinan-19670.jpg') center / cover no-repeat;
}
```
No JS needed. `background-size: cover` + `position: fixed` ensures the image always fills the full viewport at any scroll position.
```

## Gradient Text

```css
.gradient-text {
  background: linear-gradient(135deg, #00c8ff, #a855f7);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}
```

## Glassmorphism Card

```css
.glass-card {
  background: rgba(255, 255, 255, 0.78);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border: 1px solid rgba(255, 255, 255, 0.5);
  border-radius: 16px;
}
```

## Navbar Auto-hide

```js
let lastScroll = 0;
const navbar = document.getElementById('navbar');

window.addEventListener('scroll', () => {
  const current = window.pageYOffset;
  if (current > lastScroll && current > 100) {
    navbar.style.transform = 'translateY(-100%)';
  } else {
    navbar.style.transform = 'translateY(0)';
  }
  lastScroll = current;
});
```
