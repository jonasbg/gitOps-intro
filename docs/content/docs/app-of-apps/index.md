---
title: 'App of apps'
date: 2019-02-11T19:27:37+10:00
weight: 4
---

Siden vi jobber med gitOps skal vi nå gå mot et ekte git repo som grunnlag for hva ArgoCD skal gjøre fremover.

Installer `app-of-apps` i klusteret
```bash
kubectl apply -n argocd -f https://raw.githubusercontent.com/jonasbg/gitOps-intro/static-site/cluster/app-of-apps.yml
```

Når applikasjonen er ferdig installert vil du kunne nå argocd gjennom https://argocd.local