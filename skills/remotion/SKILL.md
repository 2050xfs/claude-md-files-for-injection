---
name: remotion-video-creator
description: This skill should be used when the user wants to "make a Remotion video", "create a programmatic video", "code-driven video", "animated video with React", "explainer video", "product demo video", "data visualization video", "avatar overlay video", "Google Reviews video", or needs to build and deploy a video using Remotion + React + TypeScript to Vercel.
---

# Remotion Video Creator

## 1. Overview

Remotion is a React-based framework for building videos with code. Claude Code writes the components; you render them to `.mp4`. This is fundamentally different from the AI video (Kling/NanaBanana) pipeline — you get **pixel-perfect control, infinite variations, and fully editable source code**.

Inspired by Sabrina Ramonov's "5 INSANE Claude Code + Video Prompts" — this skill operationalizes her workflow into a repeatable, production-grade pipeline.

**Stack:** Remotion + TypeScript + React + Vite + Vercel (live preview)
**Install skills:** `npx skills add remotion-dev/skills` (loads 30+ official Remotion rules)

---

## 2. Critical Rules

- **MANDATORY**: Always run `npx skills add remotion-dev/skills` inside the project before writing any composition code.
- **MANDATORY**: Always deploy to Vercel at the end — the user gets a live URL they can share. Use the Vercel MCP.
- **MANDATORY**: Never use placeholder copy. Write real draft content.
- **MANDATORY**: All local assets go in `/public`. Reference via `staticFile('filename')`.
- **CONSTRAINT**: Never hardcode `durationInFrames` as seconds. Always compute: `seconds * fps`.
- **CONSTRAINT**: Always add `extrapolateLeft: 'clamp'` and `extrapolateRight: 'clamp'` to `interpolate()` calls unless you explicitly want extrapolation.
- **CONSTRAINT**: Use `useVideoConfig()` to get `fps`, `width`, `height`, `durationInFrames` — never hardcode them inside components.

---

## 3. Project Setup

```bash
# Create blank project
npx create-video --yes --blank --no-tailwind my-video

# Install official Remotion agent skills (30+ rules)
cd my-video
npx skills add remotion-dev/skills

# Add assets
cp ~/Downloads/music.mp3 public/
cp ~/Downloads/data.csv public/

# Launch Claude Code inside the project
claude

# Dev server (live preview in browser)
npm run dev

# Render final video
npx remotion render <CompositionId> out/video.mp4
```

---

## 4. Core Architecture

Every Remotion video is a React component registered as a `<Composition>`.

### Entry point (`src/index.tsx`)
```tsx
import { Composition } from 'remotion';
import { MyVideo } from './MyVideo';

export const RemotionRoot: React.FC = () => (
  <Composition
    id="MyVideo"
    component={MyVideo}
    durationInFrames={30 * 30}   // 30 seconds × 30fps
    fps={30}
    width={1920}
    height={1080}
    defaultProps={{ title: 'Hello' }}
  />
);
```

### Format Reference
| Format | Width | Height | fps |
|--------|-------|--------|-----|
| Landscape (YouTube) | 1920 | 1080 | 30 |
| Portrait (TikTok/Reels) | 1080 | 1920 | 30 |
| Square (Instagram) | 1080 | 1080 | 30 |

---

## 5. Essential APIs

### `useCurrentFrame()` — the heartbeat of every animation
```tsx
const frame = useCurrentFrame(); // 0, 1, 2 ... durationInFrames-1
```

### `useVideoConfig()` — composition metadata
```tsx
const { fps, durationInFrames, width, height } = useVideoConfig();
```

### `interpolate()` — map frames to any value
```tsx
const opacity = interpolate(frame, [0, 30, 120, 150], [0, 1, 1, 0], {
  extrapolateLeft: 'clamp', extrapolateRight: 'clamp'
});
const x = interpolate(frame, [0, 20], [-200, 0], { extrapolateRight: 'clamp' });
```

### `spring()` — physics-based bounce
```tsx
const scale = spring({ frame, fps, from: 0, to: 1,
  config: { damping: 10, stiffness: 100, mass: 0.5 }
});
```

