# Setup Codex with openai compatible provider
## Install Codex CLI

## 

Exporting OPENAI_BASE_URL help to define our endpoints, but it's not enough because we want the authorization headers to be `APIKEY` instead of default `Bearer` expected by Codex Agent.

It sound good, but actually it's not because Cargo.toml is not loaded as we espected.

### Setup Proxy and OTEL server to ensure our headers are passed as we espect
* 
```bash
[otel]
exporter = { otlp-http = { endpoint = "http://localhost:4318/v1/logs" } }
log_user_prompt = true   
```



# Use it in a github action