# Fit & Fine 💪

> Flutter fitness app for Android with real exercise videos, smart timers, and favorites — built for real workouts.

![Flutter](https://img.shields.io/badge/Flutter-3.44-blue?style=flat-square&logo=flutter)
![Dart](https://img.shields.io/badge/Dart-3.12-blue?style=flat-square&logo=dart)
![Android](https://img.shields.io/badge/Android-API_24+-green?style=flat-square&logo=android)
![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)

---

## What it does

Fit & Fine is a production-ready Android fitness app featuring 50+ exercises across 8 body-part categories, with real exercise video demonstrations, circular countdown timers, favorites management, and custom workout timers.

**Key features:**
- 50+ exercises across Arms, Belly Fat/Abs, Glutes, Thighs, Full Body, and more
- Real exercise video playback (lazy-loaded via VisibilityDetector to avoid concurrent decoder limits)
- Circular countdown timer per exercise with beep chime + haptic vibration on completion
- Custom +/- minute timer on the dashboard for free-form workouts
- Favorites system (heart icon toggle, persisted via SharedPreferences)
- Beginner / Intermediate level toggle
- Local-only user profile (name + email, no backend required)
- Gender selection screen with photo cards

---

## App Architecture

```
lib/
├── main.dart                    # Entry point — routes Login or Gender based on onboarding
├── data/
│   ├── exercise_data.dart       # 56 exercises, 8 body parts, video asset mappings
│   ├── user_prefs.dart          # SharedPreferences: name, email, gender, onboarding flag
│   └── favorites_manager.dart   # FavoritesNotifier (ChangeNotifier) + SharedPreferences
├── screens/
│   ├── login_screen.dart        # Name + email form, local-only
│   ├── gender_screen.dart       # Photo card selection (male/female)
│   ├── dashboard_screen.dart    # Level toggle, quick timer, body-part sections
│   └── exercise_detail_screen.dart  # Hero video, timers, steps, tips
├── widgets/
│   ├── exercise_media.dart      # Video player (lazy) or AnimatedFigure fallback
│   ├── exercise_timer.dart      # Circular countdown, configurable size, beep on complete
│   ├── custom_timer_card.dart   # +/- minute picker wrapping ExerciseTimer
│   └── animated_figure.dart    # 28 AnimType stick-figure painters
└── theme/
    └── app_theme.dart           # AppColors (lime #C6FF00), AppText, AppCard, PressableScale
```

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | Flutter 3.44, Dart 3.12 |
| Video | video_player, visibility_detector (lazy loading) |
| Audio | audioplayers (beep chime on timer completion) |
| Haptics | vibration package |
| Storage | shared_preferences (local-only, no backend) |
| Platform | Android (tested on Samsung SM-N970U, Snapdragon 855) |

---

## Key Engineering Decisions

**Lazy video loading** — Android's hardware decoder is limited to ~4-8 concurrent streams. With 6+ body-part sections each showing 6 exercise thumbnails, initializing all VideoPlayerControllers simultaneously caused silent failures. Solution: `VisibilityDetector` initializes video only when ≥40% visible, disposes when scrolled away.

**Video format standardization** — Source GIFs converted to mp4 via ffmpeg with fractional frame rates (10/3, 5/2, 2/1) that Android's ExoPlayer rejected. Re-encoded all to uniform 25fps, H.264 baseline profile, yuv420p.

**Favorites as singleton** — `FavoritesNotifier` is a global `ChangeNotifier` singleton, so tapping a heart in the exercise detail screen instantly updates the dashboard favorites row without navigation or rebuild.

**Beep generation** — Custom two-note chime (C6 → E6 with harmonics) generated programmatically in Python and bundled as a WAV asset — gives a pleasant "ding-dong" instead of a harsh system beep.

---

## Screenshots

| Login | Gender Selection | Dashboard | Exercise Detail |
|-------|-----------------|-----------|----------------|
| *(coming soon)* | *(coming soon)* | *(coming soon)* | *(coming soon)* |

---

## Getting Started

```bash
git clone https://github.com/sudhakshini/Fit-and-Fine.git
cd Fit-and-Fine

flutter pub get
flutter run -d <android_device_id>
```

**Requirements:**
- Flutter 3.44+
- Android device or emulator (API 24+)
- USB debugging enabled

---

## Assets

- `assets/videos/` — 27 exercise mp4 files (25fps H.264 baseline)
- `assets/audio/beep.wav` — custom two-note chime (C6→E6, generated in Python)
- `assets/images/` — gender selection photos

---

## Built by

**Sudhakshini Puntikura** — Senior Data & AI/ML Engineer  
[LinkedIn](https://www.linkedin.com/in/sudhaakshini-puntikura-927s55215/) · [GitHub](https://github.com/sudhakshini) · sudhakshini361@gmail.com
