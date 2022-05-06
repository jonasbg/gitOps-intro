# gitOps-intro

> 👋 **Følg stegene i branchen _main_ først:** Du bør følge stegene i main før du fortsetter her

## ArgoCD
### Installer ArgoCD i klusteret
```bash
kubectl create namespace argocd &&
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
> 👋  **Ta deg en kopp kaffe**: Nå må vi vente til ArgoCD har fått spunnet opp før vi kan hente admin passordet!

### Hent admin passord
Gjenta denne kommandoen frem til passordet vises, i mellomtiden er den tom.
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" 2> /dev/null | base64 -d && echo
```

### Åpne ArgoCD og logg inn
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443 --address 0.0.0.0
```

Logg inn med `brukernavn=admin` og `passord` fra passordet ovenfor her.

> 👉 *Viktig*: La denne `port-forwarding` kjøre i bakgrunnen mens du fortsetter.

# App-of-apps
Siden vi jobber med gitOps skal vi nå gå mot et ekte git repo som grunnlag for hva ArgoCD skal gjøre fremover.

Installer `app-of-apps` i klusteret
```bash
kubectl apply -f https://raw.githubusercontent.com/jonasbg/gitOps-nnug-demo/gitea/applications/app-of-apps.yml
```

# Gitea
For å kunne
### Installer Gitea
Når du nå åpner ArgoCD i nettleseren vil du se en app som heter `app-of-apps` og som er ute av sync. Hvis du så trykker på synkroniser vil du se at den oppretter en Gitea instans for oss.

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

# Fortsett
> 👉 **Gå til _drone_:** Fortsett stegene ved å gå til _drone_ branchen.
