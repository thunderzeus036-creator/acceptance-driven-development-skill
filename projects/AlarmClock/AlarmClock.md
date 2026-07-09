---
tags:
  - project
  - desktop-app
  - C++
  - Qt
  - utilities
date: 2026-07
status: complete
---

# AlarmClock

## 📌 One-Line Overview

A Windows desktop alarm clock tool with timed/countdown alarms, always-on-top floating window, adjustable transparency, click-through lock mode, daily repeat, snooze, and full-screen flash notifications.

---

## 🧱 Tech Stack

| Layer | Technology | Version | Purpose |
|-------|-----------|---------|---------|
| Language | C++ | C++17 | Core logic |
| Framework | Qt | 5.15.2 (Widgets) | GUI, timers, system tray |
| Build | qmake | — | Project configuration |
| Storage | JSON | — | Lightweight persistence (`alarms.json`) |
| Compiler | MinGW 64-bit | 8.1.0 | Windows build |

---

## 🏗 Architecture

- **Pattern**: MVC variant — `AlarmModel` is the data layer, `MainWindow` handles UI + timer orchestration
- **Rationale**: Model holds `QVector<AlarmItem>` with JSON serialization; MainWindow polls every second via `QTimer` for trigger detection; UI operates on model through signal/slot connections

---

## 🧩 Core Modules

### AlarmModel
- **Path**: `alarmmodel.h/cpp`
- **Responsibility**: Alarm data storage, countdown creation, timed alarm creation, JSON persistence
- **Key interfaces**: `addCountdown()`, `addTimed()`, `addAlarm()`, `removeAt()`, `toggleActive()`, `saveToFile()`, `loadFromFile()`

### MainWindow
- **Path**: `mainwindow.h/cpp`
- **Responsibility**: Frameless top-level window, tab switching (countdown/timed), alarm list display, lock toggle, opacity slider, card mode, full-screen flash, system tray notifications
- **Key interfaces**: `checkAlarms()` (1s poll), `flashScreen()`, `snooze()`, `toggleCardMode()`

### AlarmItem
- **Path**: In `alarmmodel.h`
- **Responsibility**: Single alarm data: type, trigger time, active state, repeat flag, custom label
- **Key fields**: `type` (Countdown/Timed), `triggerTime`, `active`, `repeatDaily`, `customLabel`

---

## 🛡 Edge Cases & Defensive Design

1. **Lock mode unlock** — `WS_EX_TRANSPARENT` makes the lock button itself unclickable. Solution: Ctrl+L keyboard shortcut always available to toggle lock.
2. **Frameless window dragging** — `FramelessWindowHint` has no title bar. Solution: custom `mousePressEvent`/`mouseMoveEvent` for drag support.
3. **Daily repeat after close** — A repeating alarm that triggers while the app is closed should reset when the app reopens. Solution: `fromJson()` sets `active=false` for past non-repeating alarms, but preserves `repeatDaily` items.
4. **Countdown display refresh** — Without a timer, countdown text would freeze. Solution: added `updateList()` call inside the 1-second `checkAlarms()` poll.

---

## 🧠 Reusable Patterns

1. **Frameless draggable window** — Mouse press/move events enable drag on `FramelessWindowHint` windows. Applicable to any desktop overlay tool.
2. **JSON persistence with degrade** — Save on destroy, load on startup. Old format gracefully handled by default values in `fromJson()`.
3. **Tab switching without QTabWidget** — Manual button styling + `QStackedWidget` for a custom look. Lighter than QTabWidget.

---

## ⚠️ Technical Debt

| Issue | Location | Severity | Explanation |
|-------|----------|----------|-------------|
| No unit tests | — | 🔴 | All verification is manual (GUI testing) |
| Windows-only | `mainwindow.cpp` | 🟡 | `WS_EX_TRANSPARENT` and frameless window require Windows API |
| Hardcoded sound | `mainwindow.cpp:playAlert()` | 🟢 | Uses `QApplication::beep()` — no custom sound file |

---

## 📝 Overall Assessment

- **Code quality**: ⭐⭐⭐ — Clean MVC separation, consistent naming. Lacks test coverage.
- **Architecture**: ⭐⭐⭐⭐ — Model/View split is clean, timer polling is simple and effective for this scale.
- **Maintainability**: ⭐⭐⭐⭐ — Small codebase (~400 lines), single-purpose, easy to understand.
- **Test coverage**: ⭐ — Zero automated tests. All verification is manual.
- **One-line summary**: A focused, well-structured desktop utility with careful attention to edge cases (lock mode, persistence, frameless drag).
