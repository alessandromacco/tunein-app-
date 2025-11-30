# Technical Notes - Tune In Project

**Last Updated:** 30 November 2025

---

## 🔗 URLs & Endpoints

### Production
- **Web Application:** https://alessandromacco.github.io/tunein-app-/
- **CDN Audio Files:** https://tunein.b-cdn.net/
- **Current Audio:** https://tunein.b-cdn.net/Track.01.mp3

### Development
- **GitHub Repository:** https://github.com/alessandromacco/tunein-app-
- **BunnyCDN Dashboard:** https://dash.bunny.net/
- **Storage Manager:** https://dash.bunny.net/storage/1277768/file-manager

---

## 🔑 BunnyCDN Configuration

### Storage Zone
- **Name:** tunein
- **ID:** 1277768
- **Region:** Default
- **Storage Path:** `/`

### Pull Zone
- **Name:** tunein
- **Hostname:** tunein.b-cdn.net
- **Origin:** Connected to Storage Zone "tunein"
- **SSL:** Enabled (default)

### CORS Configuration
```
Auto CORS Headers: Enabled
Extension List: eot, ttf, woff, woff2, css, js, jpg, jpeg, png, mp3
```

**Result:** Automatic addition of CORS headers for listed file types

---

## 🧮 Audio Processing Formulas

### Frequency Conversion
```javascript
playbackRate = targetFrequency / STANDARD_FREQ

// Where STANDARD_FREQ = 440 Hz (international standard)
```

### Examples
```
Target: 396 Hz → playbackRate = 396/440 = 0.9
Target: 432 Hz → playbackRate = 432/440 = 0.982
Target: 528 Hz → playbackRate = 528/440 = 1.2
Target: 963 Hz → playbackRate = 963/440 = 2.189
```

### Effect on Audio
- **playbackRate < 1:** Audio slower, pitch lower
- **playbackRate = 1:** Original speed and pitch (440 Hz)
- **playbackRate > 1:** Audio faster, pitch higher

---

## 📂 File Structure

```
tunein-app-/
├── index.html                 (main app - 300+ lines)
├── /docs/
│   ├── TECH_STACK.md         (technology documentation)
│   ├── STATUS.md             (project status & progress)
│   ├── NOTES.md              (this file - technical notes)
│   ├── VISION.md             (project vision - user created)
│   └── ROADMAP.md            (development roadmap - user created)
└── README.md                  (project overview)
```

---

## 💻 Code Architecture

### React Component Structure
```
TuneIn (main component)
├── State Management (useState hooks)
│   ├── lang (language)
│   ├── selectedFreq (preset frequency)
│   ├── customFreq (custom frequency)
│   ├── isPlaying (playback state)
│   ├── currentTime (audio position)
│   ├── duration (audio length)
│   ├── volume (0-1)
│   ├── isCustomMode (preset/custom toggle)
│   └── showInfo (info panel visibility)
│
├── Refs (useRef hooks)
│   ├── audioContextRef (Web Audio API context)
│   ├── sourceNodeRef (audio source node)
│   ├── gainNodeRef (volume control node)
│   └── audioElementRef (HTML5 audio element)
│
└── Effects (useEffect hooks)
    ├── Audio setup & cleanup
    ├── Volume updates
    └── Frequency conversion
```

### Key Code Locations

**Audio Initialization (Lines 87-115):**
```javascript
const audio = new Audio(AUDIO_URL);
audio.crossOrigin = "anonymous";  // CRITICAL: Enables CORS for Web Audio API
audioElementRef.current = audio;

// Web Audio API setup
const AudioContext = window.AudioContext || window.webkitAudioContext;
audioContextRef.current = new AudioContext();
const source = audioContextRef.current.createMediaElementSource(audio);
const gainNode = audioContextRef.current.createGain();
source.connect(gainNode);
gainNode.connect(audioContextRef.current.destination);
```

**Frequency Conversion (Lines 122-128):**
```javascript
const targetFreq = isCustomMode ? customFreq : selectedFreq;
const ratio = targetFreq / STANDARD_FREQ;
audioElementRef.current.playbackRate = ratio;
```

**Play/Pause Handler (Lines 130-145):**
```javascript
const handlePlayPause = async () => {
  if (audioContextRef.current.state === 'suspended') {
    await audioContextRef.current.resume();
  }
  if (isPlaying) {
    audioElementRef.current.pause();
  } else {
    await audioElementRef.current.play();
  }
  setIsPlaying(!isPlaying);
};
```

---

## 🎨 UI Color Palette

### Frequency Colors (Chakra-based)
```
396 Hz (Muladhara):    #ef4444 (red)
417 Hz (Svadhisthana): #f59e0b (orange)
432 Hz (Universal):    #10b981 (green)
440 Hz (Standard):     #3b82f6 (blue)
528 Hz (Manipura):     #8b5cf6 (purple)
639 Hz (Anahata):      #ec4899 (pink)
741 Hz (Vishuddha):    #f97316 (orange-red)
852 Hz (Ajna):         #06b6d4 (cyan)
963 Hz (Sahasrara):    #a855f7 (violet)
```

