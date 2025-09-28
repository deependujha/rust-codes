# Rust Cargo Workspaces

A **workspace** is a group of crates managed together under one top-level Cargo.toml.
It allows shared dependency resolution, unified builds/tests, and centralized configuration.

---

## Declaration

Top-level `Cargo.toml`:

```toml
[workspace]
resolver = "2"         # use new feature resolver (optional)
members = ["crate_a", "crate_b", "examples/*"]
exclude = ["old_crate"]

[workspace.dependencies]   # optional
serde = "1"                # shared dependency for all members
```

* `resolver` → "1" (default) or "2" (new, more flexible, better for dependency resolution).
* `members` → list of crate paths included in the workspace. Can use globs (`examples/*`).
* `exclude` → paths to ignore (e.g., archived crates).
* `[workspace.dependencies]` → dependencies shared by all members.
    * Members can override locally or use `workspace = true` to inherit.

---

## Member Crates

`crate_a/Cargo.toml` example:

```toml
[package]
name = "crate_a"
version = "0.1.0"

[dependencies]
serde = { workspace = true }   # pulls version from workspace
tokio = { version = "1", optional = true }  # local override
crate_b = { path = "../crate_b" }           # inter-crate dependency
```

Notes:

* `workspace = true` → ensures all crates use the same version from `[workspace.dependencies]`.
* Inter-crate path dependencies are **local** and versioned independently from crates.io.
* Optional features can still be enabled per member.

---

## Common Workspace Features & Gotchas

> **Profiles** (`[profile.dev]`, `[profile.release]`)

    * Can be defined globally under `[workspace]`.
    * Member crates inherit unless overridden.

> **Shared Lints / Metadata**

```toml
[workspace.metadata]
clippy = { deny_warnings = true }
```

> **Publishing**

* Each member crate is published separately unless marked `publish = false`.
* Workspace crate **cannot be published itself**; only members.

> **Examples / Tests / Benches**

* Members can have their own `examples/`, `tests/`, `benches/`.
* Workspace allows `cargo test --all` or `cargo build --all`.

> **Dev-dependencies in Workspace**

* Dev-dependencies are **not shared by default**.
* `[workspace.dev-dependencies]` can be declared to avoid repeating them in each member.

> **Features Across Workspace**

* Each crate manages its own features.
* Can “forward” dependency features using `dep:crate` or `crate/feature`.
* `cargo build --all-features` → enables all features in all workspace members.

> **Overriding Dependencies**

```toml
[patch.crates-io]
serde = { git = "https://github.com/serde-rs/serde", branch = "master" }
```

* Overrides a crate globally for all workspace members.

---

## Commands

* `cargo build` → builds current crate
* `cargo build -p crate_a` → builds specific member
* `cargo build --all` → builds all workspace members
* `cargo test --all` → runs all tests across workspace
* `cargo check --workspace` → checks all members without building
* `cargo update -p crate_name` → update a dependency for all members

---

## Real-world Notes

* Large OSS projects use workspaces to avoid multiple lockfiles and keep dependencies consistent.
* Optional features and dev-dependencies often vary per crate; shared dependencies minimize version conflicts.
* Beware of cyclic dependencies — Cargo forbids them even inside a workspace.
* Path dependencies are local and **always take precedence** over crates.io.
