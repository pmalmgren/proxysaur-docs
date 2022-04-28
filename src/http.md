# HTTP Proxying

Proxysaur supports two modes for proxying HTTP requests: HTTP forwarding mode, and reverse proxy mode.

HTTP forwarding mode allows proxysaur to be used as a system-wide proxy.

Reverse proxy mode allows proxysaur to terminate HTTP(S) traffic for a single host.

## HTTP(S) Forward Proxy Configuration

Proxysaur ships with a forward proxy WASI module which is highly configurable, and allows the following things:

- Request rewriting and redirecting
- Serving responses directly from the filesystem
- Response rewriting and recording

Unless specified otherwise, this module is activated by default when the `httpforward` protocol is specified.

The remaining documentation will cover how to use `httpforward` mode to observe and manipulate HTTP traffic.