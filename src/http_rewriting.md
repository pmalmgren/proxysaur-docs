# Rewriting HTTP Requests

You can configure proxysaur to overwrite the HTTP headers and body of a request.

## Rewriting HTTP headers

Header rewrites have two parts: a match on the name or the value, and the new header name and/or value. Parts of the matched headers can be used in the old. For exact and contains matches, `$0` will expand to the entire matched value. So for example, this configuration will rewrite the `access-control-allow-origin` header to reuse the same name, but with the new value `*`.

```yaml
hosts:
  test.com:
    scheme: https
    request_rewrites:
      - when:
          - path:
              exact: /
        rewrite:
          match:
            header_name:
              exact: access-control-allow-origin
            header_value:
              # a blank value means anything will be matched
              contains: ""
          new_header_name: $0
          new_header_value: "*"
```

Regular expressions can be used to do more complex rewrites with capture groups, which can be named or anonymous. In the below example we'll capture the bearer token and convert it to a basic token:

```yaml
hosts:
  test.com:
    scheme: https
    request_rewrites:
      - when:
          - path:
              exact: /
        rewrite:
          match:
            header_name:
              exact: authorization
            header_value:
              regex: Bearer (?P<token>[0-9A-Za-z]+)
          new_header_name: $0
          new_header_value: "Basic $token"
```

## Rewriting the HTTP request body

There isn't any matching involved in an HTTP request body rewrite. The only thing you can do is overwrite the entire request body.

```yaml
hosts:
  html.duckduckgo.com:
    scheme: https
    request_rewrites:
      - when:
          - path:
              contains: /api
        rewrite:
          replace_with: |
            { "payload": "a new body" }
```

The proxy will calculate the size of the new body and set the `Content-Length` header to the appropriate size for you.