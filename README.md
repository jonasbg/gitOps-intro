# gitOps-intro

> 👋 **Følg stegene i branchen _gitea_ først:** Du bør følge stegene i main før du fortsetter her

### Lag OAuth2 applikasjon i Gitea

Lag en OAuth2 applikasjon med følgende innstillinger:
```bash
Application Name: DroneCI
Callback URI: http://192.168.64.5:8000/login
```

Kopier `Client ID` og `Client Secret` og bruk de som override i ArgoCD for DroneCI etter installasjon i steget under.

# DroneCI
For å installere DroneCI må vi fortelle ArgoCD hvor manifestet befinner seg. Manifestet befinner seg på en egen branch som heter `drone`, så dette kan endres fra ArgoCD UI eller ved å patche `app-of-apps` manifestet.
```bash
kubectl apply -f https://raw.githubusercontent.com/jonasbg/gitOps-nnug-demo/drone/applications/app-of-apps.yml
```

## Overskriv parametere
Gå inn i **ArgoCD** og overskriv parameterne til DroneCI for å bruke `Client ID` og `Client Secret` fra Gitea. Dette gjøres under **parametere** i prosjektet under **App Details**, og i **values** teksboksen.

Når du nå synkroniserer prosjektet vil ArgoCD installere DroneCI server og runner.

## Åpne DroneCI
For å kunne gå til DroneCI må du ta en `port-forward` også på denne.
```bash
kubectl port-forward svc/drone -n drone 8000:80 --address 0.0.0.0
```

> 👉 *Viktig*: La denne `port-forwarding` kjøre i bakgrunnen mens du fortsetter.

## Koble til repoet
Nå kan vi koble DroneCI til repoet vårt. Gå til [http://192.168.64.5:8000](http://192.168.64.5:8000)

# Fortsett
> 👉 **Gå til branchen _golang_**: Fortsett ved å gå til neste branch _golang_.
