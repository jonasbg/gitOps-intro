---
title: 'Multipass'
date: 2019-02-11T19:27:37+10:00
weight: 2
---

For å kunne starte, må du ha tilgang til et kubernetes kluster. Et av de enklere verktøy for å få til dette er multipass som oppretter en VM der man installerer microk8s. Det finnes mange gode alternativer som er mye mer lettvektere, men dette er valgt fordi multipass kan brukes til andre formål, og microk8s er et godt alternativ for å kjøre kubernetes på low-end maskinvare som Raspberry Pi.

For å installere multipass følg den offisielle installasjons guiden på [installasjons guide](https://multipass.run/install)

### Installer microk8s

```shell
multipass launch --name microk8s-vm --mem 4G --disk 40G
multipass shell microk8s-vm
sudo snap install microk8s --classic --channel=1.26/stable
sudo iptables -P FORWARD ACCEPT

sudo usermod -a -G microk8s $USER
sudo chown -f -R $USER ~/.kube
microk8s status --wait-ready
```

### Kommandoer du bør vite om

Alias for enklere kommandoer
```shell
alias kubectl="microk8s kubectl"
alias k=kubectl
```
For å persistere kommandoene over, må de legges inn i .-rc fil som `.bashrc` eller `.zshrc`.
```shell
cat << EOF >> ~/.bashrc
  alias kubectl="microk8s kubectl"
  alias k=kubectl
EOF
```


Sjekk noder og pod tilstander
```shell
kubectl get nodes
kubectl get pods -A
```

Opprett shell til vm
```shell
multipass shell microk8s-vm
```

Stopp Vm
```shell
multipass stop microk8s-vm
```

Slett VM og rydd opp
```shell
multipass delete microk8s-vm
multipass purge
```