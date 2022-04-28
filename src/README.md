# Introduction

[proxysaur](https://github.com/proxysaur/proxysaur) is a network application debugging proxy. Currently it supports TCP, HTTP and HTTP(s). There are plans to extend it to other protocols, such as Postgres and Redis, but these aren't implemented yet.

You can use proxysaur to observe, record, replay, and rewrite network requests in real time. This is useful when you're doing web application development and need to see what requests are being performed. 

## Design Goals

Proxysaur is designed to be extensible with [WebAssembly](https://webassembly.org/). All of the core logic is written in Rust, but request and response handling are completely delegated to compiled [WASI modules](https://wasi.dev/).

Proxysaur is designed to be shareable. Once you have a proxy configuration which works, you can share the configuration and any compiled WASM modules with your teammates. You could use this, for example, to provide separate configurations for frontend and backend developers on your team. You could also use proxysaur as a mock network layer in a test suite.

## Design Anti-Goals

Proxysaur is not meant to be used in production, at least not in its current form.

Proxysaur is configurable through a CLI interface and a configuration file, and is not meant to be a full-fledged UI application like Fiddler or Charles Proxy. That doesn't mean that one can't be built on top of Proxysaur.

## Releases

Proxysaur is currently in the pre-alpha phase of development. The API, CLI, and configuration interfaces could change at anytime.

## Getting Help

If you have questions or feedback, the best way to get help is to [open an issue](https://github.com/proxysaur/proxysaur/issues).