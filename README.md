# workshop-payload-kubernetes
Payload for the workshop lesson on Kubernetes

## Getting started

1. Open CloudShell
2. Log in using `gcloud auth login` (open the link created in the browser, confirm access, copy verification code)
3. Create a _kubconfig_ entry via `gcloud container clusters get-credentials cluster-1 --zone us-central1-c --project cern-cms`


## Persistent volume claims

One claim for the opendata (mount at `/opendata`) and one for the analysis output.

## cernopendata-client-go Jobs

Create jobs, then get the logs to see what happened.

## HTTP fileserver

```shell
kubectl create configmap basic-config --from-file=conf.d -n argo
kubectl create -f deployment-http-fileserver.yaml
kubectl expose deployment http-fileserver -n argo --type LoadBalancer --port 80 --target-port 80
```

Then check service `http-fileserver` to see if it got a public IP and open address in the browser.

## Argo installation

```shell
curl -sLO https://github.com/argoproj/argo/releases/download/v2.11.1/argo-linux-amd64.gz
gunzip argo-linux-amd64.gz
chmod +x argo-linux-amd64
mv argo-linux-amd64 argo
kubectl create clusterrolebinding cms-cluster-admin-binding --clusterrole=cluster-admin --user=cms-g001@arkivum.com
```