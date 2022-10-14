# Nginx ingress controller

## Documentation

All documentation of the nginx controller can be found under [kubernetes.github.io/ingress-nginx/](https://kubernetes.github.io/ingress-nginx/deploy/#bare-metal-clusters)

## Installation

install the ingress controller from the cli

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.4.0/deploy/static/provider/baremetal/deploy.yaml
```

Notice everything being installed under the namespace **nging-ingress**

```sh
kubectl get all -n nginx-ingress
```

![installation](images/ingress-controller/installation.jpg)

It is important to note that there is only one replcias of the ingress controller, meaning accessing and it is the point of entrance for the whole infrastructure. This is why it is the only exception to everything else where binding it to port 80 and 443 of the host machine is good.

This will need to be done outside the service by modifying the deployment ingress-nginx-controller

```sh
kubectl edit deployment ingress-nginx-controller -n ingress-nginx
```

template.spec.hostNetwork: true
