# cmd

Small Go helpers for executing system commands and parsing CLI args.

Module: `github.com/dracory/cmd`

## Install

```bash
go get github.com/dracory/cmd@latest
```

Go version per `go.mod`: 1.24.5

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

- `ExecLine`/`ExecLineSeparated` split the command by spaces using `strings.Split`. Quoted arguments and escaping are not handled. For complex invocations, prefer `Exec`/`ExecSeparated` and pass args explicitly.
- Commands run with the current process environment and working directory. No timeout is enforced.

## Testing

Run all tests:

```bash
go test ./...
```

## License

AGPL-3.0-or-later (see `LICENSE`).
