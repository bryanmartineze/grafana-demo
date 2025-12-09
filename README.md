1.Create an EKS cluster using Rancher

2.Create a Grafana Cloud Stack, go into Connections > Add new connection > Kubernetes Monitoring

3.Set the correct parameters including metric Sources of Autodiscovery with annotations and Prometheus Operator objects and Generate a Token

4.Deploy the manifest to your kubernetes cluster

Optional.Install the OTEL Demo app just to see if it works

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

5.-Create the otel repo helm chart

    helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
    helm repo update

6.-Install the otel demo with the parameter of otel-grafana-values.yaml included in this repo, also modify the values with the correct parameters of <Grafana User> and <Grafana Token>

    helm upgrade --install otel-demo open-telemetry/opentelemetry-demo \
      -n ensemble-demo \
      -f otel-grafana-values.yaml


7.-Create an ELB to expose the app to internet

    kubectl patch svc frontend-proxy \
      -n ensemble-demo \
      -p '{"spec": {"type": "LoadBalancer"}}'

8. Check the ELB name inside AWS EC2 and enter the application using awselbdns:8080

9. Go to Grafana > Observability > Frontend > Start Observing

10. Put the "Application name": emsemble-frontend and in "Domains": http://foo.bar.com, click Next

11. in "Choose your package", choose CDN

12. Copy the "url" of the snippet and put it in index.html

13. Build the docker image and push it into your fav repository

14. Modify the ensemble-frontend.yaml to use the image you pushed and deploy it into your eks cluster

15. Modify the URL with the domain of the alb created by the deployment svc of ensemble-frontend