### `<Sequence>` — time-shift and layer components
```tsx
<AbsoluteFill>
  <Sequence from={0} durationInFrames={60}><TitleCard /></Sequence>
  <Sequence from={45} durationInFrames={90}><MainContent /></Sequence>
</AbsoluteFill>
```

### `staticFile()` — reference local assets
```tsx
<Img src={staticFile('logo.png')} />
<Audio src={staticFile('music.mp3')} />
<Video src={staticFile('bg.mp4')} />
```

---

## 6. The 5 Production Prompt Templates

Paste these directly into Claude Code inside your Remotion project.

---

### Prompt 1 — Education Explainer
```
Use the remotion-best-practices skill.
Create a 60-second education explainer about [TOPIC].
- 5 scenes: Hook (0-10s), Concept A (10-25s), Concept B (25-40s), Application (40-52s), Recap + CTA (52-60s)
- spring() for text entry, staggered bullet points
- Dark background (#0f0f23), accent (#6c63ff)
- Add /public/music.mp3 at volume 0.3 with 2-second fade-out
- Render to: out/explainer.mp4, then deploy to Vercel
```

### Prompt 2 — Product Demo + Launch
```
Use the remotion-best-practices skill.
Create a 45-second product launch video for: [PRODUCT_URL]
1. Scrape the URL — extract brand name, hex colors, logo, tagline, 3-5 key features
2. Download product images to /public/product/
3. Build 5 scenes: Brand reveal → Problem → Feature showcase (3-panel grid) → UI demo (screenshot + cursor highlights) → CTA
- Use extracted brand colors throughout
- Render to: out/product-demo.mp4, then deploy to Vercel
```

### Prompt 3 — Google Reviews Testimonial Reel
```
Use the remotion-best-practices skill.
Create a 30-second testimonial reel from: [GOOGLE_MAPS_URL]
1. Scrape business name, overall star rating, top 5 reviews
2. Build a looping reel: Intro (star animation) → 5 review cards (slide in, stars fill, text types in) → Outro
- White cards, Google blue (#4285f4), dark background
- Render to: out/testimonials.mp4, then deploy to Vercel
```

### Prompt 4 — Avatar + Animated Text Overlays
```
Use the remotion-best-practices skill.
I have a talking-head video at /public/speaker.mp4
1. Transcribe speech into timestamped word segments
2. Overlay animated captions synced to timestamps (word-pop spring effect, active word highlighted)
3. Add: speaker name/title badge (lower-left, slides in frame 30), topic tag (upper-right, fades in frame 60)
- Output: 1080×1920 portrait
- Render to: out/speaker-overlay.mp4, then deploy to Vercel
```

### Prompt 5 — Data Viz Dashboard
```
Use the remotion-best-practices skill.
I have a CSV at /public/data.csv
1. Parse and analyze the CSV — identify key metrics, time series, categories
2. Build a 20-second animated dashboard (1920×1080):
   - 0-1s: Title + date range fade in
   - 1-3s: KPI cards spring in one by one, numbers count up from 0
   - 3-5s: Main chart animates (bars grow from bottom / line draws left-to-right / pie segments sweep)
   - 5-7s: Secondary charts slide in
   - 7-8s: Summary insights text + branding
- Dark theme: #1a1a2e background, #6c63ff / #ff6584 / #43aa8b accents
- Load rules/charts.md for chart patterns
- Render to: out/dashboard.mp4, then deploy to Vercel
```

---

## 7. Animation Patterns Cheat Sheet

```tsx
// Typing effect
const chars = Math.floor(interpolate(frame, [0, 60], [0, text.length], { extrapolateRight: 'clamp' }));
const display = text.slice(0, chars);

// Number counter
const value = Math.floor(interpolate(frame, [start, start + 30], [0, finalValue], { extrapolateRight: 'clamp' }));

// Staggered entrance
items.map((item, i) => {
  const enterFrame = i * 15;
  const opacity = interpolate(frame, [enterFrame, enterFrame + 15], [0, 1], {
    extrapolateLeft: 'clamp', extrapolateRight: 'clamp'
  });
  return <div style={{ opacity }}>{item}</div>;
});

// Bar chart grow from bottom
const barHeight = interpolate(frame, [enter, enter + 30], [0, maxH], { extrapolateRight: 'clamp' });
// style: { transform: `scaleY(${barHeight/maxH})`, transformOrigin: 'bottom' }

// Slide from left / bottom
const x = interpolate(frame, [0, 20], [-100, 0], { extrapolateRight: 'clamp' });
const y = interpolate(frame, [0, 20], [100, 0], { extrapolateRight: 'clamp' });
```

