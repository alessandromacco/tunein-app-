# Tech Stack - Tune In Project

**Last Updated:** 30 November 2025

---

## Frontend

- **React 18** (via CDN unpkg)
- **Babel Standalone** (JSX transpiling in-browser)
- **Tailwind CSS** (via CDN)
- **Web Audio API** (frequency analysis and audio manipulation)

---

## Hosting & CDN

### GitHub Pages
- **URL:** https://alessandromacco.github.io/tunein-app-/
- **Type:** Static hosting
- **Deploy:** Automatic on push to main branch

### BunnyCDN
- **Storage Zone:** tunein (ID: 1277768)
- **Pull Zone:** tunein.b-cdn.net
- **CDN URL:** https://tunein.b-cdn.net/
- **CORS:** Enabled for mp3 extension

---

## Repository

- **Platform:** GitHub
- **Owner:** alessandromacco
- **Repo:** tunein-app-
- **Branch:** main
- **URL:** https://github.com/alessandromacco/tunein-app-/

---

## Development Setup

### No Build Process
- Single HTML file approach
- All dependencies via CDN
- No node_modules
- No bundler required
- Direct deployment to GitHub Pages

### File Structure
```
tunein-app-/
├── index.html (main application file)
├── /docs/ (documentation)
└── README.md
```

---

## Browser APIs Used

- **Web Audio API**
  - `AudioContext` - Audio processing
  - `MediaElementSource` - Audio source from HTML5 element
  - `GainNode` - Volume control
  - `playbackRate` - Frequency conversion

- **HTML5 Audio Element**
  - Audio playback
  - Time tracking
  - Seeking functionality

---

## Dependencies (CDN)

```html
<!-- React 18 -->
<script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>

<!-- React DOM 18 -->
<script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>

<!-- Babel Standalone -->
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

<!-- Tailwind CSS -->
<script src="https://cdn.tailwindcss.com"></script>
```

---

## Localization

- **Supported Languages:** Italian (IT), English (EN)
- **Implementation:** In-app translation object (TRANSLATIONS)
- **Default Language:** Italian

---

## Audio Processing

### Frequency Conversion Formula
```javascript
playbackRate = targetFrequency / STANDARD_FREQ (440 Hz)
```

**Examples:**
- 432 Hz → playbackRate = 0.982 (slower/lower pitch)
- 528 Hz → playbackRate = 1.2 (faster/higher pitch)

### Preset Frequencies (Solfeggio Scale)
- 396 Hz - Grounding
- 417 Hz - Change
- 432 Hz - Universal Harmony
- 440 Hz - Western Standard
- 528 Hz - Love Frequency
- 639 Hz - Connection
- 741 Hz - Expression
- 852 Hz - Intuition
- 963 Hz - Unity

### Custom Frequency Range
- **Min:** 200 Hz
- **Max:** 1000 Hz
- **Step:** 1 Hz

---

## Performance Considerations

- Lightweight (single HTML file ~15KB)
- No server-side processing required
- CDN delivery for optimal global performance
- Lazy loading not needed (small footprint)

---

## Browser Compatibility

**Tested:**
- Chrome (Desktop)

**To Test:**
- Safari (Desktop/Mobile)
- Firefox (Desktop)
- Edge (Desktop)
- Mobile browsers (iOS Safari, Chrome Mobile)

**Required Features:**
- Web Audio API support
- ES6+ JavaScript
- HTML5 Audio element
- CSS Grid & Flexbox
