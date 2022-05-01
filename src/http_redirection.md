# Redirecting HTTP Requests

HTTP requests can be redirected to another URL with the following configuration block:

```yaml
hosts:
  google.com:
    scheme: https
    redirect:
        to:
          url:
            url: https://duckduckgo.com/about
            replace_path_and_query: false
        when:
          - path:
              exact: /about
```

The parameter `replace_path_and_query` will overwrite the new redirected URL. For example:

```yaml
hosts:
  google.com:
    scheme: https
    redirect:
        to:
          url:
            url: https://proxysaur.us/
            replace_path_and_query: true
        when:
          - path:
              regex: /.*
```

In the above configuration block, every request directed to google.com will be redirected to proxysaur.us, with the path component being completely overwritten.

## Redirecting file requests

*Note: This currently doesn't work, but is valid configuration*

You can also use proxysaur to serve static files with the file redirect block:

```yaml
hosts:
  test2.com:
    scheme: https
    redirect:
      to:
        file:
          path: /usr/local/www
          root_index: true
          replace_path: true
          file_suffix: .html
          content_type: text/html; charset=UTF-8
```

This will redirect requests from `https://test2.com/` to the local file system. Requests to `/` will be served from `/usr/local/www/index.html` etc. This can be useful when overwriting requests to specific paths with pre-recorded responses.