---
title: 'Gitea & Drone CI'
date: 2019-02-11T19:30:08+10:00
draft: false
weight: 5
summary: Syntax highlighting and menus can be configured via `config.toml`.
---

Når du nå åpner ArgoCD i nettleseren vil du se en app som heter `app-of-apps` og som er ute av sync. Hvis du så trykker på synkroniser vil du se at den oppretter en Gitea, Drone server og Drone runner instans for oss.

> 🎉 *Installert*: Gitea er nå installert og du kan nå den ved å bruke `port-forwarding`.

```bash
kubectl port-forward svc/gitea-http -n gitea 3000 --address 0.0.0.0
```

> 👉 *Viktig*: La denne `port-forwarding` kjøre i bakgrunnen mens du fortsetter.

Gå til gitea sin url og logg inn med

```bash
brukernavn: gitea_admin
passord: r8sA8CPHD9!bt6d
```

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
kubectl apply -f https://raw.githubusercontent.com/jonasbg/gitOps-intro/drone/applications/app-of-apps.yml
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