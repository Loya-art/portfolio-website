# Click-to-Copy Contact Links

Reusable pattern for portfolio contact sections. Makes 微信/QQ/phone handles tappable on mobile without needing to long-press-and-select.

## HTML

```html
<div class="contact-links">
  <a href="javascript:void(0)" onclick="copyText('ZZX274155583')" class="copy-link">
    <span class="copy-icon">📋</span> 💬 微信: ZZX274155583
  </a>
  <a href="javascript:void(0)" onclick="copyText('<qq>')" class="copy-link">
    <span class="copy-icon">📋</span> 💬 QQ: <qq>
  </a>
</div>

<div id="toast"></div>  <!-- hidden toast element -->
```

## CSS

```css
.copy-link { cursor: pointer; }
.copy-icon { opacity: 0.4; font-size: 0.75rem; transition: opacity 0.3s; }
.copy-link:hover .copy-icon { opacity: 1; }

#toast {
  position: fixed;
  bottom: 90px;
  left: 50%;
  transform: translateX(-50%) translateY(20px);
  background: var(--text);
  color: var(--surface);
  padding: 10px 24px;
  border-radius: 100px;
  font-size: 0.85rem;
  font-weight: 600;
  opacity: 0;
  pointer-events: none;
  transition: all 0.35s cubic-bezier(0.16, 1, 0.3, 1);
  z-index: 999;
  white-space: nowrap;
}
#toast.show {
  opacity: 1;
  transform: translateX(-50%) translateY(0);
}
```

## JavaScript

```js
function copyText(text) {
  navigator.clipboard.writeText(text).then(() => {
    const toast = document.getElementById('toast');
    toast.textContent = `✅ 已复制：${text}`;
    toast.classList.add('show');
    setTimeout(() => toast.classList.remove('show'), 2000);
  }).catch(() => {
    // fallback for older browsers
    const ta = document.createElement('textarea');
    ta.value = text;
    document.body.appendChild(ta);
    ta.select();
    document.execCommand('copy');
    ta.remove();
    const toast = document.getElementById('toast');
    toast.textContent = `✅ 已复制：${text}`;
    toast.classList.add('show');
    setTimeout(() => toast.classList.remove('show'), 2000);
  });
}
```

## Key Points

- 📋 icon dims to 0.4 opacity by default, full on hover — signals "tapable" without being visually noisy
- Toast auto-hides after 2 seconds
- `navigator.clipboard.writeText()` is the primary path; `document.execCommand('copy')` is the fallback for older browsers
- Toast is positioned above the theme-toggle button (bottom: 90px) so they don't overlap
