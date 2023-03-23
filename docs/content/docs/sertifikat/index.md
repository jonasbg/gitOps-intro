---
title: 'Sertifikater'
date: 2019-02-11T19:27:37+10:00
weight: 7
---
Hvis du har et eget domene kan du knytte opp cert-manager og nginx mot f.eks Let's Encrypt. Men i dette eksemplet skal vi se på et internt kluster. Og for å få kjørt tls kryptering mellom tjenestene, og ut av klusteret, må vi lage vårt eget sertifikat som cert-manager bruker for å utstede sertifikater for alle tjenestene.

## Opprett et nytt CA sertifikat

Det opprettes automatisk eget CA og sertifikater for hver enkelt tjeneste. Det som nå må gjøres er å manuelt godkjenne CA sertifikatet på egen maskin og på den maskinen der klusteret kjører. Dette for å kunne få tilgang til tjenestene uten å måtte si at man godtar koblingen selv om den ikke er "sikker".

I noen tilfeller som i DroneCI så gjøres det her et unntak, da byggeagentene ikke automatisk får tilgang til CA sertifikatene, og det i sånn måte må importeres som en del av byggepipeline. Av praktiske, og av læringsøymed er disse stegene heller utelatt.

### Importere sertifikatene på egen maskin og i kluster

```shell
# Hent ut nåværende issuer sertifikat.
kubectl get secrets -n cert-manager self-signed-ca -o json | jq -r '.data["tls.crt"]' | base64 -d > issuer.crt

# Installer sertifikat i lokal cert-store
sudo cp issuer.crt /usr/local/share/ca-certificates/issuer.crt
sudo update-ca-certificates

# Test om sertifikatet er gyldig. Denne kommandoen skal skrive ut HTML kode om den er TLS er gyldig.
curl -L argocd.local
```

### Importere CA sertifikatet inn i podene

For at gitea og droneci skal kunne kommunisere over en sikker TLS forbindelse, må man legge inn `issuer.crt` filen i hver eneste pod. Dette skal vi gjøre ved hjelp av en configmap, og er et stykke manuelt arbeid.

Først må vi inn med en configMap i hvert eneste namespace der en pod skal ha tilgang til CA sertifiktatet.
```shell
apiVersion: v1
data:
  ca.crt: |
    -----BEGIN CERTIFICATE-----
              [...]
    -----END CERTIFICATE-----
kind: ConfigMap
metadata:
  name: ca-store
  namespace: drone
```

Så må vi mounte inn denne configMap verdien inn i hver pod.
```shell
apiVersion: v1
kind: Pod
metadata:
  name: drone
  namespace: drone
spec:
  containers:
    - image:
      volumeMounts:
        - mountPath: /etc/ssl/certs/ca.crt
          name: ca-store
          readOnly: true
          subPath: ca.crt #denne er VIKTIG. Hvis ikke overskrives hele mappen.
  volumes:
    - configMap:
        defaultMode: 420
        name: ca-store
      name: ca-store
```
### Info
> Heldigvis tar HELM og gjør dette arbeidet lett for oss. Bare gå til `cluster/apps/configmaps/values.yml` og endre CA.crt verdien til innholder i `issuer.crt` filen.