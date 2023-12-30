# bitflags-serde-legacy

The serialization format used by [`bitflags!`](docs.rs/bitflags) has changed between `1.x` and
`2.x`. If you previously `#[derive(Serialize, Deserialize]`, then you can use this library to
maintain compatibility while upgrading.

# Usage

You can either use this as a regular dependency, or pull the source into a private module
in your project.

Add `bitflags-serde-legacy` to your `Cargo.toml`:

```toml
[dependencies.bitflags-serde-legacy]
version = "0.1.1"
```

Then, replace an existing `#[derive(Serialize, Deserialize)]` on your `bitflags!`
generated types with the following manual implementations:

```rust
use bitflags::bitflags;

bitflags! {
    // #[derive(Serialize, Deserialize)]
    struct Flags: u32 {
        const A = 0b00000001;
        const B = 0b00000010;
        const C = 0b00000100;
        const ABC = Self::A.bits() | Self::B.bits() | Self::C.bits();
    }
}

impl serde::Serialize for Flags {
    fn serialize<S: serde::Serializer>(&self, serializer: S) -> Result<S::Ok, S::Error> {
        bitflags_serde_legacy::serialize(self, "Flags", serializer)
    }
}

impl<'de> serde::Deserialize<'de> for Flags {
    fn deserialize<D: serde::Deserializer<'de>>(deserializer: D) -> Result<Self, D::Error> {
        bitflags_serde_legacy::deserialize("Flags", deserializer)
    }
}
```
