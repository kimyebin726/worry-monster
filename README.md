# Catch Worry Monsters

A webcam wellness mini-game. Smile into the camera and the falling "worry monsters" — each carrying a stress keyword (Deadline, Burnout, Comparing…) — crumble into dust.

Built for **Hana Mind Training Center**.

## Screens

| ID | Screen | What happens |
| --- | --- | --- |
| S-01 | Start | Title art; tap **START GAME!** (music begins on this tap) |
| S-02 | Mission | Today's mission, 40 sec / 3× a day, camera privacy notice |
| S-03 | Play | 40-second round: smile to clear monsters. Timer, score, progress (0/15), smile gauge |
| S-04 | Result | Mind Care Score, stars, encouragement, daily streak, smile points |

## Run it

Needs a local web server (the camera and ES modules do not work from `file://`):

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

For camera access outside localhost you must serve over **HTTPS**.

### GitHub Pages

Push this folder to a repo and enable Pages (Settings → Pages → deploy from branch). Pages serves over HTTPS, so the camera works.

## How the smile detection works

- **MediaPipe FaceLandmarker** (`@mediapipe/tasks-vision@0.10.6`), loaded from CDN
- Smile strength from blendshapes: `face = (mouthSmileLeft + mouthSmileRight) / 2 × 1.7`
- Exponential smoothing to remove jitter: `smile += (target − smile) × min(1, dt × 9)`
- A monster loses health while `smile > 0.4`; roughly 2–3 seconds of a wide smile clears one
- Score: `min(100, 42 + caught × 2.6 + secondsSmiling × 1.3)`

All analysis runs **on-device**. No video frames are uploaded or stored.

### Fallbacks

If the camera is denied, unavailable, or takes longer than 6 seconds, the game drops into **demo mode** (a banner appears) and you can press and hold the screen to simulate a smile.

## Tunable parameters

Set on the root component in `index.html` (`data-props`):

| Prop | Default | Range |
| --- | --- | --- |
| `roundTime` | 40 sec | 20–90 |
| `spawnRate` | 1 | 0.6–1.8 |
| `smileSensitivity` | 1 | 0.6–2 |

## Files

```
index.html              the game (single component: markup + logic)
support.js              component runtime
uploads/
  en-start-bg2.png      S-01 artwork
  en-mission-bg.png     S-02 artwork
  en-result-bg2.png     S-04 artwork
  en-result-btns.png    S-04 buttons
  en-badge-nocam.png    demo-mode banner
  en-timer-ring.png     play-screen timer ring
  dust.png              worry monster sprite
  bgm.mp3               background music
```

## Requirements

- iOS Safari 15+ / Android Chrome 100+ / modern desktop browsers
- Camera permission (optional — demo mode otherwise)
- Internet connection for the MediaPipe model on first load

Designed at 430 × 900 (mobile portrait).
