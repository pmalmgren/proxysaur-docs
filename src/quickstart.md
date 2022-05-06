# Quick start

To support getting started quickly with HTTP debugging, `proxysaur` comes with a command to launch a debugging proxy:

```bash
$ proxysaur http
Using configuration file: "/home/me/.config/proxysaur/proxysaur.toml"
Using existing CA dir: "/home/me/.local/share/proxysaur"
Root Certificate: "/home/me/.local/share/proxysaur/myca.crt"
To trust this certificate, run:
sudo cp "/home/me/.local/share/proxysaur/myca.crt" /usr/local/share/ca-certificates/extra
sudo update-ca-certificates
To use in a browser, read more here: https://proxysaur.us/ca#trusting-the-root-certificate-in-your-browser
Proxy configuration path: "/home/me/.config/proxysaur/config.yml"
Visit this URL to make sure everything is working: https://proxysaur.us/test
Proxy HttpForward listening on address: http://localhost:9999
```

This will initialize a root certificate authority, initialize a configuration file, and start the HTTP proxy with a default configuration.

Copy and paste the steps to trust the root certificate, and then run the command:

```bash
$ curl -x "http://localhost:9999" "https://proxysaur.us/test"
<!DOCTYPE html>
<html>
  <body>
    <h1>Proxysaur has eaten your website.</h1>
    <p>If this works, it means your proxy is correctly configured. Congratulations!</p>
    <p>To get started, head over to the <a href="/http_configuration.html#configure-the-forward-proxy">configuration docs page.</a></p>
  </body>
</html>
```

Go ahead and edit the file defined in the "proxy configuration path," in this case "/home/me/.config/proxysaur/config.yml" to look like this:

```yaml
hosts:
  google.com:
    scheme: https
    response_rewrites:
      - when:
          - path:
              exact: /test
        rewrite:
          replace_with: |
            <!DOCTYPE html>
            <html>
              <body>
                <h1>Proxysaur has eaten your website.</h1>
              </body>
            </html>
  proxysaur.us:
    scheme: https
    response_rewrites:
      - when:
          - path:
              exact: /test
        rewrite:
          replace_with: |
            <!DOCTYPE html>
            <html>
              <body>
                <h1>Proxysaur has eaten your website.</h1>
                <p>If this works, it means your proxy is correctly configured. Congratulations!</p>
                <p>To get started, head over to the <a href="/http_configuration.html#configure-the-forward-proxy">configuration docs page.</a></p>
              </body>
            </html>
```

You should see the proxy live reload. Afterwards, run the command `curl -x "http://localhost:9999" "https://proxysaur.us/test"` to verify that your changes were picked up.

## Next Steps

- [Redirecting HTTP Requests](./http_redirection.md)
- [Rewriting HTTP Requests](./http_rewriting.md)
- [Rewriting HTTP Responses](./http_response_rewriting.md)