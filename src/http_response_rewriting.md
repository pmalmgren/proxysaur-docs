# Rewriting HTTP Responses

You can configure proxysaur to overwrite the HTTP headers, body, and status code of a response. The header and body rewrites are the same as for [HTTP requests](./http_rewriting.md).

## Status code rewrites

This example rewrites the status code from 200 to 500 for `https://test.com/`:

```yaml
hosts:
  test.com:
    scheme: https
    response_rewrites:
      - when:
          - path:
              exact: /
        rewrite:
          status:
            exact: "200"
          new_status: "500"
```