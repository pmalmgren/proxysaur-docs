# Configuration

If you haven't already, run `proxysaur init` to create a new `proxysaur.toml` file in the current directory.

Make sure that your [certificate authority](/ca.md) is initialized and created.

## Add a proxy with HTTP forward protocol

To setup a forwarding HTTP proxy, use the proxysaur command `add-proxy`:

```bash
$ proxysaur add-proxy
Enter host: localhost
Enter port: 9000
Enter protocol [http|httpforward|tcp]: httpforward
Use tls [true/false]: false
Use custom wasi [true/false]? false
Enter proxy configuration path: proxy.yaml
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

## Configure the forward proxy

To intercept and rewrite requests, we need to add a proxy configuration.

Create a file called `proxy.yaml` and add the following:

```yaml
hosts:
  google.com:
    scheme: https
    response_rewrites:
      - when:
          - path:
              exact: /
        rewrite:
          replace_with: |
            <!DOCTYPE html>
            <html>
              <body>
                <h1>Proxysaur has eaten your website.</h1>
              </body>
            </html>
```

Now start up proxysaur, and in a separate terminal run:

```bash
$ curl -x "http://localhost:9000" "https://google.com/"
<!DOCTYPE html>
<html>
  <body>
    <h1>Proxysaur has eaten your website.</h1>
  </body>
</html>% 
```

## Configuration format

The configuration file has a top-level array of hosts, each of which has its own configuration block with the following properties:

- `scheme` - can either be `http` or `https`
- `response_rewrites` - an array of rewrites which can change the HTTP response, covered in [HTTP response rewriting](./http_response_rewriting.md)
- `request_rewrites` - an array of rewrites which can change the HTTP request, covered in [HTTP request rewriting](./http_rewriting.md)
- `redirect` - a rule which can redirect requests to different hosts, covered in [HTTP request redirection](./http_redirection.md)

Each rewrite or redirect has a `when` clause, which can match on either a header or the path. The match value for the path can be an exact match, a contains match which will match when the provided substring is found in the path, or a regular expression.

### Exact match example

```yaml
hosts:
  html.duckduckgo.com:
    scheme: https
    request_rewrites:
      - when:
          - path:
              exact: /path
        rewrite:
          match:
            header_name:
              exact: origin
            header_value:
              contains: ""
          new_header_name: $0
          new_header_value: "duckduckgo.com" 
```

### Contains match example

```yaml
hosts:
  html.duckduckgo.com:
    scheme: https
    request_rewrites:
      - when:
          - path:
              contains: /api/v1
        rewrite:
          match:
            header_name:
              exact: origin
            header_value:
              contains: ""
          new_header_name: $0
          new_header_value: "duckduckgo.com" 
```

### Regular expression match example

```yaml
hosts:
  html.duckduckgo.com:
    scheme: https
    request_rewrites:
      - when:
          - path:
              regex: /api/v1/(.*)/books
        rewrite:
          match:
            header_name:
              exact: origin
            header_value:
              contains: ""
          new_header_name: $0
          new_header_value: "duckduckgo.com" 
```