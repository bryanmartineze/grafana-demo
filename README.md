Clone the values-nodeport.yaml

To install it:

helm upgrade --install cart podinfo/podinfo \
  --namespace ensemble-web --create-namespace \
  -f values-nodeport.yaml

  