### Background Gradient
```css
background: linear-gradient(to bottom right, 
  indigo-950, purple-950, pink-950)
```

---

## 🔧 Critical Configuration Details

### CORS Issue Resolution
**Problem:** Web Audio API couldn't access audio from BunnyCDN due to CORS restrictions

**Solution Applied:**
1. Enabled CORS headers in BunnyCDN Pull Zone
2. Added `mp3` to extension list
3. Set `audio.crossOrigin = "anonymous"` in code (line 88)

**Result:** Audio loads and frequency analysis works correctly

### GitHub Pages Deployment
- **Trigger:** Automatic on push to `main` branch
- **Build Time:** 1-2 minutes
- **Cache:** May need hard refresh (Ctrl+Shift+R) to see changes

---

## 📊 Performance Notes

### Load Time Breakdown
- HTML: ~15KB (< 100ms)
- React CDN: ~130KB (cached)
- Tailwind CDN: ~50KB (cached)
- Audio file: ~200MB (streaming)

### Optimization Opportunities
- Audio file compression (current: MP3)
- Consider WebM/Opus for better compression
- Implement progressive loading
- Add service worker for offline capability

---

## 🌐 Browser Compatibility Notes

### Web Audio API Support
- Chrome: ✅ Full support
- Safari: ✅ Full support (webkit prefix)
- Firefox: ✅ Full support
- Edge: ✅ Full support

### Known Browser Issues
- **Safari:** May require user interaction before AudioContext starts
- **iOS Safari:** Audio autoplay restrictions apply
- **Firefox:** Different audio codec preferences

### Fallback Strategy
```javascript
const AudioContext = window.AudioContext || window.webkitAudioContext;
```

---

## 🔍 Debugging Tips

### Check CORS Issues
Open browser console and look for:
```
Access to audio at 'https://tunein.b-cdn.net/Track.01.mp3' 
from origin 'https://alessandromacco.github.io' 
has been blocked by CORS policy
```

### Check Audio Loading
```javascript
audio.addEventListener('loadedmetadata', () => {
  console.log('Duration:', audio.duration);
});

audio.addEventListener('error', (e) => {
  console.error('Audio error:', e);
});
```

### Check Frequency Conversion
```javascript
console.log('Target Freq:', targetFreq);
console.log('Playback Rate:', audioElement.playbackRate);
console.log('Expected Rate:', targetFreq / 440);
```

---

## 🔐 Security Considerations

- No user data stored
- No cookies used
- No authentication required
- All traffic over HTTPS
- No third-party analytics (currently)
- CORS properly restricted

---

## 📝 Development Workflow

### Making Changes
1. Edit `index.html` locally or on GitHub
2. Commit to `main` branch
3. Wait 1-2 minutes for GitHub Pages deploy
4. Hard refresh browser (Ctrl+Shift+R)
5. Test changes

### Adding Audio Files
1. Go to BunnyCDN Dashboard
2. Navigate to Storage Zone "tunein"
3. Upload file via web interface
4. File automatically available at: `https://tunein.b-cdn.net/filename.mp3`
5. Update `AUDIO_URL` constant in code

---

## 💡 Future Technical Considerations

### Scalability
- Multiple audio files: Consider playlist array
- User uploads: Need backend or Bunny Storage API
- User accounts: Requires authentication system

### Performance
- Consider lazy loading for multiple tracks
- Implement audio preloading strategy
- Add loading states and skeleton UI

### Monitoring
- Add error tracking (e.g., Sentry)
- Add analytics (e.g., Google Analytics, Plausible)
- Monitor CDN usage and costs

---

## 📚 Useful Resources

### Documentation
- [Web Audio API MDN](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)
- [BunnyCDN Docs](https://docs.bunny.net/)
- [GitHub Pages](https://docs.github.com/en/pages)
- [React Hooks](https://react.dev/reference/react)

### Audio Frequency References
- [Solfeggio Frequencies](https://en.wikipedia.org/wiki/Solfeggio_frequencies)
- [A440 Standard](https://en.wikipedia.org/wiki/A440_(pitch_standard))
- [Chakra System](https://en.wikipedia.org/wiki/Chakra)

---

## 🆘 Common Issues & Solutions

### Issue: Audio not playing
**Check:**
- Browser console for errors
- Network tab for failed requests
- CORS configuration in BunnyCDN
- Audio file exists at CDN URL

### Issue: Frequency not changing
**Check:**
- `playbackRate` property of audio element
- Math calculation (targetHz / 440)
- Audio element reference is valid

### Issue: GitHub Pages not updating
**Solutions:**
- Wait 2-3 minutes after commit
- Check GitHub Actions tab for build status
- Hard refresh browser (Ctrl+Shift+R)
- Clear browser cache

---

## 📞 Support Contacts

- **BunnyCDN Support:** support@bunny.net
- **GitHub Support:** Via repository issues
- **Project Maintainer:** Alessandro Macco (GitHub: @alessandromacco)
