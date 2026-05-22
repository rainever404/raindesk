# RainDesk Agent Guide

## Project Identity

RainDesk is a second-development fork based on the upstream
[`rustdesk/rustdesk`](https://github.com/rustdesk/rustdesk.git) project.

This repository is the new long-lived RainDesk codebase. Future development
should treat `RainDesk` as the product name and should avoid reintroducing
RustDesk branding except where it is required for upstream attribution,
license notices, or compatibility with upstream code comments.

Keep upstream compatibility in mind. When possible, make RainDesk changes as
small, reviewable patches so future rebases or cherry-picks from upstream
RustDesk stay practical.

## Project Layout

### Directory Structure

* `src/` Rust app
* `src/server/` audio / clipboard / input / video / network
* `src/platform/` platform-specific code
* `src/ui/` legacy Sciter UI (deprecated)
* `flutter/` current UI
* `libs/hbb_common/` config / proto / shared utils
* `libs/scrap/` screen capture
* `libs/enigo/` input control
* `libs/clipboard/` clipboard
* `libs/hbb_common/src/config.rs` all options

### Key Components

* **Remote Desktop Protocol**: custom protocol implemented in
  `src/rendezvous_mediator.rs` for communicating with rustdesk-server
* **Screen Capture**: platform-specific screen capture in `libs/scrap/`
* **Input Handling**: cross-platform input simulation in `libs/enigo/`
* **Audio/Video Services**: real-time audio/video streaming in `src/server/`
* **File Transfer**: secure file transfer implementation in `libs/hbb_common/`

### UI Architecture

* **Legacy UI**: Sciter-based (deprecated), files in `src/ui/`
* **Modern UI**: Flutter-based, files in `flutter/`
  * Desktop: `flutter/lib/desktop/`
  * Mobile: `flutter/lib/mobile/`
  * Shared: `flutter/lib/common/` and `flutter/lib/models/`

## RainDesk Development Rules

* Prefer RainDesk naming in user-visible text, build metadata, package names,
  installer text, desktop entries, icons, and Flutter UI strings.
* Preserve upstream RustDesk license headers and attribution where required.
* Keep custom RainDesk behavior easy to identify and review.
* Avoid broad rewrites when a focused patch is enough.
* Do not add dependencies unless the change clearly needs them.
* Do not hard-code universal credentials or secrets into source code.
* Remote-control behavior must remain transparent to authorized users. Do not
  implement hidden access, stealth operation, or silent suppression of security
  notices.

## Rust Rules

* Avoid `unwrap()` / `expect()` in production code.
* Exceptions:
  * tests;
  * lock acquisition where failure means poisoning, not normal control flow.
* Otherwise prefer `Result` + `?` or explicit handling.
* Do not ignore errors silently.
* Avoid unnecessary `.clone()`.
* Prefer borrowing when practical.
* Do not add dependencies unless needed.
* Keep code simple and idiomatic.

## Tokio Rules

* Assume a Tokio runtime already exists.
* Never create nested runtimes.
* Never call `Runtime::block_on()` inside Tokio / async code.
* Do not hide runtime creation inside helpers or libraries.
* Do not hold locks across `.await`.
* Prefer `.await`, `tokio::spawn`, channels.
* Use `spawn_blocking` or dedicated threads for blocking work.
* Do not use `std::thread::sleep()` in async code.

## Editing Hygiene

* Change only what is required.
* Prefer the smallest valid diff.
* Do not refactor unrelated code.
* Do not make formatting-only changes.
* Keep naming/style consistent with nearby code.
* Read nearby code before editing, especially under `libs/hbb_common`,
  `src/server`, and `flutter/lib`.
* Verify builds or targeted tests after each meaningful step when practical.
