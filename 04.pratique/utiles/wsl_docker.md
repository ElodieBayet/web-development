
# Guide : Installer WSL et Docker

## WSL / Ubuntu 24.04

Depuis Windows 11, WSL est automatiquement en version 2.

Démarrer Power Shell en mode administrateur, et installer WSL
```sh
wsl --install
```
Contrôler les distributions disponibles
```sh
wsl.exe --list --online
```
Installer la distribution souhaitée
```sh
wsl.exe --install Ubuntu-24.04
```
Mettre à jour le répertoire local des packages et les installer
```sh
sudo apt update && sudo apt upgrade
```


### Installer les outils connexes

Installer Windows Terminal à partir du Microsoft Store.

Verifier que VS Code est ajouté au `PATH` des variables d'environnement (Système et Utilisateur).

À partir de WSL, ouvrir le répertoire courant
```sh
code .
```


---


## Installer Docker

⚠️ L'idéal est **d'installer le Docker Engine dans WSL** au lieu de passer par le Docker Desktop à partir de Windows.

### Docker Engine avec APT

Installation à partir du répertoire de l'Advanced Package Tool.

Ajouter la clé GPG officielle de Docker à l'APT
```sh
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```
Ajouter le repository aux sources APT
```sh
echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

Installer Docker – le Docker Engine
```sh
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
Vérifier le statut du Docker Engine
```sh
sudo systemctl status docker
```
Si besoin démarrer le Docker Engine
```sh
sudo systemctl start docker
```

⚠️ Pour éviter des complications, ne pas lancer de container avec `sudo docker run hello-world`.


### Configuration post-installation

Docker Engine est toujours exécuté en tant que `root` (Administrateur). Pour éviter de préfixer chaque commande `docker` par `sudo` il faut créer un groupe et y ajouter le `$USER`.

Créer le groupe "docker"
```sh
sudo groupadd docker
```
Ajouter l'utilisateur au groupe "docker"
```sh
sudo usermod -aG docker $USER
```
Activer les changements – ou redémarrer WSL si besoin
```sh
newgrp docker
```

Démarrer Docker au lancement de `systemd` 
```sh
sudo systemctl enable docker.service && sudo systemctl enable containerd.service
```


**Débuggage**

L'emploi du préfixe `sudo` antérieurement à la création / ajout au groupe "docker" risque de provoquer une erreur.

Si la commande `docker run hello-world` échoue
```
WARNING: Error loading config file: /home/user/.docker/config.json -
stat /home/user/.docker/config.json: permission denied
```
Réinitialiser la configuration locale en supprimant le répertoire `~/.docker`, ou alors, changer les droits
```sh
sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
sudo chmod g+rwx "$HOME/.docker" -R
```


---
---


## Annexes

### Glossaire

**GPG key**
Les clés GPG sont utilisées par l'Advanced Package Tool (APT) pour vérifier l'autenticité et l'intégrité d'un package, et s'assurer qu'il n'est pas falsifié.


### Pour en savoir plus

- [WSL Documentation - Microsoft Learn](https://learn.microsoft.com/en-us/windows/wsl/)
- [WSL Tutorials : Bonnes pratiques, VS Code, etc - Microsoft Learn](https://learn.microsoft.com/en-us/windows/wsl/setup/environment)
- [Windows Terminal - Microsoft Learn](https://learn.microsoft.com/en-us/windows/terminal/)
- [Docker Engine : Installation sur Ubuntu - Docker Docs](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)
- [Docker Engine : Configration Post-Installation - Docker Docs](https://docs.docker.com/engine/install/linux-postinstall/)
