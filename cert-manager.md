# Cert-Manager
## Documentation
You can find everything related to cert-manager under [cert-manager.io](https://cert-manager.io/docs/)

It is a tool that will handle our ssl certificate within the whole cluster!

## Installation
Install cert-manager from the cli
```sh
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.9.1/cert-manager.yaml
```

notice everything created in the **cert-manager** namespace
```sh
kubectl get all -n cert-manager
```
![installation](images/cert-manager/installation.jpg)