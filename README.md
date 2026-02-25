# We configure a better proxy and OTEL collector
## Arize Phoenix
**Install Phoenix for LLM observability**
```sh
# Install Phoenix using PostgreSQL storage.
helm install phoenix oci://registry-1.docker.io/arizephoenix/phoenix-helm \
  --namespace envoy-ai-gateway-system \
  --create-namespace \
  --set auth.enableAuth=false \
  --set server.port=6006
  ```

## Envoy AI Gateway

**Install AI Gateway CRDs**
```bash
helm upgrade -i aieg-crd oci://docker.io/envoyproxy/ai-gateway-crds-helm \
  --version v0.4.0 \
  --namespace envoy-ai-gateway-system \
  --create-namespace
```

**Configure AI Gateway with OpenTelemetry**
```sh
helm install aieg oci://docker.io/envoyproxy/ai-gateway-helm \
  --version v0.4.0 \
  --namespace envoy-ai-gateway-system \
  --set "extProc.extraEnvVars[0].name=OTEL_EXPORTER_OTLP_ENDPOINT" \
  --set "extProc.extraEnvVars[0].value=http://phoenix-svc.envoy-ai-gateway-system.svc.cluster.local:6006" \
  --set "extProc.extraEnvVars[1].name=OTEL_METRICS_EXPORTER" \
  --set "extProc.extraEnvVars[1].value=none"
# OTEL_SERVICE_NAME defaults to "ai-gateway" if not set
# OTEL_METRICS_EXPORTER=none because Phoenix only supports traces, not metrics
```

**Install Envoy Gateway**
```bash
helm install eg oci://docker.io/envoyproxy/gateway-helm \
    --version v1.6.0 \
    --namespace envoy-gateway-system \
    --create-namespace \
    -f https://raw.githubusercontent.com/envoyproxy/ai-gateway/v0.4.0/manifests/envoy-gateway-values.yaml

kubectl wait --timeout=2m -n envoy-gateway-system deployment/envoy-gateway --for=condition=Available
```

Run envoy instance :
```bash
kubectl apply -f deploy-proxy-container.yaml
```

```bash
kubectl apply -f models-providers.yaml
```