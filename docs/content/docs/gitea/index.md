---
title: 'Gitea & Drone CI'
date: 2019-02-11T19:30:08+10:00
draft: false
weight: 5
summary: Syntax highlighting and menus can be configured via `config.toml`.
---

Når du nå åpner ArgoCD i nettleseren vil du se en app som heter `app-of-apps` og som er ute av sync. Hvis du så trykker på synkroniser vil du se at den oppretter en Gitea, Drone server og Drone runner instans for oss.


```bash
brukernavn: gitea
passord: gitops
```

### Lag OAuth2 applikasjon i Gitea

Lag en OAuth2 applikasjon med følgende innstillinger:
```bash
Application Name: DroneCI
Callback URI: https://drone.local/login
```

Kopier `Client ID` og `Client Secret` og bruk de som override i ArgoCD for DroneCI etter installasjon i steget under.

# DroneCI

## Overskriv parametere
Gå inn i **ArgoCD** og overskriv parameterne til DroneCI for å bruke `Client ID` og `Client Secret` fra Gitea. Dette gjøres under **parametere** i prosjektet under **App Details**, og i **values** teksboksen.

Når du nå synkroniserer prosjektet vil ArgoCD installere DroneCI server og runner.
