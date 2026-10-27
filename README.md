# go-bash

A read-only bash subset over an `fs.FS`, in plain Go. It runs a model's `grep`, `jq`, `sed -n`, `head` and the like over data it is given, with no machine behind it: no process, no network, no write.

```go
res, err := bash.Run(ctx, `grep -n '"id": "A-17"' /results/call_1 | head -3`, fsys)
```

## What it is

- The parser and interpreter of [mvdan.cc/sh](https://github.com/mvdan/sh), with every handler taken over: commands, file opens, directory reads and the environment.
- A syntax-tree check first. Loops, functions, `$( )`, arithmetic, writing redirects and `&` are refused with a clear error.
- 14 commands written for GNU behavior: `ls cat head tail sed -n wc grep sort uniq cut tr base64 echo`, and `jq` on [gojq](https://github.com/itchyny/gojq).
- A supported command or flag behaves as GNU's does; anything else fails with a clear error. The same rule holds for bash syntax.
- The same input gives the same output on every machine. It always ends and honours `ctx`; its memory is bounded.

## Checks

- Golden cases for every command and flag.
- `TestAgainstGNU` compares with the installed GNU tools (and jq 1.7.1) when `GOBASH_GNU=1`; CI runs it on Ubuntu. On macOS install GNU tools with a prefix and set `GOBASH_GNU_PREFIX=g`.

## Origin

This was the `adapter/bash` module of [Gotato](https://github.com/jinhuang712/gotato) and was moved out with its history. The first commits also hold `bashtool`, the thin layer that wrapped the engine as an agent tool; it needed a hook Gotato no longer has.
