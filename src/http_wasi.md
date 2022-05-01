# Customization with WASI

Compiled WASM modules, specifically compiled with [WASI](https://wasi.dev/), can be used to extend proxysaur. Currently, bindings exist only for Rust. The API exposed by these bindings is not stable and subject to change.

A good reference point is the `http-forward-proxy`, which is the proxy that is documented for intercepting and rewriting requests via the YAML configuration file.

## Writing a Rust proxysaur module

### Cargo.toml

Add the following dependency:

```toml
proxysaur-bindings = { git = "https://github.com/pmalmgren/proxysaur" }
```

### Configuration

The configuration parameter in the `toml` file called `proxy_configuration_path` will be read in and the raw bytes are accessible with the `config` bindings:

```rust
use proxysaur_bindings::config;

fn main() {
    let config_data: Vec<u8> = proxysaur_config::get_config_data();
    ...
}
```

If the data is invalid, you can use the `set_invalid_data` function which allows you to set a `String` error message:

```rust
use proxysaur_bindings::config;

fn main() {
    let config_data: Vec<u8> = proxysaur_config::get_config_data();
    let config: Config = serde::from_bytes(&config_data).map_err(|err| {
        let msg = format!("Error serializing data: {}", err);
        config::set_invalid_data(msg);
        err
    }).unwrap();
}
```

### HTTP pre-requests

Before proxysaur proxies a request, it checks to see if it should intercept the request or just forward the data between the client and the server. It does this by running the module, and checking to see what the module set the proxy mode to. A value of `Intercept` means that it will proceed to call methods to manipulate the request and the response.

For example, this WASI module will proxy requests only to `localhost:9234`:

```rust
use proxysaur_bindings::http::pre_request::{self, HttpPreRequest, ProxyMode};

fn main() {
    let request: HttpPreRequest = pre_request::http_request_get();

    if request.authority == "localhost:9234" {
        pre_request::http_set_proxy_mode(ProxyMode::Intercept);
    }
}
```

### HTTP Requests

After proxysaur received a request from the client, it runs a WASI module and checks the data set by the module. In the request module, you can set anything you want on the request. For example, this one changes the request body to some random data for `test.com`:

```rust
use proxysaur_bindings::http::request::{self, HttpRequest};

fn main() {
    let request: HttpRequest = request::http_request_get().expect("should get the request");

    if request.host == "test.com" {
        request::http_request_set_body("haha!".as_bytes()).expect("should set the body");
    }
}
```

### HTTP Responses

After proxysaur receives a response from the server, it runs a WASI module to check the response data set by the module. 

```rust
use proxysaur_bindings::http::response::{self, HttpResponse};

fn main() {
    let response: HttpResponse = response::http_response_get().expect("should get the response");

    if response.status == 200 {
        response::http_response_set_status(500).expect("should set the status");
        response::http_response_set_body("broken!".as_bytes()).expect("should set the body");
    }
}
```