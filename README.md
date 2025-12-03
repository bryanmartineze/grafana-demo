1. Create an EKS cluster using Rancher

2. Open the port 30080 to everyone from the Security Group

3. Clone the values-nodeport.yaml of this repo to your machine/kubernetes cli

4. To install it run:

helm repo add podinfo https://stefanprodan.github.io/podinfo
helm repo update

helm install cart podinfo/podinfo \
  -n ensemble-web --create-namespace \
  -f values-demo.yaml

5.-Now that is deployed, check the Prometheus metrics by:

  Understanding one of the node Public IP:
  kubectl get nodes -o wide

  Then run:

  curl http://<node_ip_address>:30080/          # podinfo UI
  curl http://<node_ip_address>:30080/metrics | head

  You should see something like:

    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
    0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0# HELP go_gc_duration_seconds A summary of the wall-time pause (stop-the-world) duration in garbage collection cycles.
  # TYPE go_gc_duration_seconds summary
  1go_gc_duration_seconds{quantile="0"} 2.891e-05
  0go_gc_duration_seconds{quantile="0.25"} 0.000137514
  0go_gc_duration_seconds{quantile="0.5"} 0.000276659
   go_gc_duration_seconds{quantile="0.75"} 0.000471624
  1go_gc_duration_seconds{quantile="1"} 0.002029422
  6go_gc_duration_seconds_sum 0.006143666
  5go_gc_duration_seconds_count 14

That means that podinfo is succesfully installed and generating metrics


6. Inside your a cluster node, install hey:

# Download hey binary for Linux amd64
curl -L https://hey-release.s3.us-east-2.amazonaws.com/hey_linux_amd64 \
  -o hey

chmod +x hey
sudo mv hey /usr/local/bin/  # if you have sudo
# or just: mv hey ~/bin/ && export PATH=$PATH:~/bin

