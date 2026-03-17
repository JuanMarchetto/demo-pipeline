# Demo Pipeline — Action Reference

Complete reference for all supported actions in the unified YAML format, with mappings to Maestro (mobile) and webreel (web).

## Actions

### tap

Tap or click an element.

**By testID/selector:**
```yaml
- tap: "button-login"
```
- Maestro: `- tapOn: { id: "button-login" }`
- webreel: `{ "action": "click", "selector": "#button-login" }`

**By visible text:**
```yaml
- tap: "Sign In"
```
- Maestro: `- tapOn: "Sign In"`
- webreel: `{ "action": "click", "text": "Sign In" }`

---

### type

Type text into an input field.

```yaml
- type: { target: "search-input", text: "Espresso" }
```
- Maestro: `- tapOn: { id: "search-input" }` then `- inputText: "Espresso"`
- webreel: `{ "action": "type", "selector": "#search-input", "text": "Espresso" }`

---

### scroll

Scroll the screen.

```yaml
- scroll: down     # or: up, left, right
```
- Maestro: `- scroll` (default direction based on context)
- webreel: `{ "action": "scroll", "y": 500 }` (positive = down, negative = up)

---

### swipe

Swipe gesture (mobile-specific, approximated on web).

```yaml
- swipe: left      # or: right, up, down
```
- Maestro: `- swipe: { direction: LEFT, duration: 400 }`
- webreel: `{ "action": "drag", "from": { "x": 300, "y": 400 }, "to": { "x": 50, "y": 400 } }`

---

### wait

Fixed pause.

```yaml
- wait: 2s
```
- Maestro: (no-op — Maestro auto-waits; used for pacing in recordings)
- webreel: `{ "action": "pause", "ms": 2000 }`

---

### wait_for

Wait until an element is visible.

```yaml
- wait_for: "dashboard-loaded"
```
- Maestro: `- extendedWaitUntil: { visible: { id: "dashboard-loaded" }, timeout: 10000 }`
- webreel: `{ "action": "wait", "selector": "#dashboard-loaded", "timeout": 10000 }`

---

### screenshot

Capture a screenshot at this point.

```yaml
- screenshot: "03-rewards-list"
```
- Maestro: `- takeScreenshot: "03-rewards-list"`
- webreel: `{ "action": "screenshot", "output": "03-rewards-list.png" }`

Screenshots are saved to `./screenshots/` with the given name.

---

### navigate (web only)

Navigate to a URL.

```yaml
- navigate: "http://localhost:3000/dashboard"
```
- Maestro: N/A (use deep links via `openLink`)
- webreel: `{ "action": "navigate", "url": "http://localhost:3000/dashboard" }`

---

### key (web only)

Press a keyboard key or shortcut.

```yaml
- key: "Enter"
- key: "cmd+s"
```
- Maestro: N/A
- webreel: `{ "action": "key", "key": "Enter" }`

Key combo syntax: `cmd+s`, `ctrl+shift+p`, `Enter`, `Escape`, `ArrowDown`

## YAML Structure Reference

```yaml
target: expo-mobile | web | expo-universal
app_id: com.example.app          # mobile only
base_url: http://localhost:3000  # web only

demo_mode: false                 # true to enable state injection
env:                             # environment variables (optional)
  DEMO_MODE: "true"

steps:
  - name: "<Step Name>"
    state:                       # optional — mock state to inject for this step
      key: value
    native: real                 # optional — real | mock (default: real)
    actions:
      - <action>: <value>
      - screenshot: "<NN-name>"

output:
  screenshots: ./screenshots/
  video: ./videos/demo.mp4
  video_mode: device-only        # device-only | full (default: full)
  report: ./docs/demo-report.md
```

## Step-Level Fields

### state (optional)

Mock state to inject before this step's actions execute. Only used when `demo_mode: true`.

```yaml
state:
  wallet: "0x123...abc"
  balance: 5000
  streak: 7
```

The skill injects this via AsyncStorage/adb before the step runs.

### native (optional)

Controls native module behavior for this step. Default: `real`.

```yaml
native: mock    # inject mock result, skip native module
native: real    # let native flow execute normally
```

### video_mode

Controls video recording method.

- `device-only` — records only device screen via `adb screenrecord` (clean, no terminal logs)
- `full` — records via `maestro record` (includes Maestro UI, default)
