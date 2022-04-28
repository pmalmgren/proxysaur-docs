# Configuration

To setup a forwarding HTTP proxy, use the proxysaur command `add-proxy`:

```bash
$ proxysaur add-proxy
Enter host: localhost
Enter port: 9000
Enter protocol [http|httpforward|tcp]: httpforward
Use tls [true/false]: false
Use custom wasi [true/false]? false
```

This will generate the following configuration in `proxysaur.toml`:

```toml
[[proxy]]
port = 9000
protocol = 'httpforward'
tls = false
address = 'localhost'
upstream_address = ''
upstream_port = 9999
```

To check that this works, start proxysaur and then run the following command:

```bash
$ curl -x "http://localhost:9000" "http://neverssl.com"
...
```

You should see requests and responses in the proxysaur logs:

```bash
$ proxysaur
INFO protocols::http::proxy: Received request req=Request { ... }
INFO protocols::http::proxy: New request. new_request=Request {...}
INFO protocols::http::proxy: New response. new_response=Response { ...}
INFO protocols::http::proxy: Finished tunneling. 
INFO protocols::http::proxy: HTTP proxy result. 
```