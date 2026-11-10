# CoreWeave interview practice kit

Files:

- `PREP_PLAN.md` — research findings on the role, the loop, the Round-1 Go task, later rounds, and a 7-day plan. **Start here.**
- `TASKS.md` — six reconstructed interview tasks with the follow-up questions interviewers ask.
- `READING.md` — the pkg.go.dev skim list CoreWeave's guide asks for: exact pages, symbols and examples to read per package, what to skip, about 3 hours total.
- `interviews/` — **ten 60-minute mock sessions shaped like the real screen**: a provided server to read and run, a `TASK.md` with three stages, an empty `main.go` for your attempt, a hidden junior-style `solution.go.txt`, follow-up questions. Own Go 1.18 module, so `cd interviews` first. Start with i01 and i04.
- `cheatsheet/cheatsheet.pdf` — **printable 4-page cheat sheet** (Go 1.26): 12 snippet blocks + a one-page 60-minute game plan (status-code table, phrases, gotchas, commands). Printed code is pulled from `snippets.go`/`snippets_test.go`, which compile and pass `go test -race`; edit those, then `python3 -I cheatsheet/build.py`.
- `cheatsheet_simple/cheatsheet.pdf` — same 4-page sheet **without `context`**: `client.Get` / `http.NewRequest`, `time.Sleep` backoff, stop-on-fatal via a shared `atomic.Bool`. Matches how you write Go today; `cheatsheet/` is the context version to review.
- `calibrd/` — **seven sessions built from the calibrd.com CoreWeave guide** (per-pool 10% reboot budget, health→offline rerun-safe, 500 IDs / 20 in flight / stop on fatal, resumable migration, query server, design, behavioural), new `fleetapi` server on :9090 with a `/debug/stats` scoreboard. **Go 1.26 module** (the pad runs go1.26.0, not 1.18). See `calibrd/README.md`.
- `drills_junior/` — 20 simpler drills at a junior Go / DevOps level: `main.go` holds only the task and your attempt, the reference is `solution.go.txt` (never compiled, so no accidental peeking). Start here if `drills/` feels too senior. Index and workflow in `drills_junior/README.md`.
- `drills/` — 20 drill solutions (your 10 plus 10 more), one runnable `main.go` each with TASK / RUN / KNOW in the header, for reading once and retyping from memory. Index and protocol in `drills/README.md`.
- `DRILLS.md` — ten time-boxed syntax drills (10–20 min each) that rebuild the interview task piece by piece against `mockfleet`; do these before the timed run.
- `CHEATSHEET.md` — the exact Go snippets you need in the pad (flags, HTTP client, JSON, error classification, retry, bounded concurrency, worker pool, context, tests, tiny server, gotchas). Every block compiles on Go 1.18. Read once, then retype from memory.
- `cmd/mockfleet_simple` — the smallest version of the same API (one file, no auth, no randomness, optional `-fail-every N`). Start here for drills 3–5, and read it in 2 minutes as practice for "understand the provided server".
- `cmd/mockfleet` — chaos-enabled mock "fleet API" to practice against (500s, slow responses, truncated JSON, 429s, expiring tokens, reboots that never finish).
- `parts/part1`, `parts/part2`, `parts/part3` — what your `main.go` should look like at the end of each stage of the task (sequential → failures → concurrency), with `parts/README.md` listing the "done" criteria per stage. Diff them to see what each stage adds.
- `parts_simple/part1..3` — the same three stages in their simplest form (constants instead of flags, args only, plain `Printf`, `Client.Timeout`): what a clean first pass looks like in 15 minutes per stage. See `parts_simple/README.md` for what was dropped and why.
- `cmd/fleetctl` — stdlib-only reference solution for the canonical task, with `httptest`-based tests. Attempt the task yourself under a 60-minute timer **before** reading it.
- `examples/errgroup` — the same bounded fan-out using `golang.org/x/sync/errgroup` (third-party packages are allowed per CoreWeave's official guide).

Quick start:

```bash
cd ~/coreweave-prep
go test ./...                                   # reference solution tests
go run ./cmd/mockfleet                          # terminal 1: chaos on
go run ./cmd/mockfleet -chaos=false             #   ...or a clean server while you build Part 1

# terminal 2
curl -s -H 'Authorization: Bearer demo-token' localhost:8080/machines/CW-0007 | jq
curl -s -H 'Authorization: Bearer demo-token' 'localhost:8080/machines?status=unreachable&page_size=5' | jq
curl -s -X POST -H 'Authorization: Bearer demo-token' -H 'Idempotency-Key: abc' localhost:8080/machines/CW-0007/reboot | jq

# reference solution
go run ./cmd/fleetctl -action lookup CW-0001 CW-0002 CW-9999
seq -f 'CW-%04g' 1 60 | go run ./cmd/fleetctl -action reboot -concurrency 8 -f -
go run ./cmd/fleetctl -action offline -client-id demo -client-secret secret -o json CW-0003
```

Practice loop: start `mockfleet`, open an empty `main.go` in a plain editor (CoderPad has no IDE help), set a 60-minute timer, build Parts 1→2→3 from `TASKS.md` task 1 while talking out loud, then diff against `cmd/fleetctl`.

## CoderPad notes

CoreWeave runs the screen in CoderPad (five independent reports). CoderPad's public Go page advertises **Go 1.18 with no IntelliSense**, so `cmd/fleetctl` is written against 1.18 (no `min`/`max`, no `math/rand/v2`, no mux method patterns, no typed atomics, loop variables passed into goroutines explicitly). `cmd/mockfleet` uses newer features because it only runs on your machine.

Before the interview, open the free sandbox at https://app.coderpad.io/sandbox, pick Go, run `go version` in the shell, and read the language Info panel for the exact version and packages. See `PREP_PLAN.md` section 3.4a.
# inter
