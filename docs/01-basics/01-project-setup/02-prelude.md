# Prelude

## What is a Prelude?

* A **prelude** is a module that **re-exports commonly used items**.
* Users import it once:

  ```rust
  use my_crate::prelude::*;
  ```
* Purpose: reduce repetitive `use` statements.
* **No magic** — it is just `pub use`.

---

## Is `prelude` a special keyword?

**No.**

* `prelude` is **not** a reserved word.
* Rust treats it like any other module name.
* It is special only by **convention**, not by the language.

These are equivalent to the compiler:

```rust
pub mod prelude { ... }
pub mod foo { ... }
pub mod whatever { ... }
```

---

## Why the name `prelude` exists

* The standard library uses `std::prelude`
* Ecosystem adopted the pattern
* Readers immediately understand intent

> `prelude` is a **social contract**, not a language feature.

---

## Standard Prelude (built-in)

Automatically imported by Rust.

Includes:

* Types: `Option`, `Result`, `Vec`, `String`
* Enums: `Some`, `None`, `Ok`, `Err`
* Traits: `Copy`, `Clone`, `Drop`, `Sized`, `Into`, `From`

Defined in:

* `std::prelude::v1`
* `core::prelude::v1` (`no_std`)

You usually never touch this directly.

---

## Custom Prelude (your crate)

### Minimal Pattern

```rust
pub mod prelude {
    pub use crate::foo::FooExt;
    pub use crate::bar::BarExt;
}
```

Usage:

```rust
use my_crate::prelude::*;
```

Prelude is **opt-in**.

---

## What belongs in a Prelude ✅

**Primary purpose: traits**

Good candidates:

* Extension traits (`FooExt`)
* Operator-like traits
* Iterator / async helpers
* Math / SIMD traits
* Domain traits used everywhere

Example:

```rust
pub trait TensorExt {
    fn norm(&self) -> f32;
}
```

---

## What does NOT belong ❌

Avoid:

* Concrete structs/enums (unless truly fundamental)
* Internal helpers
* One-off implementations
* Entire external crates

Bad:

```rust
pub use anyhow;
pub use petgraph;
pub use tracing;
```

Reason:

* hides dependencies
* hurts clarity
* increases name conflicts

---

## Re-export vs Define

Prelude should **re-export**, not define.

Good:

```rust
pub use crate::graph::GraphExt;
```

Bad:

```rust
pub trait GraphExt { ... } // hidden inside prelude
```

Prelude is an **index**, not a junk drawer.

---

## Visibility Rule

Items must be `pub`:

```rust
pub trait FooExt { ... }
```

Prelude does **not** change visibility.

---

## External Crates (best practice)

Split layers if needed:

```rust
pub mod prelude {
    pub use crate::op::{DType, Runtime};
}

pub mod dev_prelude {
    pub use anyhow;
    pub use tracing;
}
```

Keeps the public API honest.

---

## Macros

Macros are special; re-export carefully.

Preferred:

```rust
pub mod macros {
    pub use paste::paste;
}
```

Usage:

```rust
use my_crate::macros::paste;
```

---

## Naming Notes

* Module names are part of the **public API**
* Non-idiomatic names reduce trust/adoption
* Follow conventions intentionally

Recommended:

```rust
prelude
dev_prelude
macros
```

---

## You can name it anything (technically)

```rust
pub mod fuck_it {
    pub use crate::op::{DType, Runtime};
}
```

Usage:

```rust
use my_crate::fuck_it::*;
```

Works exactly like `prelude`.
Not recommended for public APIs.

---

## When to Create a Prelude

Good idea when:

* crate is trait-heavy
* users import many items together
* examples get noisy

Avoid when:

* crate is small
* users import 1–2 symbols
* API is type-centric

---

## Mental Model (important)

> A prelude is just a **recommended `use` list**.

No compiler tricks.
No hidden behavior.
If it feels confusing → the prelude is too big.

---

## Sanity Check

Ask:

* Can readers tell what comes from **my crate vs dependencies**?
* Would removing this item surprise most users?

If unsure → don’t put it in the prelude.
