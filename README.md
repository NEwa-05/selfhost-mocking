# selfhost-mocking

Test self-hosted mocking with Hub

## Deploy Traefik Hub

### Create namespace

```bash
kubectl create ns traefik
```

### Create Hub token secret

```bash
kubectl create secret generic hub-license --from-literal=token="${HUB_TOKEN}" -n traefik
```

### deploy Traefik

```bash
helm upgrade --install traefik traefik/traefik --create-namespace --namespace traefik --values hub/hub-values.yaml --set "additionalArguments={--certificatesresolvers.le.distributedAcme=true,--certificatesresolvers.le.distributedAcme.storage.kubernetes=true,--certificatesresolvers.le.distributedAcme.email=david.blaskow@traefik.io,--certificatesresolvers.le.distributedAcme.tlschallenge=true',--hub.providers.microcks.endpoint=http://microcks.microcks:8080}"
```

### Add Hub dashboard ingress if needed

```bash
envsubst < hub/ingress.yaml | kubectl apply -f -
```

## Install Microcks

### add Microcks helm repo

```bash
helm repo add microcks https://microcks.io/helm
```

### deploy Microcks

```bash
helm upgrade --install microcks microcks/microcks --create-namespace --namespace microcks --values microcks/values.yaml --set microcks.url=microcks.${CLUSTERNAME}.${DOMAINNAME}
```
