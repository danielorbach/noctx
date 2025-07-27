# noctx

![](https://github.com/sonatard/noctx/workflows/CI/badge.svg)

`noctx` finds function calls without context.Context.

Passing `context.Context` enables library user to cancel request, getting trace information and so on.

`noctx` helps you to identify code that could be rewritten to use the context.Context.

## Usage

### noctx with go vet

go vet is a Go standard tool for analyzing source code.

1. Install noctx.
```sh
$ go install github.com/sonatard/noctx/cmd/noctx@latest
```

2. Execute noctx
```sh
$ go vet -vettool=`which noctx` main.go
./main.go:6:11: net/http.Get must not be called
```

### noctx with golangci-lint

golangci-lint is a fast Go linters runner.

1. Install golangci-lint.
[golangci-lint - Install](https://golangci-lint.run/usage/install/)

2. Setup .golangci.yml
```yaml:
# Add noctx to enable linters.
linters:
  enable:
    - noctx

# Or enable-all is true.
linters:
  default: all
  disable:
   - xxx # Add unused linter to disable linters.
```

3. Execute noctx
```sh
# Use .golangci.yml
$ golangci-lint run

# Only execute noctx
golangci-lint run --enable-only noctx
```

## Configuration

### Excluding Functions

You can exclude specific functions from noctx checks using the `exclude` flag. This is useful when you want to allow certain functions without context, such as when using the `log/slog` package where context variants are optional.

#### With go vet

```sh
$ go vet -vettool=`which noctx` -noctx.exclude="log/slog.*,(*log/slog.Logger).*" ./...
```

#### With golangci-lint

In your `.golangci.yml`:

```yaml
linters:
  enable:
    - noctx

linters-settings:
  noctx:
    # Exclude specific functions using glob patterns (* wildcards)
    exclude:
      # Exclude all log/slog package functions
      - "log/slog.*"
      # Exclude all methods on log/slog.Logger
      - "(*log/slog.Logger).*"
      # Exclude specific functions
      - "log/slog.Debug"
      - "log/slog.Info"
```

The exclusion patterns support standard glob patterns with `*` wildcards.

## net/http package
### Rules
https://github.com/sonatard/noctx/blob/b768dab1764733f7f69c5075b7497eff4c58f260/noctx.go#L41-L50

### Sample
https://github.com/sonatard/noctx/blob/b768dab1764733f7f69c5075b7497eff4c58f260/testdata/src/http_client/http_client.go#L11
https://github.com/sonatard/noctx/blob/b768dab1764733f7f69c5075b7497eff4c58f260/testdata/src/http_request/http_request.go#L17

### Reference
- [net/http - NewRequest](https://pkg.go.dev/net/http#NewRequest)
- [net/http - NewRequestWithContext](https://pkg.go.dev/net/http#NewRequestWithContext)
- [net/http - Request.WithContext](https://pkg.go.dev/net/http#Request.WithContext)

## net package

### Rules
https://github.com/sonatard/noctx/blob/b768dab1764733f7f69c5075b7497eff4c58f260/noctx.go#L26-L39

### Sample
https://github.com/sonatard/noctx/blob/b768dab1764733f7f69c5075b7497eff4c58f260/testdata/src/network/net.go#L17

### References
- [net - ListenConfig](https://pkg.go.dev/net#ListenConfig)
- [net - Dialer.DialContext](https://pkg.go.dev/net#Dialer.DialContext)
- [net - Resolver](https://pkg.go.dev/net#Resolver)
- [net - DefaultResolver](https://pkg.go.dev/net#DefaultResolver)

## database/sql package
### Rules
https://github.com/sonatard/noctx/blob/b768dab1764733f7f69c5075b7497eff4c58f260/noctx.go#L52-L66

### Sample
https://github.com/sonatard/noctx/blob/b768dab1764733f7f69c5075b7497eff4c58f260/testdata/src/sql/sql.go#L18

### Reference
- [database/sql](https://pkg.go.dev/database/sql)

## crypt/tls package
### Rules
https://github.com/sonatard/noctx/blob/b768dab1764733f7f69c5075b7497eff4c58f260/noctx.go#L71-L74

### Sample
https://github.com/sonatard/noctx/blob/b768dab1764733f7f69c5075b7497eff4c58f260/testdata/src/crypto_tls/tls.go#L17

### Reference
- [crypto/tls - Dialer.DialContext](https://pkg.go.dev/crypto/tls#Dialer.DialContext)
- [crypto/tls - Conn.HandshakeContext](https://pkg.go.dev/crypto/tls#Conn.HandshakeContext)

## exec package
### Rules
https://github.com/sonatard/noctx/blob/b768dab1764733f7f69c5075b7497eff4c58f260/noctx.go#L68-L69

### Sample
https://github.com/sonatard/noctx/blob/b768dab1764733f7f69c5075b7497eff4c58f260/testdata/src/exec_cmd/exec.go#L11

### Reference
- [exec - exec.CommandContext](https://pkg.go.dev/exec#CommandContext)

## log/slog package

### Rules
https://github.com/sonatard/noctx/blob/b768dab1764733f7f69c5075b7497eff4c58f260/noctx.go#L76-L84

### Sample
https://github.com/sonatard/noctx/blob/b768dab1764733f7f69c5075b7497eff4c58f260/testdata/src/slog/slog.go#L12-L15

### Reference
- [slog - Using contexts](https://pkg.go.dev/log/slog#hdr-Contexts)

