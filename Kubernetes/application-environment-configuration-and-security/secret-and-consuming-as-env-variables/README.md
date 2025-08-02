# Secret and Consuming It as Environment Variables

## Install

```text
kubectl apply -f install.yaml
```

File: [install.yaml](install.yaml)

## Checks

```text
# kubectl -n secret-and-consuming-as-env-variables exec -it nginx -- env

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=nginx
TERM=xterm
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
NGINX_VERSION=1.23.2
NJS_VERSION=0.7.7
PKG_RELEASE=1~bullseye
HOME=/root
```

Ok, that's wrong. We shall use key3 from Secrat as KEY3 env.

## Solution

```text
$ kubectl -n secret-and-consuming-as-env-variables get pod nginx -o yaml > edit.yaml

$ nano edit.yaml

???
# kubectl -n secret-and-consuming-as-env-variables set env --keys=KEY3 --from=secret/nginx-secret pod/nginx
```

```yaml
spec:
  containers:
  - image: nginx
    ...
    # <-- add this following 6 rows
    env:
      - name: KEY3
        valueFrom:
          secretKeyRef:
            name: nginx-secret
            key: Key3
    # end
```

````text
$ kubectl -n secret-and-consuming-as-env-variables delete pod nginx

$ kubectl -n secret-and-consuming-as-env-variables apply -f edit.yaml
```

## Verify

```text
kubectl -n secret-and-consuming-as-env-variables exec -it nginx -- env

PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=nginx
TERM=xterm
KEY3=TG1MSGJZaHNnV1p3TmlmaXFhUm9ySDhU # <-- Bingo!!!
```
