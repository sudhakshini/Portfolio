# DubStudio.AI 🎬

> AI-powered multilingual video dubbing and translation platform — available on Google Play.

![Python](https://img.shields.io/badge/Python-3.11-blue?style=flat-square&logo=python)
![Flutter](https://img.shields.io/badge/Flutter-3.x-blue?style=flat-square&logo=flutter)
![FastAPI](https://img.shields.io/badge/FastAPI-Backend-green?style=flat-square&logo=fastapi)
![Whisper](https://img.shields.io/badge/OpenAI-Whisper-black?style=flat-square)
![Google Play](https://img.shields.io/badge/Google_Play-Published-green?style=flat-square&logo=googleplay)

---

## What it does

DubStudio.AI takes any video (YouTube URL or local upload), automatically transcribes the speech, translates it, and re-dubs the video in English with gender-matched AI voices — all in one pipeline.

**Key capabilities:**
- Translate and dub Hindi, Chinese (Mandarin), Telugu, Korean, Spanish, Japanese, French → English
- Gender-aware voice assignment using pitch analysis (nearest-neighbor detection)
- AI story video generation with Ken Burns zoom/pan animation and Pixar-style scene images
- Floating watermark + "English Dubbed" badge + Like/Follow overlay
- Auto-crop blank/colored borders from source videos
- Profanity filter on all translated output

---

## Pipeline Architecture

```
YouTube URL / Local Video
         │
         ▼
   yt-dlp download
   (best quality mp4)
         │
    ┌────┴────┐
    │  Video  │  Audio (WAV 16kHz)
    └────┬────┘         │
         │              ▼
         │    Whisper Medium Model
         │    ├── Hindi/Telugu/etc → Transcribe → Google Translate
         │    └── Chinese → Direct translate (task="translate")
         │              │
         │    Profanity Filter
         │              │
         │    Gender Detection
         │    ├── Per-window pitch analysis (librosa pyin)
         │    ├── Nearest-neighbor fill for silent windows
         │    └── Overall gender fallback
         │              │
         │    Edge-TTS Voice Synthesis
         │    ├── Male → en-US-GuyNeural
         │    └── Female → en-US-JennyNeural
         │              │
         │    fit_audio_to_duration()
         │    (MAX_SPEED=1.2x, MIN_SPEED=0.65x)
         │              │
    ┌────┴────────┐     │
    │  merger.py  │◄────┘
    │  -c:v copy  │  (no re-encode = quality preserved)
    └────┬────────┘
         │
    add_watermark()
    ├── Floating DubStudio.AI (moves corners every 5s)
    ├── [ English Dubbed ] gold badge top-center
    └── >> Like and Follow @DubStudio.AI << orange bottom
         │
         ▼
    Final MP4 output
```

---

## Story Video Generator

```
User Story Text
      │
      ▼
split_story_into_scenes()  (3 scenes × 3 images)
      │
      ▼
Pollinations AI → Pixar/Ghibli-style scene images
      │
      ▼
animate_image_opencv()  (Ken Burns zoom/pan effects)
      │
      ▼
Edge-TTS narration
      │
      ▼
ffmpeg scene stitching + background music
      │
      ▼
Animated story video
```

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Backend | FastAPI, Python 3.11 |
| Transcription | faster-whisper (medium model) |
| Translation | Google Translate (Hindi), Helsinki-NLP opus-mt-zh-en (Chinese) |
| TTS | Edge-TTS (Azure Neural voices) |
| Audio Processing | librosa, pydub, ffmpeg |
| Video Processing | OpenCV, ffmpeg |
| Frontend | Flutter 3.x (Android) |
| Distribution | Google Play Store |

---

## Key Engineering Challenges Solved

**1. Segment alignment bug** — Google Translate was stripping `|||` separators, causing translated segments to misalign. Fixed with numbered markers `[1][2][3]` that survive translation.

**2. Gender misclassification** — Simple pitch threshold (160Hz) was too low for Hindi male voices. Replaced with: per-window pitch → nearest-neighbor fill → overall video gender fallback.

**3. Video quality degradation** — Original pipeline re-encoded video 3× (strip audio + merge + watermark). Fixed with `-c:v copy` in merger, reducing re-encodes to 1.

**4. Pollinations API rate limit** — `flux-pro` model started returning HTTP 402. Switched to bare prompt URL (no model/seed params) + ffmpeg resize for consistent output dimensions.

---

## Getting Started

```bash
# Backend
git clone https://github.com/sudhakshini/DubStudio-AI.git
cd DubStudio-AI/backend
pip install -r requirements.txt
python main.py

# Frontend (Flutter)
cd ../frontend
flutter pub get
flutter run -d android
```

---

## Built by

**Sudhakshini Puntikura** — Senior Data & AI/ML Engineer  
[LinkedIn](https://www.linkedin.com/in/sudhaakshini-puntikura-927s55215/) · [GitHub](https://github.com/sudhakshini) · sudhakshini361@gmail.com
