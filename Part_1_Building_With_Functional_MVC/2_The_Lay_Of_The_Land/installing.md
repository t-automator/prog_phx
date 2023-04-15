#### Postgres Configuration

Some frameworks (like Django) will default to using an sqlite backend for barebones, initial scaffolding.  Phoenix goes straight for Postgres by default.

The tl;dr on this is that newer versions of Postgres don't have a post

There might be a version mismatch but in any event, here's what happens if you follow the instructions:

```
hello$ mix ecto.create; mix phx.server
Compiling 15 files (.ex)
Generated hello app

06:58:21.203 [error] Postgrex.Protocol (#PID<0.429.0>) failed to connect: ** (Postgrex.Error) FATAL 28P01 (invalid_password) password authentication failed for user "postgres"
[info] Running HelloWeb.Endpoint with cowboy 2.9.0 at 127.0.0.1:4000 (http)
[info] Access HelloWeb.Endpoint at http://localhost:4000
[watch] build finished, watching for changes...
```

Uh oh, looks like we have a bad user/pass combination.

In the project directory, we find two files that contain the Postgres user/password: 
```
dev.exs:  username: "postgres",
dev.exs:  password: "postgres",
test.exs:  username: "postgres",
test.exs:  password: "postgres",
```

...So let's change dev.exs to something else (I'll create a `t_automator` user for this), and add the appropriate creds to Postgres, as follows

```
postgres=# create user t_automator with password 't_automator';
CREATE ROLE
postgres=# alter user t_automator with login;
ALTER ROLE
```

Relaunching mix phx.server gives us another error--we don't have a hello_dev database!

```
[error] Postgrex.Protocol (#PID<0.563.0>) failed to connect: ** (Postgrex.Error) FATAL 3D000 (invalid_catalog_name) database "hello_dev" does not exist
```

So let's add it

`create database hello_dev;`

And now we should be in business!

```
hello$ mix phx.server
[info] Running HelloWeb.Endpoint with cowboy 2.9.0 at 127.0.0.1:4000 (http)
[info] Access HelloWeb.Endpoint at http://localhost:4000
[watch] build finished, watching for changes...
```
