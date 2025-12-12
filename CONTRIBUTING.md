# Contributing to Nullbit1

Thanks for wanting to poke at this codebase.

Nullbit1 is mostly low-level and security-related projects. That means a couple of things:

- changes can break other tools in subtle ways;
- we care a lot about correctness and clarity;
- "drive‑by" cosmetic PRs are usually not helpful.

If you are okay with that, read on.

## Before you start

1. **Pick a repo and read its README.**  
   Each project has its own scope, status and stack.
2. **Search existing issues.**  
   Maybe someone already reported the bug or proposed the feature.
3. **Open an issue first for non‑trivial work.**  
   For big changes, design tweaks or new features, please discuss them in an issue before writing a large PR.

Small typo fixes or obviously correct one‑liner fixes do not need a prior issue.

## How we work

Typical workflow:

1. Fork the repo or create a feature branch if you have write access.
2. Create a branch with a descriptive name, e.g. `aegishv/fix-ept-race`, `volguard/add-dump-filter`.
3. Commit your changes in small, logical chunks.
4. Make sure tests and linters pass locally.
5. Open a PR against the `main` (or documented default) branch.

A maintainer will review the PR when they have time. We do not guarantee fast responses — this is a spare-time lab, not a company.

## Code style

Different repos use different languages. Some baseline rules still apply.

### General

- Prefer explicit code over clever one-liners.
- Keep functions small and focused.
- Add comments where behaviour is non-obvious, not for every line.
- Avoid bringing in huge dependencies for tiny tasks.
- No hidden telemetry, no auto-updaters sneaking in.

### C / C++

- Use `clang-format` (or the repo's `.clang-format`) before committing.
- Watch for lifetime/ownership; undefined behaviour in security tools is not fun.
- If you touch low-level parts (hypervisor, kernel), add short comments about assumptions and invariants.

### Rust

- Run `cargo fmt` and `cargo clippy` with `-D warnings` where possible.
- Prefer `Result` over panics in library code; binaries can be stricter.
- Keep unsafe blocks minimal and well-documented.

### Go

- Run `go fmt ./...` and `go vet ./...`.
- Keep packages small and with clear boundaries.

### Python / scripting

- Keep scripts readable; they are often used for incident response by tired people at 3AM.
- Prefer standard library; only add dependencies if they clearly pay for themselves.

### Tests

- If you fix a bug, add a test that fails without your fix and passes with it.
- For low-level parts where tests are hard, at least add reproducer instructions in the PR description.

## Security-sensitive contributions

If your change:

- touches verification/detection logic;
- adjusts how we collect or process security telemetry;
- adds new parsing of untrusted data;

…please call that out explicitly in the PR description and link any relevant docs, PoCs or references.

If you discovered a **vulnerability** in one of the projects, do **not** open a regular issue. Follow the process in [SECURITY.md](SECURITY.md).

## Commit messages

- Use short, imperative titles: `fix race in EPT handler`, `add basic memdump filter`.
- Put longer explanations in the body, wrapped around 72 characters per line.
- Reference issues as `Fixes #123` when relevant.

We do not enforce a strict Conventional Commits scheme, but you are free to use it if you like.

## Licensing

Most projects here are under permissive licenses (MIT, Apache-2.0 or similar). By submitting a contribution, you agree that it can be licensed under the project's existing license.

If you cannot agree to that for some reason, please say so clearly in the PR so we can decide what to do.

---

If something in this file looks wrong or overkill for a specific repo, open an issue or PR to adjust it. This org is evolving together with the tools.
