---
title: "Use UUID in SQLite database with Rust + Diesel.rs"
date: 2022-12-26T19:05:00+01:00
draft: false
toc: false
images:
tags:
  - rust
  - sqlite
  - ORM
  - diesel.rs
---

Recently in one of my project I ran into a problem where I wanted to use UUIDs in my database but as it turns out SQLite only support 5 data types : `TEXT`, `INTEGER`, `REAL`, `NUMERIC` and `BLOB`. This in itself is not really a concern, as I use an ORM (Object-relational mapping) called [diesel.rs](https://diesel.rs/) in my project.

The issue that I encountered is in the ORM itself. The crate diesel.rs as an optional feature, allowing the use of the type Uuid from the rust crate uuid. Issue is, this feature only works with PostgreSQL backends, and the only place where it's mentioned is deep in the documentation. So I spent a good amount of time trying to figure out why this feature wasn't working. Anyway, I still wanted to use UUIDs in my database without having to each time explicitly convert it to a type accepted by it.

The solution ? Creating a proxy type to handle the magic for me :

```rust
use diesel::deserialize::{self, FromSql};
use diesel::serialize::{self, Output, ToSql};
use diesel::sql_types::Binary;
use diesel::sqlite::Sqlite;
use diesel::{AsExpression, FromSqlRow};
use std::fmt;
use std::fmt::{Display, Formatter};
use uuid;

#[derive(Debug, Clone, Copy, FromSqlRow, AsExpression, Hash, Eq, PartialEq)]
#[sql_type = "Binary"]
pub struct UUID(pub uuid::Uuid);

// Small function to easily initialize our uuid
impl UUID {
    pub fn random() -> Self {
        Self(uuid::Uuid::new_v4())
    }
}

// Allow easy conversion from UUID to the wanted uuid::Uuid
impl From<UUID> for uuid::Uuid {
    fn from(s: UUID) -> Self {
        s.0
    }
}

impl Display for UUID {
    fn fmt(&self, f: &mut Formatter<'_>) -> fmt::Result {
        write!(f, "{}", self.0)
    }
}

// Convert binary data from SQLite to a UUID
impl FromSql<Binary, Sqlite> for UUID {
    fn from_sql(bytes: diesel::backend::RawValue<'_, Sqlite>) -> deserialize::Result<Self> {
        let bytes = <*const [u8] as FromSql<Binary, Sqlite>>::from_sql(bytes)?;
        let bytes = unsafe { &*bytes };
        let uuid = uuid::Uuid::from_slice(bytes).map_err(|_| "Invalid UUID")?;
        Ok(UUID(uuid))
    }
}

// Convert UUID to binary data for SQLite
impl ToSql<Binary, Sqlite> for UUID {
    fn to_sql<'b>(&'b self, out: &mut Output<'b, '_, Sqlite>) -> serialize::Result {
        Ok(<[u8] as ToSql<Binary, Sqlite>>::to_sql(
            self.0.as_bytes(),
            out,
        )?)
    }
}
```

With that, we just need to use the `BLOB` type to store UUID as raw binary data in SQLite :

```sql
CREATE TABLE tags (
    uuid BLOB PRIMARY KEY NOT NULL,
    name TEXT NOT NULL UNIQUE,

    created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

And finally, use our new `UUID` type in our rust code :

```rust
#[derive(Queryable, Debug)]
pub struct Tag {
    pub id: UUID,
    pub name: String,
    pub display_name: String,
    pub created_at: NaiveDateTime,
    pub updated_at: NaiveDateTime,
}

#[derive(Insertable)]
#[diesel(table_name = tags)]
pub struct NewTag<'a> {
    pub uuid: &'a UUID,
    pub name: &'a str,
    pub display_name: &'a str,
    pub created_at: &'a NaiveDateTime,
    pub updated_at: &'a NaiveDateTime,
}
```
