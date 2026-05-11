# A2Racer4WidescreenPatcher source

This ZIP contains the Go source code for `A2Racer4WidescreenPatcher.exe`.

## Build on Windows

Install Go, then run:

```cmd
go build -trimpath -ldflags="-s -w" -o A2Racer4WidescreenPatcher.exe A2Racer4WidescreenPatcher.go
```

## Cross-compile from Linux

```bash
GOOS=windows GOARCH=amd64 CGO_ENABLED=0 go build -trimpath -ldflags="-s -w" -o A2Racer4WidescreenPatcher.exe A2Racer4WidescreenPatcher.go
```
## Compile a native Linux version
```bash
GOOS=linux GOARCH=amd64 CGO_ENABLED=0 go build -o A2Racer4WidescreenPatcher A2Racer4WidescreenPatcher.go
```

## Usage

Place `A2Racer4WidescreenPatcher` in the A2 Racer 4 game folder next to the original `spel.dat`, run it, and enter a resolution such as:

```text
2560x1440
```

The patcher creates a `4x3_backup` folder, moves the original `spel.dat` into it, and writes a new widescreen-patched `spel.dat` in the game folder.
