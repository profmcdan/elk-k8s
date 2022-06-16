# Install Nginx Ingress

```sh
helm upgrade --install ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --create-namespace
```

## Docker Desktop Kubernetes Dashboard 

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.5.0/aio/deploy/recommended.yaml

kubectl patch deployment kubernetes-dashboard -n kubernetes-dashboard --type 'json' -p '[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value": "--enable-skip-login"}]'
```

## Install Metrics Server 

```sh
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

kubectl patch deployment metrics-server -n kube-system --type 'json' -p '[{"op": "add", "path": "/spec/template/spec/containers/0/args/-", "value": "--kubelet-insecure-tls"}]'
```

## Delete Dashboard and Metrics Server 

```sh
kubectl delete -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.4.2/components.yaml

kubectl delete -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.2.0/aio/deploy/recommended.yaml
```

View the dashboard on:

```sh
# Run the Kubectl proxy to allow accessing the dashboard
kubectl proxy
```

## Getting a Bearer Token

### Create a service Account if non exists

```yml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```

You can run `kubectl apply -f dashboard-adminuser.yaml`

### Create a ClusterRoleBinding

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

You can run `kubectl apply -f dashboard-clusterrole.yaml`

Now we need to find the token we can use to log in. Execute the following command and copy the token:

```sh
kubectl -n kubernetes-dashboard create token admin-user
```

[http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/)

## Install the helm packages 

```sh
cd filebeat
helm install filebeat ./filebeat/.

cd ../logstash
helm install logstash .

cd ../elasticsearch
helm install elasticsearch .

cd ../kibana
helm install kibana .
```