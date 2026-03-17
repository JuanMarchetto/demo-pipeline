# Demo Pipeline

Record automated app demos from natural language descriptions. Converts plain English into Maestro flows (mobile) or webreel configs (web), executes them, and outputs screenshots + video + QA report.

## Install

```
/plugin marketplace add JuanMarchetto/agent-skills
/plugin install demo-pipeline@agent-skills
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

## Requirements

- [Maestro CLI](https://maestro.dev/) (for mobile) or [webreel](https://www.npmjs.com/package/webreel) (for web)
- Running app (device/emulator or dev server)

## License

[MIT](LICENSE)
