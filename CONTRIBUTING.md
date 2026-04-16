# Contributing

Thanks for taking the time to improve Claude Android QA Plugin.

## Good First Contributions

- Fix unclear README examples or troubleshooting notes.
- Improve Windows, macOS, or Linux compatibility guidance.
- Add small helper-script tests or sample XML fixtures.
- Report emulator, `adb`, `uiautomator`, or logcat edge cases with enough detail to reproduce them.

## Before Opening an Issue

Please include:

- Claude Code version, if relevant
- Operating system
- Android emulator or device details
- Package name, if safe to share
- Command or prompt you used
- Error output, screenshot path, UI summary, or log snippet

Do not include API keys, credentials, private app source code, customer data, or sensitive logs.

## Before Opening a Pull Request

Keep changes focused. A small PR that fixes one thing is easier to review than a broad rewrite.

Run the local checks when possible:

```bash
python3 -m json.tool .claude-plugin/plugin.json
python3 -B -m py_compile scripts/android-qa.py skills/android-emulator-qa/scripts/ui_pick.py skills/android-emulator-qa/scripts/ui_tree_summarize.py
python3 scripts/android-qa.py --help
```

## Code Style

- Keep helper scripts dependency-free and Python standard-library only.
- Prefer explicit `adb` argument lists over shell strings.
- Keep documentation copy practical and concrete.
- Avoid committing generated output, screenshots, logs, or emulator artifacts.

## Security

If you find a security issue, avoid posting sensitive details publicly. Open a minimal issue asking for a private contact path, or use GitHub private vulnerability reporting if it is enabled for the repository.
