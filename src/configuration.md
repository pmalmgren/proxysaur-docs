# Configuration

Proxysaur uses a [toml configuration file](https://github.com/pmalmgren/proxysaur/blob/6159f78651b4c7b545a16e82bd1e35567b403339/config/src/tests/config.toml) to store configuration settings.

`ca_path` stores a reference to the root certificate authority, which is needed if you want proxysaur to proxy and terminate TLS connections.

Proxysaur can have one or more proxy configurations. These tell the server which ports to listen on, what application protocol to expect on those connections, and how to handle those connections.

## Initialization

To generate a configuration file, use the command:

```bash
$ proxysaur init [optional-path]
```

It will create a `proxysaur.toml` file in the current directory. Alternatively, you can pass in a path.

You can then launch the proxy server with the command:

```bash
$ proxysaur
```

Or, if you specified a configuration file path:

```bash
$ proxysaur --config-path /path/to/config
```

## Certificate Authority Configuration

Proxysaur comes with a root certificate authority generator. This is necessary if you want to configure it as a man-in-the-middle forwarding proxy for HTTPS.

```bash
$ proxysaur generate-ca
```

By default, this will place the certificate authority in the XDG data directory on Linux, and the application support directory on macOS.

## Proxy Configuration

To add a proxy configuration, use the subcommand `add-proxy`. This example will create a proxy which will listen on port 9000 and forward the requests to www.google.com:

```bash
$ proxysaur add-proxy
Enter host: localhost
Enter port: 9000
Enter protocol [http|httpforward|tcp]: http
Use tls [true/false]: false
Upstream address: www.google.com
Upstream port: 443
Use custom wasi [true/false]? false
```

### Enter host/Enter port

This prompt is the address and port that proxysaur will bind to when listening for incoming connections.

### Enter protocols

Currently proxysaur supports three different protocols:

- `http`, a reverse proxy protocol which simply forwards along requests to an upstream server
- `httpforward`, which allows proxysaur to be configured as a system-wide http proxy
- `tcp`, a reverse tcp proxy which forwards along tcp requests to an upstream server

### Use tls

If `true`, the server will terminate connections using TLS and the configured certificate authority.

### Upstream address/port

Where to forward the requests.

These configuration settings aren't applicable to the `httpforward` protocol.

### Use custom wasi

If set to `true`, you will be asked to enter the path to a custom WASI module that handles requests for the protocol you specified.