# `github.com/StevenACoffman/pgxtest`

[![Reference](https://pkg.go.dev/badge/github.com/StevenACoffman/pgxtest?tab=doc)](https://pkg.go.dev/github.com/StevenACoffman/pgxtest?tab=doc)
[![Contributor Covenant](https://img.shields.io/badge/Contributor%20Covenant-v2.0%20adopted-ff69b4.svg)](CODE_OF_CONDUCT.md)

### NOTE: This is a fork of https://github.com/zombiezen/postgrestest to use https://github.com/jackc/pgx

The readme below has not been updated to reflect recent code changes. PRs welcome!

TODO(steve): I would further like to make the changes in https://github.com/gokrazy/gus/commit/b97c652fd03754ba817bd3c13f18ea6e2e154ef4#diff-7829468e86c1cc5d5133195b5cb48e1ff6c75e3e9203777f6b2e379d9e4882b3
from https://michael.stapelberg.ch/posts/2024-11-19-testing-with-go-and-postgresql-ephemeral-dbs/

Package `pgxtest` provides a test harness that starts an ephemeral
[PostgreSQL][] server. It is tested on macOS, Linux, and Windows. It can cut
down the overhead of PostgreSQL in tests up to 90% compared to spinning up a
`postgres` Docker container: starting a server with this package takes
roughly 650 milliseconds and creating a database takes roughly 20 milliseconds.

[PostgreSQL]: https://www.postgresql.org/

## Example

```go
func TestApp(t *testing.T) {
	// Start up the PostgreSQL server. This can take a few seconds, so better to
	// do it once per test run.
	ctx := context.Background()
	srv, err := postgrestest.Start(ctx)
	if err != nil {
		t.Fatal(err)
	}
	t.Cleanup(srv.Cleanup)

	// Each of your subtests can have their own database:
	t.Run("Test1", func(t *testing.T) {
		db, err := srv.NewDatabase(ctx)
		if err != nil {
			t.Fatal(err)
		}
		if _, err := db.Exec(`CREATE TABLE foo (id SERIAL PRIMARY KEY);`); err != nil {
			t.Fatal(err)
		}
		// ...
	})

	t.Run("Test2", func(t *testing.T) {
		db, err := srv.NewDatabase(ctx)
		if err != nil {
			t.Fatal(err)
		}
		if _, err := db.Exec(`CREATE TABLE foo (id SERIAL PRIMARY KEY);`); err != nil {
			t.Fatal(err)
		}
		// ...
	})
}
```

## Installation

PostgreSQL must be installed locally for this package to work. See the
[PostgreSQL Downloads page][] for instructions on how to obtain PostgreSQL for
your operating system.

To install the package:

```
go get github.com/StevenACoffman/pgxtest
```

[PostgreSQL Downloads page]: https://www.postgresql.org/download/

## License

[Apache 2.0](LICENSE)
