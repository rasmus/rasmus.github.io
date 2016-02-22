---
layout:   post
title:    "EventFlow MSSQL NuGet packages merge"
date:     2016-02-22 21:54:00 +1
category: eventflow
disqus:   true
tags:
  - eventflow
  - nuget
  - open source
---

Currently it takes [EventFlow](https://github.com/rasmus/EventFlow) no less than
five EventFlow NuGet packages to get support for Microsoft SQL Server if you
want to use it for both event store and read store.

- `EventFlow`: Core package
- `EventFlow.Sql`: Contains general SQL support for EvenFlow which is shared
  with the SQLite event and read model stores
- `EventFlow.MsSql`: General MSSQL support
- `EventFlow.ReadStores.MsSql`: MSSQL read store support
- `EventFlow.EventStores.MsSql`: MSSQL read store support

In an upcoming release of EventFlow, the `EventFlow.ReadStores.MsSql` and
`EventFlow.EventStores.MsSql` will be deprecated and the containing
functionality will be moved to the `EventFlow.MsSql` package.

Changing the package layout is something that will be somewhat annoying for
any developer that use EventFlow. But I hope by making this change now, it will
ease managing EventFlow packages in the future.  

To ease the transition, the deprecated packages will be provided for a few
releases, but they will not contain any DLL files. In the general case, the only
breaking change will be a new namespace for the `UseMssqlReadModel` and
`UseMssqlEventStore` extension methods.

The changes to EventFlow itself would have been trivial if it wasn't for the
inclusion of SQL migration scripts for setting up the event store table and
table type used to pass events effectively to the MSSQL server. The scripts
will be moved, but they will also have to be updated to be idempotent, i.e.,
make sure they don't try to execute if e.g. the event store table is already
present.

If you want to follow the progress or leave comment, have a look at the
GitHub pull request [#204](https://github.com/rasmus/EventFlow/pull/204) or
leave a comment here.
