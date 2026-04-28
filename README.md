# Pintos Test Explorer

Languages: English | [한국어](README.ko.md)

Run, debug, reset, and inspect Pintos tests from VS Code, and use the exact same workflow from the terminal with either `pt` or `pintos-tests`.

## Demo

[![Watch the demo video](https://img.youtube.com/vi/FyJ1jKg3zNk/hqdefault.jpg)](https://youtu.be/FyJ1jKg3zNk)

Watch the walkthrough: [YouTube demo video](https://youtu.be/FyJ1jKg3zNk)

## What This Gives You

- A dedicated VS Code sidebar for browsing `threads`, `userprog`, `vm`, and `filesys` tests
- One-click run and debug actions per test
- Batch execution for checked tests
- Quick links for `output`, `result`, and `errors` artifacts
- Reset commands for selected tests or the entire workspace
- A companion terminal CLI for local use, shell scripts, and CI jobs
- A single CLI surface exposed as both `pt` and `pintos-tests`

## Install

### VS Code Extension

1. Open `Extensions` in VS Code.
2. Search for `Pintos Test Explorer`.
3. Click `Install`.
4. If you are using a Dev Container, install it into the container too.
5. Run `Developer: Reload Window` once after installation.

### CLI From The Published Extension

Inside the VS Code integrated terminal where the extension is active, both `pt` and `pintos-tests` are exposed automatically. Open a new terminal after the extension activates, then run:

```bash
pt --help
pintos-tests --help
```

If you want the same commands in every shell, run the Command Palette action `Pintos: Install CLI Wrappers to Shell`.

### CLI From A Source Checkout

If you are working from a clone of this repository, you have three practical options:

1. One-time shell installation:

```bash
source scripts/install-pintos-cli.sh
pt --help
```

2. Current shell only:

```bash
source scripts/pintos-shell.sh
pt --help
```

3. Repo-local direct commands, which are especially handy in CI:

```bash
./pt --help
./pintos-tests --help
```

All three options ultimately run the same CLI implementation.

## Quick Start

### In VS Code

1. Open your Pintos workspace in a Dev Container or Linux environment.
2. Click the `P os` icon in the Activity Bar.
3. Expand `Threads`, `User Programs`, `Virtual Memory`, or `File System`.
4. Click the green `Run` action next to a test to execute it.
5. Click the orange `Debug` action to start a GDB session for one test.
6. Check multiple tests and use `Run Checked Tests` from the toolbar.
7. Use the sort button to switch between `Number order` and `Latest first`.
8. Use the leftmost red `Reset All Tests` button to clear the whole workspace, or `Reset Checked Tests` to reset only the selected tests.

When a test already has artifacts, the tree shows links for `output`, `result`, and `errors`.

### In The Terminal

Inside a Pintos workspace:

```bash
pt projects
pt list threads
pt run threads alarm-zero
pt run threads 11-20
pt debug vm 4 --server-only
pt reset threads alarm-zero
pt reset-all
pt artifacts threads alarm-zero
```

Outside the workspace, point to it explicitly:

```bash
PINTOS_ROOT=/path/to/pintos pt list threads
PINTOS_ROOT=/path/to/pintos pintos-tests run filesys all
```

`pt` is the short everyday name. `pintos-tests` is the explicit long name. They accept the same subcommands and flags.

## Command Reference

Every command below works with both `pt` and `pintos-tests`. The examples use `pt` for brevity.

### `projects`

Show which Pintos projects are available in the current workspace.

```bash
pt projects
pt projects --json
```

Use this when a script wants to discover whether `threads`, `userprog`, `vm`, or `filesys` are available.

### `list <project>`

Print the test list for one project.

```bash
pt list threads
pt list vm --recent-first
pt list filesys --json
```

- `--recent-first` moves recently used tests to the top.
- `--json` prints machine-friendly metadata.

### `pick <project> [selectors...]`

Resolve selectors and print only the matched test names. This is mainly for automation and scripting.

```bash
pt pick threads 1 3-5 alarm-*
pt pick threads alarm-* --recent-first
pt pick threads alarm-zero --single
```

- `--single` requires exactly one match.
- `pick` is useful when another script wants a normalized test name before calling `run`, `debug`, or artifact upload logic.

### `run <project> [selectors...]`

Run one or more tests.

```bash
pt run threads alarm-zero
pt run threads 1 3-5 alarm-*
pt run filesys all
```

- `all` is allowed here.
- If you omit selectors, the CLI prints the project test list and prompts interactively.
- Build-time failures are still recorded as `FAIL`, and the error text is preserved in artifacts when possible.

### `debug <project> [selectors...]`

Debug exactly one test.

```bash
pt debug threads 12
pt debug threads alarm-zero
pt debug vm 4 --server-only
```

- `debug` must resolve to exactly one test.
- `--server-only` starts only the Pintos GDB server and does not attach GDB automatically.
- If you omit selectors, the CLI drops into interactive single-test selection.

### `reset <project> [selectors...]`

Delete `output`, `result`, and `errors` artifacts for selected tests.

```bash
pt reset threads alarm-zero
pt reset threads 4 7-9
pt reset threads all
pt reset threads alarm-* --json
```

- `all` is allowed here.
- `--json` prints a summary that is useful in scripts or CI logs.
- If you omit selectors, the CLI prompts interactively.

### `reset-all`

Delete every test artifact in the workspace.

```bash
pt reset-all
pt reset-all --json
```

Use this when you want a clean workspace-wide reset instead of a project-scoped cleanup.

### `artifacts <project> <selector>`

Print artifact paths for exactly one test.

```bash
pt artifacts threads alarm-zero
pt artifacts threads alarm-zero --json
```

This is useful when you want to open, inspect, or upload the generated `output`, `result`, and `errors` files.

## Selector Rules

- `11-20` means an inclusive numeric range.
- `alarm-zero` selects by exact short name.
- `tests/threads/alarm-zero` also works.
- `alarm-*` works as a wildcard pattern.
- Bare leaf names also work when they uniquely match a test.
- `all` is supported by `run` and project-scoped `reset`.
- `debug` and `artifacts` must resolve to exactly one test.

If a selector does not match, the CLI tries to suggest similar names.

## Artifacts And History

Each run may produce these artifact files:

- `output`: captured command output
- `result`: the Pintos result summary such as `PASS` or `FAIL`
- `errors`: captured error details

The tree view and CLI both use the same artifacts. `--recent-first` is backed by local run/debug history so you can bring frequently used tests to the top quickly.

## CI And Automation

`pt` and `pintos-tests` are intentionally interchangeable. They are not separate tools with different behavior. `pt` is the short entry point, and `pintos-tests` is the more explicit long name. In CI, shell scripts, and local terminals, you can mix them freely.

Practical guidance:

- Use `./pintos-tests ...` in CI when you want logs to be extra explicit.
- Use `./pt ...` when you want shorter commands.
- Use `source scripts/pintos-shell.sh` if you want `pt` and `pintos-tests` on `PATH` for the current shell step.
- Use `projects --json`, `list --json`, `pick`, `artifacts --json`, `reset --json`, and `reset-all --json` for machine-friendly automation.

Examples:

```bash
./pintos-tests list threads --json
./pt pick threads alarm-zero --single
./pintos-tests run threads 1 3-5
./pt artifacts threads alarm-zero --json
```

Mixing both names in one workflow is fine:

```yaml
- name: Run Pintos smoke tests
  run: |
    ./pintos-tests run threads alarm-zero
    ./pt run threads 1-3
```

## Requirements

- VS Code `1.85.0` or newer
- A Pintos workspace in either of these layouts:
  - `<workspace>/threads`, `<workspace>/userprog`, `<workspace>/vm`, `<workspace>/tests`
  - `<workspace>/pintos/threads`, `<workspace>/pintos/userprog`, `<workspace>/pintos/vm`, `<workspace>/pintos/tests`
- A Linux or Dev Container environment with `make`
- `gdb` on your `PATH` for debug sessions
- `ms-vscode.cpptools`

## Troubleshooting

- If `pt list ...` says it cannot find a Pintos project root, open the real Pintos workspace or set `PINTOS_ROOT=/path/to/pintos`.
- If VS Code debug startup fails, open the `Pintos Tests` output channel first.
- If a run stops at a compile or build error, the extension marks that test as `FAIL` and keeps the captured error output in the artifacts when possible.
- If debugging does not start, confirm that `gdb` is installed in the active environment.

## More Details

- [`extension/README.md`](extension/README.md)
- [`extension/SUPPORT.md`](extension/SUPPORT.md)
- [`extension/CHANGELOG.md`](extension/CHANGELOG.md)
