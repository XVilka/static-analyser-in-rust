# The Abstract Syntax Tree

```rust
#![allow(missing_docs)]
use codemap::Span;
```

The most basic element of the AST is a `Literal`. These are either integer,
float, or string literals.

```rust
#[derive(Debug, Clone, PartialEq, Serialize, Deserialize)]
#[serde(tag = "type")]
pub enum LiteralKind {
    Integer(usize),
    Decimal(f64),
    String(String),
}

#[derive(Debug, Clone, PartialEq, Serialize, Deserialize)]
pub struct Literal {
    pub span: Span,
    pub kind: LiteralKind,
}
```

We also want to add a couple `From` impls so creating a `LiteralKind` is easy
to do.

```rust
impl From<usize> for LiteralKind {
    fn from(other: usize) -> LiteralKind {
        LiteralKind::Integer(other)
    }
}

impl From<f64> for LiteralKind {
    fn from(other: f64) -> LiteralKind {
        LiteralKind::Decimal(other)
    }
}

impl PartialEq<LiteralKind> for Literal {
    fn eq(&self, other: &LiteralKind) -> bool {
        &self.kind == other
    }
}
```

We also want to deal with identifiers and dot-separated identifiers.

```rust
#[derive(Debug, Clone, PartialEq, Serialize, Deserialize)]
pub struct Ident {
    pub span: Span,
    pub name: String,
}

#[derive(Debug, Clone, PartialEq, Serialize, Deserialize)]
pub struct DottedIdent {
    pub span: Span,
    pub parts: Vec<Ident>,
}

impl<'a> PartialEq<&'a str> for Ident {
    fn eq(&self, other: &&str) -> bool {
        &self.name == other
    }
}

impl<'a, T: AsRef<[&'a str]>> PartialEq<T> for DottedIdent {
    fn eq(&self, other: &T) -> bool {
        self.parts.iter()
            .zip(other.as_ref().iter())
            .all(|(l, r)| l == r)
    }
}
```