# Istio Gateway and Virtual Service Demo

This sample project shows how to configure an Istio Gateway for an application, as well as the Virtual Service.

## Requirements

- A Kubernetes cluster properly configured
- `kubectl` installed on local machine and bound to the cluster from the previous step
- Istio installed on the Kubernetes cluster
- The Kubernetes cluster must be able to attach LoadBalancers for our services (if using Minikube or MicroK8s, Metallb is recommended)
- cURL installed on local machine

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

# Appendix: Installing Microk8s and Istio on Ubuntu

On Ubuntu, make sure you have `snap` package manager installed. Otherwise, that can be achieved through the following command:

```
$ sudo apt install snap
```

With `snap installed` Microk8s installation is as simple as this:

```
$ sudo snap install microk8s --classic
```

After Microk8s installation, a few add ons must be enabled:

```
$ microk8s enable ingress
$ microk8s enable dns
$ microk8s enable dashboard
$ microk8s enable storage
$ microk8s enable registry
$ microk8s enable metrics-server
$ microk8s enable metallb
$ microk8s enable istio
```

As you can see, istio is among the add ons enabled. You must wait a few minutes before every add on is fully set up.

Microk8s also works as a "wrapper" for `kubectl`. So you can skip `kubectl` installation to user microk8s through command line simply by addind `microk8s ` prefix to every `kubectl` command - although that is not recommended.

Otherwise, if you have `kubectl` installed, you can get Microk8s configuration setup to add to your `~/.kube/config` file with the following command:

```
$ microk8s config
```

Make sure you paste the configuration in the proper places if you already have a context configured for another Kubernetes cluster.

To actually use microk8s context with `kubectl` you must command:

```
$ kubectl config use-context microk8s
```

And now you're ready to go. [Lens](https://k8slens.dev/) project is also a great graphical way to inspect your kubernetes cluster - give it a try.