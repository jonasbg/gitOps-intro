# gitOps-nnug-demo

Opprett en linux VM med minkube eller annen ønsket k8s tilbyder.
```bash
# Start multipass VM
multipass launch --name demo --cpus 2 --mem 4G --disk 50G minikube
```

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
For å åpne rett url er det lettest å finne IP adressen til Multipass VM.
```bash
multipass list

Name                    State             IPv4             Image
demo                    Running           192.168.64.5     Ubuntu 20.04 LTS
```
Nå kan du åpne ArgoCD på [http://192.168.64.5:8080](http://192.168.64.5:8080)

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

### Lag OAuth2 applikasjon i Gitea
Gå til gitea sin url og logg inn med

```bash
brukernavn: gitea_admin
passord: r8sA8CPHD9!bt6d
```
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

## Åpne DroneCI
For å kunne gå til DroneCI må du ta en `port-forward` også på denne.
```bash
kubectl port-forward svc/drone -n drone 8000:80 --address 0.0.0.0
```

> 👉 *Viktig*: La denne `port-forwarding` kjøre i bakgrunnen mens du fortsetter.

## Koble til repoet
Nå kan vi koble DroneCI til repoet vårt. Gå til [http://192.168.64.5:8000](http://192.168.64.5:8000)

# Port-forwarding
Dette eksemplet krever en ip og port-forwarding som er gitt, hvis ikke vil ikke tjenestene fungere. Vi kan forenkle dette med å lage en tmux sesjon som håndterer alt dette for oss.

```bash
tmux new-session -s port-forward -d 'kubectl port-forward svc/argocd-server -n argocd 8080:443 --address 0.0.0.0'
tmux split-window -t port-forward -v 'kubectl port-forward svc/gitea-http -n gitea 3000 --address 0.0.0.0'
tmux split-window -t port-forward -v 'kubectl port-forward svc/drone -n drone 8000:80 --address 0.0.0.0'
```