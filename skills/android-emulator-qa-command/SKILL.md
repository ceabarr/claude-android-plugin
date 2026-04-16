---
name: android-emulator-qa
description: Drive Android emulator QA with adb, UI tree inspection, screenshots, and logcat evidence
argument-hint: [package-or-task]
allowed-tools: [Bash, Read, Glob, Grep]
---

# Android Emulator QA Command

The user invoked `/android-emulator-qa` with: $ARGUMENTS

Use the argument as optional package, app, feature, or task context. If the argument looks like an Android package name, use it for package discovery, activity resolution, and log capture. If it looks like a feature or bug description, use it as the scenario to validate.

## Command Workflow

1. List devices:
   ```bash
   python3 ${CLAUDE_PLUGIN_ROOT}/scripts/android-qa.py devices
   ```

2. Build or install when app source is present:
   - Run exactly one Gradle wrapper style based on the host OS.
   - macOS/Linux:
     ```bash
     ./gradlew tasks --all | rg install
     ./gradlew :app:installDebug --console=plain
     ```
   - Windows PowerShell:
     ```powershell
     .\gradlew.bat tasks --all | rg install
     .\gradlew.bat :app:installDebug --console=plain
     ```

3. Resolve and launch the app:
   ```bash
   python3 ${CLAUDE_PLUGIN_ROOT}/scripts/android-qa.py resolve-activity --serial <serial> --package <package>
   adb -s <serial> shell am start -n <package>/<activity>
   ```

4. Inspect UI before tapping:
   ```bash
   python3 ${CLAUDE_PLUGIN_ROOT}/scripts/android-qa.py dump-ui --serial <serial> --out android-qa-output/ui.xml
   python3 ${CLAUDE_PLUGIN_ROOT}/scripts/android-qa.py summarize-ui --input android-qa-output/ui.xml --out android-qa-output/ui-summary.txt
   python3 ${CLAUDE_PLUGIN_ROOT}/scripts/android-qa.py pick --input android-qa-output/ui.xml --target "Settings"
   adb -s <serial> shell input tap <x> <y>
   ```

5. Capture evidence:
   ```bash
   python3 ${CLAUDE_PLUGIN_ROOT}/scripts/android-qa.py screenshot --serial <serial> --out android-qa-output/screen.png
   python3 ${CLAUDE_PLUGIN_ROOT}/scripts/android-qa.py logcat --serial <serial> --package <package> --out android-qa-output/logcat.txt
   ```

## Raw adb Fallbacks

Use direct adb when needed:

```bash
adb devices
adb -s <serial> shell pm list packages | rg <namespace>
adb -s <serial> shell cmd package resolve-activity --brief <package>
adb -s <serial> exec-out uiautomator dump /dev/tty > android-qa-output/ui.xml
adb -s <serial> exec-out screencap -p > android-qa-output/screen.png
adb -s <serial> shell input tap <x> <y>
adb -s <serial> shell input swipe <x1> <y1> <x2> <y2>
adb -s <serial> shell input keyevent 4
adb -s <serial> logcat -d > android-qa-output/logcat.txt
```

## Output

Return a concise QA report with:

- Device serial
- Package and activity
- Scenario from `$ARGUMENTS`, if provided
- Steps performed
- Screenshot and log paths
- Result, blocker, or reproduction details
