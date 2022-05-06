# gitOps-intro

> 👋 **Følg stegene i branchen _drone_ først:** Du bør følge stegene i drone før du fortsetter her

# Gitea
-> Gå til Gitea og importer dette repoet til Gitea. Dette gjøres under **+** og **New migration**.

# DroneCI runner
* Gå til DroneCI, logg inn og autoriser mot Gitea. 
* Du bør nå få en opp repoet som du migrerte over. 
  * Aktiver dette repoet.
* Trykk på **New Build** og vel _golang_ som branch.
* DroneCI vil nå starte 3 poder i klusteret som alle bygger en golang versjon med testing.

# Port-forwarding
Dette eksemplet krever en ip og port-forwarding som er gitt, hvis ikke vil ikke tjenestene fungere. Vi kan forenkle dette med å lage en tmux sesjon som håndterer alt dette for oss.

```bash
tmux new-session -s port-forward -d 'kubectl port-forward svc/argocd-server -n argocd 8080:443 --address 0.0.0.0'
tmux split-window -t port-forward -v 'kubectl port-forward svc/gitea-http -n gitea 3000 --address 0.0.0.0'
tmux split-window -t port-forward -v 'kubectl port-forward svc/drone -n drone 8000:80 --address 0.0.0.0'
```
