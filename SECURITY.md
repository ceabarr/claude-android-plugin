# Security Policy

## Supported Versions

Security updates are provided for the latest released version of Claude Android QA Plugin.

| Version | Supported |
| ------- | --------- |
| 1.x     | Yes       |
| < 1.0   | No        |

## Reporting a Vulnerability

Please report security issues using GitHub private vulnerability reporting for this repository.

Do not open a public issue for security-sensitive reports.

When reporting a vulnerability, include as much detail as you can safely share:

- affected version or commit
- operating system
- Claude Code version, if relevant
- Android emulator or device details, if relevant
- steps to reproduce
- impact of the issue
- any relevant logs or command output with secrets removed

Please do not include API keys, access tokens, private app source code, customer data, or sensitive logs.

## Response Expectations

I will review security reports as soon as reasonably possible.

If the report is valid, I will work on a fix and coordinate disclosure through GitHub private vulnerability reporting. If the report is not accepted as a vulnerability, I will explain why when possible.

## Scope

Examples of in-scope issues:

- command execution vulnerabilities in helper scripts
- unsafe handling of file paths or shell arguments
- behavior that could expose private app data, logs, screenshots, or emulator output unexpectedly
- plugin instructions that could cause unsafe or unintended actions

Examples of out-of-scope issues:

- vulnerabilities in Android, `adb`, emulators, Claude Code, Python, or Gradle themselves
- issues that require already having full local machine access
- reports based only on unsupported or modified versions of the plugin
- spam, social engineering, or denial-of-service reports without a concrete plugin vulnerability
