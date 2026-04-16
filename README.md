# Claude Android QA Plugin

I wanted a practical Claude Code plugin for Android emulator QA, so this is the one I made. It gives Claude a repeatable way to launch apps, inspect `uiautomator` XML, derive tap coordinates, capture screenshots, and collect logcat evidence without guessing from screenshots.

## Requirements

- Claude Code with plugin support
- Android Debug Bridge: `adb`
- Python 3
- Gradle or a project-specific build command when installing an app from source
- A running Android emulator or connected Android device

## Plugin Contents

- `skills/android-emulator-qa/`: contextual Android emulator QA workflow
- `skills/android-emulator-qa-command/`: `/claude-android-qa-plugin` command entry point
- `scripts/android-qa.py`: wrapper CLI for common adb QA operations
- `skills/android-emulator-qa/scripts/ui_pick.py`: exact text or content-desc coordinate picker
- `skills/android-emulator-qa/scripts/ui_tree_summarize.py`: compact UI tree summarizer
- `assets/android-qa-plugin.png`: original plugin icon

## Usage

Ask Claude to validate an Android flow, reproduce an emulator UI bug, capture Android screenshots, inspect a UI tree, or collect logcat evidence. The contextual skill should activate for those requests.

Use the command for an explicit entry point:

```text
/claude-android-qa-plugin com.example.app
/claude-android-qa-plugin reproduce the settings screen crash
```

## Extended Test Prompts

Use this for a broad end-to-end QA pass:

```text
Use the Claude Android QA plugin to run a full emulator QA pass for this Android app.

Target package: com.example.app
Scenario: validate the main happy path from launch through settings and back to the home screen.

Please do the following:

1. Detect connected Android devices or emulators and choose one explicit serial.
2. If this repo has an Android Gradle project, discover the install tasks and install the debug build using the OS-appropriate Gradle wrapper command.
3. Resolve the launch activity for the package and launch the app.
4. Capture an initial screenshot and UI XML dump.
5. Summarize the UI tree and identify the visible primary navigation options.
6. Use UI XML, not screenshot guessing, to find and tap the Settings entry if present.
7. After opening Settings, capture:
   - screenshot
   - UI XML
   - compact UI summary
8. Verify that the Settings screen has at least one visible interactive control or settings row.
9. Press Back and verify the app returns to the previous/home screen.
10. Capture final screenshot and UI XML.
11. Collect logcat for the target package.
12. Return a concise QA report with:
   - device serial
   - package and resolved activity
   - build/install command used, or why install was skipped
   - each screen visited
   - screenshots and UI summary paths
   - any failed taps, missing UI targets, crashes, or logcat errors
   - final pass/fail result

Important constraints:
- Do not tap coordinates guessed from screenshots.
- Always derive tap coordinates from uiautomator XML bounds.
- If a target is missing, scroll once, dump the UI tree again, and re-check before calling it missing.
- Use the plugin wrapper commands first, and raw adb only when the wrapper does not cover the needed action.
```

Use this to test the explicit command entry point:

```text
/claude-android-qa-plugin com.example.app

Run a full QA pass: install if possible, launch, capture initial screenshot and UI XML, summarize the UI, navigate to Settings using XML-derived coordinates, capture evidence, return home, collect logcat, and report pass/fail with file paths.
```

Use this for a reproduction-style test:

```text
/claude-android-qa-plugin

Reproduce this Android UI issue: after launching the app, opening Settings, scrolling once, and returning with Back, the app should still show the home screen without crashing or freezing.

Use the Android QA workflow end to end:
- choose a connected emulator serial
- install the app from source if this repo supports it
- resolve and launch the package
- dump UI XML before every tap
- summarize each UI tree
- derive tap coordinates only from XML bounds
- capture screenshots at launch, Settings, after scroll, and after Back
- collect app logcat and crash buffer evidence
- finish with a concise reproduction report and note whether the bug reproduced
```

## Wrapper Examples

Use the device serial shown by `adb devices -l`. The first running emulator is often listed as `emulator-5554`, but the plugin does not assume that value.

List devices:

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/scripts/android-qa.py devices
```

Resolve an app activity:

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/scripts/android-qa.py resolve-activity --serial <serial> --package com.example.app
```

Dump and summarize UI XML:

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/scripts/android-qa.py dump-ui --serial <serial> --out android-qa-output/ui.xml
python3 ${CLAUDE_PLUGIN_ROOT}/scripts/android-qa.py summarize-ui --input android-qa-output/ui.xml --out android-qa-output/ui-summary.txt
```

Pick tap coordinates from exact UI text or content description:

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/scripts/android-qa.py pick --input android-qa-output/ui.xml --target "Settings"
```

Capture screenshots and logs:

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/scripts/android-qa.py screenshot --serial <serial> --out android-qa-output/screen.png
python3 ${CLAUDE_PLUGIN_ROOT}/scripts/android-qa.py logcat --serial <serial> --package com.example.app --out android-qa-output/logcat.txt
```

## Platform Notes

- macOS/Linux shells usually use `python3`; Windows PowerShell often uses `py -3` or `python`.
- macOS/Linux Gradle wrappers usually run as `./gradlew`; Windows projects often use `.\gradlew.bat`.
- `${CLAUDE_PLUGIN_ROOT}` is available when Claude Code runs plugin instructions. When running commands manually, replace it with the path to this plugin.
- The examples write to `android-qa-output/` so paths work naturally on Windows, macOS, and Linux.

## QA Notes

Always pick tap coordinates from UI XML, not screenshots. Screenshots are evidence; XML bounds are the source of truth for automation. If a target is missing and the current tree has scrollable nodes, scroll once, dump the tree again, and search again before concluding the target is absent.

## Troubleshooting

- `adb` is missing: install Android platform tools and ensure `adb` is on `PATH`.
- No devices appear: start an emulator, then run `adb devices -l`.
- Activity does not resolve: confirm the package name with `adb -s <serial> shell pm list packages`.
- Log capture reports no process: launch the app first, then retry log capture.
- Tap goes to the wrong location: dump fresh UI XML and recompute coordinates before tapping.
- For a guided run, ask Claude Code to use `/claude-android-qa-plugin <package-or-task>`.
