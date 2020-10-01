# workshop-payload-kubernetes
Payload for the workshop lesson on Kubernetes

## Getting started

1. Open CloudShell
2. Log in using `gcloud auth login` (open the link created in the browser, confirm access, copy verification code)
3. Create a _kubconfig_ entry via `gcloud container clusters get-credentials cluster-1 --zone us-central1-c --project cern-cms`

## Storage

Need to use NFS persistent volumes to allow parallel access (see also
[this post](https://medium.com/platformer-blog/nfs-persistent-volumes-with-kubernetes-a-case-study-ce1ed6e2c266)).

```shell
kubectl apply -n argo -f https://raw.githubusercontent.com/cms-opendata-workshop/workshop-payload-kubernetes/master/001-nfs-server.yaml
kubectl apply -n argo -f https://raw.githubusercontent.com/cms-opendata-workshop/workshop-payload-kubernetes/master/002-nfs-server-service.yaml
```

Then download the manifest needed to create the PersistentVolume and
the PersistentVolumeClaim:

```shell
curl -OL https://raw.githubusercontent.com/cms-opendata-workshop/workshop-payload-kubernetes/master/003-pv-pvc.yaml
```

In the line containing `server:` replace `<Add IP here>` by the output
of the following command:

```shell
kubectl get svc nfs-server |grep ClusterIP | awk '{ print $3; }'
```

This command queries the `nfs-server` service that we created above
and then filters out the `ClusterIP` that we need to connect to the
NFS server.

## cernopendata-client-go Jobs

Create jobs, then get the logs to see what happened.

## HTTP fileserver

```shell
kubectl create configmap basic-config --from-file=conf.d -n argo
kubectl create -n argo -f deployment-http-fileserver.yaml
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