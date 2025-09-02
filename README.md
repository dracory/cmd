# cmd <a href="https://gitpod.io/#https://github.com/dracory/cmd" style="float:right:"><img src="https://gitpod.io/button/open-in-gitpod.svg" alt="Open in Gitpod" loading="lazy"></a>

[![Tests Status](https://github.com/dracory/cmd/actions/workflows/tests.yml/badge.svg?branch=main)](https://github.com/dracory/cmd/actions/workflows/tests.yml)
[![Go Report Card](https://goreportcard.com/badge/github.com/dracory/cmd)](https://goreportcard.com/report/github.com/dracory/cmd)
[![PkgGoDev](https://pkg.go.dev/badge/github.com/dracory/cmd)](https://pkg.go.dev/github.com/dracory/cmd)

Small Go helpers for executing system commands and parsing CLI args.

## License

This project is dual-licensed under the following terms:

- For non-commercial use, you may choose either the GNU Affero General Public License v3.0 (AGPLv3) _or_ a separate commercial license (see below). You can find a copy of the AGPLv3 at: https://www.gnu.org/licenses/agpl-3.0.txt

- For commercial use, a separate commercial license is required. Commercial licenses are available for various use cases. Please contact me via my [contact page](https://lesichkov.co.uk/contact) to obtain a commercial license.

## Installation

```bash
go get github.com/dracory/cmd@latest
```

## API

- `Exec(name string, args ...string) (out string, err error)`
- `ExecSeparated(name string, args ...string) (stdout, stderr string, err error)`
- `ExecLine(cmd string) (out string, err error)`
- `ExecLineSeparated(cmd string) (stdout, stderr string, err error)`
- `ArgsToMap(args []string) map[string]string`

All functions live in package `cmd`.

## Usage

### Exec

```go
package main

import (
    "fmt"
    "github.com/dracory/cmd"
)

func main() {
    out, err := cmd.Exec("echo", "hello")
    fmt.Printf("out=%q err=%v\n", out, err)
}
```

### ExecSeparated

```go
stdout, stderr, err := cmd.ExecSeparated("sh", "-c", "echo ok && echo err >&2")
fmt.Println("stdout:", stdout)
fmt.Println("stderr:", stderr)
fmt.Println("err:", err)
```

On Windows, use `powershell -Command`:

```go
stdout, stderr, err := cmd.ExecSeparated("powershell", "-Command", "Write-Output ok; Write-Error err")
```

### ExecLine

```go
out, err := cmd.ExecLine("echo hello")
```

### ExecLineSeparated

```go
stdout, stderr, err := cmd.ExecLineSeparated("sh -c 'echo ok && echo err 1>&2'")
```

### ArgsToMap

Converts `--key=value` and `--flag value` into a map. Unfilled flags map to empty string.

```go
m := cmd.ArgsToMap([]string{"--user=alice", "--force", "--count", "3"})
// m => map[string]string{"user":"alice", "force":"", "count":"3"}
```

## Notes & limitations

- `ExecLine`/`ExecLineSeparated` tokenize using `strings.Fields` (whitespace collapsed). No shell parsing is performed:
  - Quotes/escaping/grouping are NOT supported.
  - Use `Exec`/`ExecSeparated` with explicit args for non-trivial commands, or invoke a shell explicitly (e.g., `sh -c` or `powershell -Command`).
- Commands run with the current process environment and working directory. No timeout is enforced.

## Testing

Run all tests:

```bash
go test ./...
```

## License

AGPL-3.0-or-later (see `LICENSE`).
