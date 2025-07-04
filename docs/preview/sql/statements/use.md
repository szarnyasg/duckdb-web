---
layout: docu
railroad: statements/use.js
title: USE Statement
---

The `USE` statement selects a database and optional schema to use as the default.

## Examples

```sql
--- Sets the 'memory' database as the default. Will use 'main' schema implicitly or error
--- if it does not exist.
USE memory;
--- Sets the 'duck.main' database and schema as the default
USE duck.main;
```

## Syntax

<div id="rrdiagram1"></div>

The `USE` statement sets a default database or database/schema combination to use for
future operations. For instance, tables created without providing a fully qualified
table name will be created in the default database.
