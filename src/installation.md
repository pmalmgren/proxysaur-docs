# Installation

## Releases

There are prebuilt releases for macOS and Linux available on the [releases page](https://github.com/proxysaur/proxysaur/releases).

Keep in mind you will need a working installation of [OpenSSL 1.1.1](https://www.openssl.org/source/) which can be installed via [homebrew](https://formulae.brew.sh/formula/openssl@1.1) or on Debian-based Linux distributions with `apt-get install`.

## Building from source

Alternatively, you can build proxysaur from source.

You'll need to have [Rust installed](https://www.rust-lang.org/tools/install), and then you can run:

```bash
$ git clone https://github.com/proxysaur/proxysaur.git
$ cd proxysaur
$ cargo build --release
$ cp target/release/proxysaur /usr/local/bin # or somewhere in your PATH
```