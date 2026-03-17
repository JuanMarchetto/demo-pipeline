# Demo Pipeline

Record automated app demos from natural language descriptions. Converts plain English into Maestro flows (mobile) or webreel configs (web), executes them, and outputs screenshots + video + QA report.

## Install

```
/plugin marketplace add JuanMarchetto/agent-skills
/plugin install demo-pipeline@agent-skills
```

Or via [skills.sh](https://skills.sh):
```bash
npx skills add JuanMarchetto/demo-pipeline
```

Or manually:
```bash
git clone https://github.com/JuanMarchetto/demo-pipeline.git
cp -r demo-pipeline ~/.claude/skills/demo-pipeline
```

## How It Works

```
"record demo — show login, navigate to rewards, highlight coupons, end on profile"
                                    |
                          DISCOVER → DETECT → PARSE → EXECUTE → COMPILE
                                    |
                          screenshots/ + videos/demo.mp4 + docs/demo-report.md
```

**Pipeline**: Discover screens via static analysis → Detect project type (Expo/web) → Parse natural language to unified YAML → Execute with Maestro or webreel → Compile outputs.

## Example Output

```
$ claude "record demo — show login, navigate to rewards, highlight coupons, end on profile"

▸ Discovering screens via static analysis...
  Found: Welcome, Login, Home, Rewards, Coupons, Profile (6 screens)

▸ Detecting project type...
  Type: Expo/React Native (Maestro)

▸ Parsing description to unified YAML...
  Generated: 12-step flow across 4 screens

▸ Executing with Maestro...
  Step  1/12: Launch app                    ✓
  Step  2/12: Tap "Sign In"                 ✓
  Step  3/12: Fill email field              ✓
  Step  4/12: Fill password field           ✓
  Step  5/12: Tap "Login"                   ✓
  Step  6/12: Wait for Home screen          ✓
  Step  7/12: Navigate to Rewards tab       ✓
  Step  8/12: Screenshot rewards list       ✓
  Step  9/12: Tap "Coupons" section         ✓
  Step 10/12: Screenshot coupon details     ✓
  Step 11/12: Navigate to Profile tab       ✓
  Step 12/12: Screenshot profile            ✓

▸ Compiling outputs...
  screenshots/01-login.png          (1170x2532, 245KB)
  screenshots/02-rewards-list.png   (1170x2532, 312KB)
  screenshots/03-coupon-detail.png  (1170x2532, 287KB)
  screenshots/04-profile.png        (1170x2532, 198KB)
  videos/demo.mp4                   (1080x1920, 12.3MB, 45s)
  docs/demo-report.md              (QA: 12/12 steps passed)
```

## Mobile vs Web

| | Mobile (Maestro) | Web (webreel) |
|---|---|---|
| **Requires** | Emulator/device + maestro CLI | Dev server + webreel npm |
| **Output** | Screenshots + MP4 | Screenshots + GIF/MP4/WebM |
| **Extras** | testID selectors, gesture support | Cursor animation, keystroke overlays |

## Requirements

- [Maestro CLI](https://maestro.dev/) (for mobile) or [webreel](https://www.npmjs.com/package/webreel) (for web)
- Running app (device/emulator or dev server)

## License

[MIT](LICENSE)