---

## 8. Audio Integration

```tsx
<Audio
  src={staticFile('music.mp3')}
  volume={(f) => interpolate(f, [durationInFrames - 30, durationInFrames], [1, 0], {
    extrapolateLeft: 'clamp'
  })}
/>
```

Load `rules/audio.md` for trimming, multi-track, and pitch shifting.

---

## 9. Dynamic Duration from Data

```tsx
export const calculateMetadata = calculateMetadata<Props>({
  calculate: async ({ props }) => ({
    durationInFrames: props.items.length * 60,
    props,
  })
});

<Composition calculateMetadata={calculateMetadata} ... />
```

Load `rules/calculate-metadata.md` for full patterns.

---

## 10. Render & Deploy Cheat Sheet

```bash
# Preview
npx remotion studio

# Render
npx remotion render MyVideo out/video.mp4

# With props
npx remotion render MyVideo out/video.mp4 --props='{"title":"My Title"}'

# Single frame (debug)
npx remotion still MyVideo out/frame.png --frame=30

# High quality
npx remotion render MyVideo out/video.mp4 --codec=h264 --crf=18

# Deploy to Vercel (always do this — gives client a live preview URL)
# Use the Vercel MCP: mcp__83f244a8__deploy_to_vercel
```

---

## 11. Integration with Creative-Cloner Pipeline

Remotion handles what Kling/NanaBanana can't — and vice versa:

| Use Case | Remotion | Kling/NanaBanana |
|----------|----------|-----------------|
| Data dashboards | ✅ | ❌ |
| Text animations / captions | ✅ | ❌ |
| Charts and graphs | ✅ | ❌ |
| Slide-style explainers | ✅ | ❌ |
| Product UI demos | ✅ | ❌ |
| Realistic motion (people) | ❌ | ✅ |
| Style transfer from reference | ❌ | ✅ |
| Image-to-video animation | ❌ | ✅ |

**Hybrid workflow:** Use Remotion for title cards, lower-thirds, data overlays, intros/outros. Use Kling for hero clips. Stitch together with `ffmpeg-static` via the `video-combiner` skill.

---

## 12. Official Rules Modules (`npx skills add remotion-dev/skills`)

| Rule | Load When |
|------|-----------|
| `rules/animations.md` | Any animation — interpolate, spring, sequence |
| `rules/compositions.md` | Setting up Composition, folders, props |
| `rules/assets.md` | Importing images, video, audio, fonts |
| `rules/audio.md` | Volume, trimming, multi-track |
| `rules/audio-visualization.md` | Spectrum bars, waveforms, bass-reactive |
| `rules/subtitles.md` | Captions, word-level timing, TikTok style |
| `rules/charts.md` | Bar, pie, line, stock charts |
| `rules/calculate-metadata.md` | Dynamic duration from data |
| `rules/3d.md` | Three.js / React Three Fiber |
| `rules/ffmpeg.md` | Trim, silence detection, post-processing |
| `rules/fonts.md` | Google Fonts, local fonts |
| `rules/transitions.md` | Slide, fade, wipe between scenes |

---

## 13. Troubleshooting

| Problem | Fix |
|---------|-----|
| Blank white frame | Wrap in `<AbsoluteFill>`, check `<Composition>` registration |
| `useCurrentFrame` undefined | Component must be inside a registered `<Composition>` |
| Audio desync | Use `startFrom` offset; verify frame/fps math |
| Animation doesn't stop | Add `extrapolateRight: 'clamp'` |
| Asset not found | File must be in `/public`, reference via `staticFile()` |
| Duration wrong | `durationInFrames = seconds × fps` — never mix units |
