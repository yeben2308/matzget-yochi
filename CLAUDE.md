# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A self-contained Hebrew RTL web presentation for a school activity at "בית ספר אורן, עפולה" — targeting 9th grade students. The topic is conformity (קונפורמיות) and the Asch experiment.

## Files

| File | Purpose |
|---|---|
| `מצגת-האומץ-להיות-שונה.html` | Main presentation (7 slides, self-contained) |
| `שאלון-תלמידים.html` | Mobile quiz students open on their phones |
| `rhinos_stampeding.mp4` | Background video for slide 4 (rhino herd) |
| `one_rhino_turns.mp4` | Background video for slide 5 (brave rhino) |
| `*.png` | Logo images for slide 1 header |
| `הוראות-firebase.txt` | Hebrew setup guide for Firebase (for the school principal) |

## Architecture

Both HTML files are fully self-contained (no build step, no package manager). They load dependencies from CDNs:
- Google Fonts (Frank Ruhl Libre + Heebo)
- Firebase v10 compat SDK (app + database)
- qrcodejs (presentation only, for QR code generation)

### Presentation (`מצגת-האומץ-להיות-שונה.html`)

7 slides rendered as absolutely-positioned `<div class="slide">` elements inside `#deck`. Only the active slide has `opacity:1`. Navigation via keyboard arrows or nav buttons.

Slide order:
1. `#s1` — Title with school logos
2. `#s2` — Interactive survey (checkbox show-of-hands)
3. `#s-results` — Live quiz results with QR code + bar charts
4. `#s3` — Asch experiment visual
5. `#s4` — Rhino herd video
6. `#s5` — Brave rhino video
7. `#s6` — Summary quote

Keyboard shortcuts: `←/→` navigate, `N` toggles presenter notes panel, `F` toggles fullscreen.

### Quiz (`שאלון-תלמידים.html`)

Single-page app with 4 screens (`#welcome`, `#q-screen`, `#done`, `#already`) toggled via `show(id)`. 3 questions rendered one at a time. On submit, writes `{q1, q2, q3, ts}` to Firebase path `quiz_aoren/responses`.

### Firebase Integration

Both files share the same Firebase Realtime Database (`quiz_aoren` project). The presentation listens via `dbRef.on('value', ...)` for real-time chart updates. The variable is named `firebaseConfig` (lowercase) in both files.

The Firebase config block and `QUIZ_URL` constant are at the top of the `<script>` section in each file — these are the only values that need updating when deploying.

## Deployment

All files are deployed together as a static site to Netlify Drop (drag the whole folder). No server required. The quiz URL must match `QUIZ_URL` in the presentation file after deploying.

## Design Conventions

- **Language**: Hebrew, RTL (`dir="rtl"`, `lang="he"`)
- **Fonts**: Frank Ruhl Libre (headings/display), Heebo (body)
- **Colors**: `--bg #09090b`, `--gold #C8921A`, `--muted #B0AB9E`, `--text #EDE8DC`
- **Slide content text** uses `clamp()` for responsive sizing
- Videos use `autoplay loop muted playsinline` with `object-fit:cover` filling the slide
