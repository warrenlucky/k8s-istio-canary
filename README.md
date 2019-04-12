# Helm

(I'm using Mac OS)

```
cd YOUR_WORKING_DIR
wget https://storage.googleapis.com/kubernetes-helm/helm-v2.13.1-darwin-amd64.tar.gz
tar zxvf helm-v2.13.1-darwin-amd64.tar.gz
cp ./darwin-amd64/helm /usr/local/bin/helm
```

Or install via homebrew:

```
brew install kubernetes-helm
```

If you don't have brew, do this first:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

# K8s

I'm using my own existing k8s cluster in Azure AKS.

If you are using anythign else, make sure your cluster is running and you can access the cluster before continuing.

If this test also wants to see how I deploy k8s with IaC, see here:

https://github.com/IronCore864/terraform-azure-examples/tree/master/modules/k8s

(My repo)

# Tiller

```
kubectl apply -f helm-rbac.yaml
helm init --service-account tiller
```

# Spinnaker

```
helm install --name spinnaker stable/spinnaker --timeout 600
```

# Istio

## Install
```
git clone https://github.com/istio/istio.git
cd istio
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
# verify
kubectl get crds | grep 'istio.io\|certmanager.k8s.io' | wc -l
# default config profile
helm install install/kubernetes/helm/istio --name istio --namespace istio-system
```

## Verify

```
kubectl get svc -n istio-system
kubectl get pods -n istio-system
# should all in Running status
```

# Canary Deployment

```
# two different versions
kubectl apply -f deployment.yaml
# svc
kubectl apply -f service.yaml
# virtual service, dest rules, gateway
kubectl apply -f canary.yaml
```

# Test Result

```
t.guo@KI-3033 ~/workspace/alz $ while true; do curl http://104.40.134.179/hello; done
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v2-789749bb77-q89h2 | v=2
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v2-789749bb77-q89h2 | v=2
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
Hello Kubernetes bootcamp! | Running on: hello-v1-868c88fb84-nmkz2 | v=1
```

# Notes

- To be able to show my deployment, I used k8s yaml instead of spinnaker UI;
- The description of this task specifies "services" plural, I only used 1 service to expose two different versions, it is already enough to show canary;
- Total time spent: 2 hours.

