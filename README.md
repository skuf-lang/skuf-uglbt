# Ugly Boot (uglbt)

A minimal, opinionated HTTP framework for [Skuf](https://github.com/skuf-lang/skuf). Build APIs and web services with zero boilerplate.

## Install

Add uglbt to your project with norm:

```bash
norm add uglbt 0.3.0
```

Or add it manually to your `.norm` file:

```
need uglbt 0.3.0
```

Then install:

```bash
norm install
```

## Quick Start

```
mod main

use uglbt.http, log

fn main:
  app := http.app()

  http.get(app, "/", |_|:
    http.text(200, "hello uglbt")
  )

  http.get(app, "/health", |_|:
    http.json(200, "{\"status\":\"ok\"}")
  )

  http.get(app, "/users/:id", |r|:
    id := http.param(r, "id")
    http.json(200, "{\"id\":\"${id}\"}")
  )

  http.post(app, "/echo", |r|:
    http.text(200, r.body)
  )

  log.info("starting", "server")
  http.listen(app, 8080)
```

Run it:

```bash
skuf run src/main.skf
```

Test it:

```bash
curl localhost:8080/
curl localhost:8080/health
curl localhost:8080/users/42
curl -X POST -d "ping" localhost:8080/echo
```

## API

### HTTP Server

| Function | Description |
|---|---|
| `http.app() -> int` | Create a new application instance |
| `http.get(app, path, handler)` | Register a GET route |
| `http.post(app, path, handler)` | Register a POST route |
| `http.put(app, path, handler)` | Register a PUT route |
| `http.delete(app, path, handler)` | Register a DELETE route |
| `http.listen(app, port)` | Start the server (blocking) |
| `http.stop(app)` | Stop the server gracefully |

### Responses

| Function | Description |
|---|---|
| `http.text(status, body)` | Plain text response |
| `http.json(status, data)` | JSON response |
| `http.redirect(url)` | 302 redirect |
| `http.error(status, msg)` | JSON error response |

### Request Helpers

| Function | Description |
|---|---|
| `http.param(r, key)` | Extract a named route parameter (e.g. `:id`) |

### Routing

Routes support named parameters and wildcards:

```
http.get(app, "/users/:id", handler)      # :id extracted via http.param
http.get(app, "/files/*", handler)         # wildcard match
```

### Logging

uglbt uses the Skuf standard library `log` module:

```
use log

log.info("event", "data")
log.warn("event", "data")
log.error("event", "data")
log.debug("event", "data")
```

Outputs structured JSON to stderr:

```json
{"level":"info","event":"event","data":"data","ts":1234567890}
```

## .norm manifest

```
pkg myserver 0.1.0
entry src/main.skf

need uglbt 0.3.0
```

## Requirements

- [Skuf](https://github.com/skuf-lang/skuf) v0.0.15+
- A C compiler (`cc`, `gcc`, or `clang`)

## License

MIT
