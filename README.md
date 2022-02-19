# Instructions for local ArgoCD tests

## Minikube Basic Setup
1 - Init minikube:
```
minikube delete
minikube start
```

2 - Enable Ingress Controller:
```
minikube addons enable ingress
```

## Install Argo CD inside cluster
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
OBS: There is an easy helm chart option as well.

1 - Apply the Argo additional yamls:
```
kubectl apply -f argocd-install/
```

2 - Update hosts:
```
minikube ip
/etc/hosts 192.168.49.2    argocd.local
```

3 - Test:
https://argocd.local/


## Install Argo CD CLI (local)
1 - Follow the instructions:
https://argo-cd.readthedocs.io/en/stable/cli_installation/

2 - Get Argo CD admin local password:
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

3 - Argo CLI Login:
```
argocd login <ARGOCD_SERVER>
```

4 - Add kubectl context:
(These steps aren't required when kubectl is configured)
```
kubectl config get-contexts -o name
argocd cluster add <MY_CONTEXT>
argocd cluster list
```

# Basic Usage
## Create applications DECLARATIVE MODE
```
kubectl apply -f argocd-applications/
```

## Create applications COMMAND LINE MODE
```
argocd app create guestbook \
	--repo https://github.com/argoproj/argocd-example-apps.git \
	--path guestbook \
	--dest-server https://kubernetes.default.svc \
	--dest-namespace guestbook

argocd app create hello-world \
  --repo https://github.com/ricardofukuda/helm-hello-world.git \
  --path . \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace hello-world

argocd app create apache \
  --repo https://charts.bitnami.com/bitnami \
  --helm-chart apache \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace apache \
  --revision 9.0.2

argocd app list
argocd app get apache
argocd app sync apache
argocd app delete apache
```


#Cool Stuff	
## (only for tests) Uploading local manifests directly
```
argocd app sync hello-world --local helm-hello-world/
```

## (only for tests) Parameter Overrides
https://argo-cd.readthedocs.io/en/stable/user-guide/parameters/





















argocd app sync guestbook --local hello-world-argocd/