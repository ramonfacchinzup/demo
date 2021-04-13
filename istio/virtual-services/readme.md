# Istio Gateway and Virtual Service Demo

This sample project shows how to configure an Istio Gateway for an application, as well as the Virtual Service.

## Requirements

- Have a cluster properly configured
- Have `kubectl` installed and bound to the cluster from the previous step
- The Kubernetes cluster must be able to attach LoadBalancers for our services (if using Minikube or MicroK8s, Metallb is recommended)
- cURL installed

## Installing the Sample Service in the cluster

Open a command line within this project's root folder and execute the following commands (order matters):

```
$ kubectl apply -f kubernetes/namespace.yaml
$ kubectl apply -f kubernetes/deployment.yaml
$ kubectl apply -f kubernetes/service.yaml
$ kubectl apply -f kubernetes/gateway.yaml
$ kubectl apply -f kubernetes/virtualservice.yaml
```

## Checking if the Gateway and the Virtual Service are working

First of all, you must discover what's Istio's Ingress Gateway external address.

You can achieve that opening a command line and typing:

```
$ kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].hostname}'
```

If you're running a local Kubernetes cluster, the output might be empty. That means you have no hostname assigned to that service. An alternative is to query for Istio Ingress Gateway Service IP:

```
$ kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Then, make a request to the sample Virtual Service:

```
$ curl --location --request GET 'http://[HOST|IP]/sample/'
```

A NGINX default index page must be shown.

## Changing Sample Endpoint

To try changing dinamically the endpoint of a single Virtual Service it's a piece of cake. Edit `virtualservice.yaml`'s `spec.http[0].match.uri.prefix` from `/sample/` to `/sample2/` (or another path - your choice) and retype the `cURL` command from the previos section. The outcome must be the same.