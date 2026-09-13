# Agent context

Shared context for every AI assistant used on this repository: Cursor, GitHub
Copilot, Claude, Gemini and the Xcode extension itself. The other instruction
files (`CLAUDE.md`, `GEMINI.md`, `.github/copilot-instructions.md`,
`.cursor/rules/`) point here, so this file is the single place to edit.

## Project

GitHub Copilot for Xcode: a macOS app plus Xcode source editor extension.
Requires macOS 13+, Xcode 14+ and a GitHub account.

Targets, and where the code lives:

- `Copilot for Xcode` — host app and settings UI. Implemented in `Core/HostApp`.
- `EditorExtension` — sandboxed Xcode source editor extension. Forwards editor
  content to the XPC service and writes the result back.
- `ExtensionService` — background process where the features live. Implemented
  in `Core/Service`.
- `CommunicationBridge` — keeps the host app and extension talking to
  `ExtensionService` across the sandbox boundary.
- `Core` and `Tool` — Swift packages holding most of the logic.

`DEVELOPMENT.md` has the full description; read it before restructuring targets.

## Build and test

Node and `npm` must be on the system path, because an Xcode run script resolves
them with a very limited `PATH`:

```sh
sudo ln -s `which npm` /usr/local/bin
sudo ln -s `which node` /usr/local/bin
```

Local build:

```sh
cd ./Script
sh ./uninstall-app.sh    # remove any previous installation
rm -rf ../build          # clean the build directory
sh ./localbuild-app.sh   # build a fresh copy
```

Unit tests run from the `Copilot for Xcode` target. New tests belong in
`TestPlan.xctestplan`. To exercise the editor extension, run `ExtensionService`,
`CommunicationBridge` and `EditorExtension` together.

Versions for the app and all targets come from `Version.xcconfig`.

## Code style

SwiftFormat, following the Ray Wenderlich Swift style guide, with 4-space
indentation (the Xcode default) rather than 2.

## Working agreements

- Keep answers short. One task finished at a time, then say what is still
  waiting.
- Split the work by tool: VS Code for running, debugging and small fixes;
  Claude for long explanations when the error message is already in hand;
  Perplexity for looking things up on the web; Cursor when many files have to be
  read and changed at once.
- Do not start heavy subagents or long research rounds unless asked for.
- Never update, upgrade or self-update software unless the request says so in
  that same message. This covers `brew update`/`brew upgrade`,
  `pip install --upgrade`, npm and yarn upgrades, `mas`, OpenFOAM and Homebrew
  Python, and app self-updates. Installing a missing package that was asked for
  is fine; upgrading existing ones is not.
- Never change git config.
