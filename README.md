# vergen
## Version
[![Crates.io](https://img.shields.io/crates/v/vergen.svg)](https://crates.io/crates/vergen)
[![Build
Status](https://travis-ci.org/rustyhorde/vergen.svg?branch=master)](https://travis-ci.org/rustyhorde/vergen)

## Breaking Changes
### Version 3.x.x
Introduces `generate_cargo_keys()` and support for rebuild when `.git/HEAD` changes.
Internally converted to use `failure` so `Result` is no longer exported and changed to the Rust 2018
edition.

**This means the 3.x.x version will only work in the beta and nightly channels until Rust 2018
hits stable (12/06/18)**

### Version 2.1.x
Backport of the 3.x.x changes to work on stable until Rust 2018 hits stable.

### Version 2.0.x
Compatible with Version 1.x.x, but introduces a completely new way to use the constants without having to
use the `include!` macro.

### Version 1.x.x
A breaking change from the 0.1.0 series.  This crate no longer only generates functions
to display the build time information, but rather generates constants.  See below for more detail.

## Documentation
[Documentation](https://docs.rs/vergen)

## Generate Build Time Information
`vergen`, when used in conjunection with cargo [build scripts], will
generate environment variables to use with the `env!` macro.  Below
is a list of the supported variables.

Key                       | Sample Value
--------------------------|----------------------------------------
VERGEN_BUILD_TIMESTAMP    |2018-08-09T15:15:57.282334589+00:000
VERGEN_BUILD_DATE         |2018-08-09
VERGEN_SHA                |75b390dc6c05a6a4aa2791cc7b3934591803bc22
VERGEN_SHA_SHORT          |75b390d
VERGEN_COMMIT_DATE        |2018-08-08
VERGEN_TARGET_TRIPLE      |x86_64-unknown-linux-gnu
VERGEN_SEMVER             |v3.0.0
VERGEN_SEMVER_LIGHTWEIGHT |v3.0.0

The variable generation can be toggled on or off at an individual level
via [ConstantsFlags](crate::constants::ConstantsFlags)

### Note on `SEMVER`
`VERGEN_SEMVER` can be generated via `git describe` or by
`env::var("CARGO_PKG_VERSION")`.

By default, `SEMVER` uses `git describe` if possible, and falls back to `CARGO_PKG_VERSION`.

If you wish to force `CARGO_PKG_VERSION`, toggle off `SEMVER` and toggle
on `SEMVER_FROM_CARGO_PKG`.

## Re-build On Changed HEAD
`vergen` can also be configured to re-run `build.rs` when either `.git/HEAD` or
the file that `.git/HEAD` points at changes.

This can behavior can be toggled on or of with the [REBUILD_ON_HEAD_CHANGE] flag.

[REBUILD_ON_HEAD_CHANGE]: crate::constants::ConstantsFlags::REBUILD_ON_HEAD_CHANGE
[build scripts]: https://doc.rust-lang.org/cargo/reference/build-scripts.html

## 'cargo:' Key Build Script Output
```toml
[package]
#..
build = "build.rs"

[dependencies]
#..

[build-dependencies]
vergen = "3"
```

### Example 'build.rs'

```rust
extern crate vergen;

use vergen::{ConstantsFlags, generate_cargo_keys};

fn main() {
    // Setup the flags, toggling off the 'SEMVER_FROM_CARGO_PKG' flag
    let mut flags = ConstantsFlags::all();
    flags.toggle(ConstantsFlags::SEMVER_FROM_CARGO_PKG);

    // Generate the 'cargo:' key output
    generate_cargo_keys(ConstantsFlags::all()).expect("Unable to generate the cargo keys!");
}
```

### Use the constants in your code

```rust
fn my_fn() {
    println!("Build Timestamp: {}", env!("VERGEN_BUILD_TIMESTAMP"));
}
```

## License

Licensed under either of
 * Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE) or http://www.apache.org/licenses/LICENSE-2.0)
 * MIT license ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)
at your option.

### Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in the work by you, as defined in the Apache-2.0 license, shall be dual licensed as above, without any
additional terms or conditions.
