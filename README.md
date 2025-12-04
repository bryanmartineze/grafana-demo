1. Create an EKS cluster using Rancher

2.Install the OTEL Demo app
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm repo update

helm upgrade --install otel-demo open-telemetry/opentelemetry-demo \
  -n ensemble-demo \
  -f - <<EOF
opentelemetry-collector:
  config:
    exporters:
      otlphttp/grafana:
        endpoint: "<GRAFANA_OTLP_ENDPOINT>"
        headers:
          # basic auth: user:api_key (the collector does this for you)
          # some charts let you set username/password separately instead of this header,
          # if so, use those fields instead.
          Authorization: "Basic $(echo -n '<GRAFANA_OTLP_USER>:<GRAFANA_OTLP_API_KEY>' | base64)"

    service:
      pipelines:
        traces:
          receivers: [otlp]
          processors: [batch]
          exporters: [otlphttp/grafana]
        metrics:
          receivers: [otlp]
          processors: [batch]
          exporters: [otlphttp/grafana]
        logs:
          receivers: [otlp]
          processors: [batch]
          exporters: [otlphttp/grafana]
EOF

3.-Create an ELB to expose the app to internet

kubectl patch svc frontend-proxy \
  -n ensemble-demo \
  -p '{"spec": {"type": "LoadBalancer"}}'

4.-Create the otel repo

helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm repo update

5.-Install the otel collector

helm upgrade --install otel-demo open-telemetry/opentelemetry-demo \
  -n ensemble-demo \
  -f otel-grafana-values.yaml


