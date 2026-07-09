# Daily Companion — Task Manager App

A personal productivity Flutter app built as an independent side project.
Combines shopping list, to-do tasks, and birthday reminders into one clean, dark-themed daily companion.

---

## Screenshots

### Home Dashboard
<img src="screenshots/home_dashboard.jpeg" width="220"/> &nbsp; <img src="screenshots/home_quickaccess.jpeg" width="220"/>

> Personalized greeting with live date, summary tiles for all modules, and Quick Access shortcuts.

---

### Shopping List
<img src="screenshots/shopping_list.jpeg" width="220"/>

> Add items by category, control quantity with +/− stepper, filter by All / Pending / Done, and track completion percentage live.

---

### To-Do List
<img src="screenshots/todo_list.jpeg" width="220"/> &nbsp; <img src="screenshots/todo_newtask.jpeg" width="220"/> &nbsp; <img src="screenshots/todo_timepicker.jpeg" width="220"/>

> Create tasks with High / Medium / Low priority, pick date and time for deadlines, attach a voice note via mic, and batch-clear completed tasks.

---

### Birthday Tracker
<img src="screenshots/birthday_add.jpeg" width="220"/> &nbsp; <img src="screenshots/birthday_saved.jpeg" width="220"/>

> Save birthdays with a visual month + day picker. Each entry shows a live countdown in days. Notification bell for reminders.

---

## Features

| Module | What it does |
|---|---|
| Home | Daily greeting, live summary tiles, Quick Access to all sections |
| Shopping | Category tagging, quantity stepper, All/Pending/Done filter, completion % |
| To-Do | Priority levels, date/time picker, voice note, Clear Done action |
| Birthdays | Month/day picker, countdown in days, local notifications |

---

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Flutter 3.x (Dart) |
| State management | setState / Provider |
| Local storage | SharedPreferences |
| Notifications | flutter_local_notifications |
| UI | Material 3, custom dark theme |
| Platform | Android (tested on physical device) |

---

## Project Structure

```
lib/
├── main.dart               # App entry, bottom nav, theme
├── screens/
│   ├── home_screen.dart    # Dashboard with summary tiles
│   ├── shopping_screen.dart
│   ├── todo_screen.dart
│   └── birthday_screen.dart
├── models/
│   ├── shopping_item.dart
│   ├── todo_item.dart
│   └── birthday.dart
└── services/
    ├── storage_service.dart      # SharedPreferences wrapper
    └── notification_service.dart # Local notification scheduling
```

---

## How to Run

```bash
# Clone the repo
git clone https://github.com/yourusername/daily-companion.git
cd daily-companion

# Install dependencies
flutter pub get

# Run on connected Android device or emulator
flutter run
```

---

## About

Built independently outside of work as a personal Flutter project.
No team, no deadline — just learning by shipping.

**Sudha** · Senior Data & AI/ML Engineer · 11+ years experience · Jacksonville, FL
[LinkedIn](https://linkedin.com/in/yourprofile) · [GitHub](https://github.com/yourusername)

---

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                   Flutter App                        │
│                                                      │
│  ┌──────────────────────────────────────────────┐   │
│  │              UI Layer (Screens)               │   │
│  │                                              │   │
│  │  HomeScreen  ShoppingScreen  TodoScreen      │   │
│  │                    BirthdayScreen            │   │
│  │                                              │   │
│  │         Bottom Navigation (4 tabs)           │   │
│  └──────────────┬───────────────────────────────┘   │
│                 │ setState / Provider                 │
│  ┌──────────────▼───────────────────────────────┐   │
│  │             Business Logic Layer              │   │
│  │                                              │   │
│  │  ShoppingItem   TodoItem    Birthday         │   │
│  │  (model)        (model)     (model)          │   │
│  │                                              │   │
│  │  Filter logic   Priority    Countdown        │   │
│  │  Completion %   sorting     calculation      │   │
│  └──────────────┬───────────────────────────────┘   │
│                 │                                     │
│  ┌──────────────▼───────────────────────────────┐   │
│  │              Services Layer                   │   │
│  │                                              │   │
│  │  StorageService          NotificationService │   │
│  │  (SharedPreferences)     (local_notifications│   │
│  │  - save/load all data    - schedule birthday │   │
│  │  - JSON encode/decode      reminders         │   │
│  └──────────────┬───────────────┬───────────────┘   │
│                 │               │                     │
└─────────────────┼───────────────┼─────────────────────┘
                  │               │
     ┌────────────▼──┐   ┌────────▼──────────┐
     │ SharedPrefs   │   │ Android Notification│
     │ (device local │   │ System              │
     │  storage)     │   │                     │
     └───────────────┘   └─────────────────────┘
```

### Data Flow

```
User Action (e.g. Add Task)
        │
        ▼
   Screen Widget
   (todo_screen.dart)
        │
        ▼
   TodoItem Model
   (validate, create object)
        │
        ▼
   StorageService
   (encode to JSON → SharedPreferences)
        │
        ▼
   setState() → UI rebuilds
   (task appears in list immediately)
```

### Key Design Decisions

| Decision | Reason |
|---|---|
| SharedPreferences over SQLite | Simple key-value data, no relational queries needed, faster to implement |
| setState over BLoC/Riverpod | Single-user app, screen-scoped state, no cross-screen state sharing needed |
| Local notifications only | No backend required, works fully offline, no user login needed |
| Single APK, no backend | Zero server cost, instant load, privacy-friendly — all data stays on device |

