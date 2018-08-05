# Solution

This document contains a solution to the _learnk8s_ _Kubernetes_ challenge.  

Included in this repository are some very simple _kubernetes_ manifests.  They can be found in the `manifests/` folder.

We first need to build the _Docker_ container.

```console
docker build -t learnk8s:v1 .
```

Before continuing, we need to check the IP address of our _Minikube_ profile.

```console
minikube ip
```

Generally you would expect the that the IP address for the default _Minikube_ profile is `192.168.99.100`.  The configuration of the `ingress.yaml` has this IP address encoded into the FQDN within the `host` field, as can be seen below.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: learnk8s
  annotations:
    kubernetes.io/ingress.class: nginx
  labels:
    app: learnk8s
spec:
  rules:
  - host: learnk8s.192.168.99.100.nip.io
    http:
      paths:
      - backend:
          serviceName: learnk8s
          servicePort: 80
        path: /
```

**Note**

The value of the `host` field is using an FQDN from the `nip.io` domain.  `nip.io` is a DNS service that translates an FQDN to an IP address that is encoded within the FQDN.  From our example we can see that the encoded IP address is `192.168.99.100`.

If you discovered that your _Minikube_ IP address is not `192.168.99.100` then you will need to modify the `ingress.yaml` to reflect your IP address before proceeding.

Next, we need to ensure that we have the _Nginx Ingress Controller_ _Minikube_ Addon running.

```console
minikube addons enable ingress
```

**Note**

You may need to wait a few minutes for the _Nginx Ingress Controller_ to download.


Now, we can deploy our application.

```console
kubectl -n default apply -f manifests
```

Finally, we can test that the application is accessible.

```console
curl learnk8s.192.168.99.100.nip.io
```

**Note**

If necessary, change the FQDN used by the `curl` command to reflect the IP address of your _Minikube_ profile.

We should now see the following output.

```console
Hello Des!
```

