Clone the values-nodeport.yaml

To install it:

helm repo add podinfo https://stefanprodan.github.io/podinfo
helm repo update

helm install cart podinfo/podinfo \
  -n ensemble-web --create-namespace \
  -f values-demo.yaml

  
