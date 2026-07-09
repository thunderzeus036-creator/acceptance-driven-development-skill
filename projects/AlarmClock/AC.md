# AlarmClock Acceptance Criteria

## 🎯 Project Goal

A Windows desktop alarm clock tool. Supports timed alarms and countdown timers, multi-alarm list management, always-on-top floating window with adjustable transparency and click-through lock mode. Triple notification on trigger: popup + system tray + sound. JSON persistence.

---

## 📋 Acceptance Criteria

### Features — Core Alarm

| ID | Criterion | Status | How to Verify | Expected Result |
|----|-----------|--------|---------------|-----------------|
| AC-1 | Timed alarm: set a specific time (HH:MM), alarm triggers at that time | [x] | Set an alarm 2 minutes in the future | Triggers at the set time |
| AC-2 | Countdown alarm: set N minutes, alarm triggers after countdown | [x] | Set a 1-minute countdown | Triggers after 1 minute |
| AC-3 | Alarm list shows all alarms with type (timed/countdown) and remaining time | [x] | Add 3 alarms (2 timed + 1 countdown) | All 3 shown, countdown shows live remaining time |
| AC-4 | Popup reminder window (non-modal) when alarm triggers, shows name/type | [x] | Wait for alarm to trigger | Popup appears, closes on OK |
| AC-5 | Windows system notification (tray) on trigger | [x] | Wait for alarm to trigger | System tray notification appears |
| AC-6 | Sound alert on trigger (beep) | [x] | Wait for alarm to trigger | Hear beep sound |
| AC-7 | Triggered alarm auto-removed from list | [x] | Alarm triggers → close popup | Removed from list |

### Features — Window Control

| ID | Criterion | Status | How to Verify | Expected Result |
|----|-----------|--------|---------------|-----------------|
| AC-8 | Window always on top of other windows | [x] | Open app → switch to another window | AlarmClock stays visible |
| AC-9 | Opacity adjustable via slider (10%-100%) | [x] | Drag opacity slider | Window transparency changes in real-time |
| AC-10 | Lock mode: click-through (mouse passes to windows behind) | [x] | Click lock → click alarm window → click unlock | Locked: clicks pass through. Unlocked: normal |
| AC-11 | Lock state has clear visual indicator | [x] | Check lock button | Locked/unlocked visually distinct |

### Features — Management

| ID | Criterion | Status | How to Verify | Expected Result |
|----|-----------|--------|---------------|-----------------|
| AC-12 | Manual delete of untriggered alarms from list | [x] | Click delete button | Alarm removed, won't trigger |
| AC-13 | Countdown presets (1/5/10/30/60 min buttons) | [x] | Click "5 min" preset | Auto-fills 5-min countdown and starts |
| AC-14 | Timed alarm defaults to current time + 5 min | [x] | Open timed alarm panel | Time selector shows now+5min |
| AC-15 | Persistence (JSON): alarms survive app restart | [x] | Add 3 alarms → close → reopen | All 3 still in list |
| AC-16 | Expired alarms default to OFF on restart, active alarms remain ON | [x] | Add alarm → close → wait for it to expire → reopen | Expired: OFF. Active: ON |
| AC-17 | Alarms don't ring while app is closed; re-monitor on reopen | [x] | Set 2min alarm → close app → wait 2min → reopen | No ring; alarm in list, still active |
| AC-18 | Card mode: collapse to ~180×80 mini card (time + nearest countdown), expand to full | [x] | Click collapse → observe → click expand | Mini card mode, full on expand |

### Features — Repeat / Label / Snooze / Flash

| ID | Criterion | Status | How to Verify | Expected Result |
|----|-----------|--------|---------------|-----------------|
| AC-19 | Daily repeat: check "repeat daily", triggers reset for tomorrow | [x] | Set 1min daily alarm → trigger → close → check list | Alarm reappears, time = tomorrow |
| AC-20 | Custom label: optional name when adding; shown instead of default | [x] | Add alarm with label "Meeting" → check list | Shows "Meeting" not default text |
| AC-21 | Snooze: trigger popup has "Snooze" button → input minutes → new countdown | [x] | Trigger alarm → click Snooze → enter 3 | New 3-min countdown created |
| AC-22 | Full-screen flash: screen flashes red 3 times on trigger (~0.3s each), auto-stops | [x] | Set 30s alarm → wait | Screen flashes red 3 times, auto-stops |

### Compatibility

| ID | Criterion | Status | How to Verify | Expected Result |
|----|-----------|--------|---------------|-----------------|
| AC-20 | Runs on Windows 10/11 | [x] | Launch app | Window displays, functions work |
| AC-21 | Window ≤ 400×600 by default, compact for corner placement | [x] | Launch app | Compact, fits in corner |

### Performance

| ID | Criterion | Status | How to Verify | Expected Result |
|----|-----------|--------|---------------|-----------------|
| AC-30 | Countdown accurate to ±1 second | [x] | 30s countdown vs stopwatch | < 1s difference |
| AC-31 | Idle CPU < 1% | [x] | Task Manager | CPU < 1% when idle |

### Quality

| ID | Criterion | Status | How to Verify | Expected Result |
|----|-----------|--------|---------------|-----------------|
| AC-40 | Invalid time (past) shows error, no crash | [x] | Try adding a past-time alarm | Error message, no crash |
| AC-41 | Non-numeric countdown input shows error, no crash | [x] | Type letters in countdown → add | Error message, no crash |

---

## 📊 Status Summary

| Category | Total | Passed | Pending | Notes |
|----------|-------|--------|---------|-------|
| Features | 22 | 22 | 0 | All passed |
| Compatibility | 2 | 2 | 0 | All passed |
| Performance | 2 | 2 | 0 | All passed |
| Quality | 2 | 2 | 0 | All passed |

---

## 🔄 Change Log

| Date | AC-ID | Change | Reason |
|------|-------|--------|--------|
| 2026-07-03 | AC-1~41 | Initial creation | Greenfield project from design doc |
| 2026-07-03 | AC-15~17 | Modified | Persistence: close preserves alarms, expired items default OFF |
