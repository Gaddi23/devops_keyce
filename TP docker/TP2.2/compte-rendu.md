# TP 2.2 — Installer Docker et maîtriser le cycle de vie d'un conteneur

**Énoncé :** [TP2.2_Installation_Premiers_conteneurs.md](../TP2.2_Installation_Premiers_conteneurs.md)
**Statut :** fait le 16/11/2026 (étapes 2 à 5) — étape 6 (Odoo) non réalisée faute d'espace disque, défi Redis bloqué par une coupure réseau
**Preuve :** [`preuves/tp2.2-sortie-commandes.txt`](preuves/tp2.2-sortie-commandes.txt) — sortie réelle de toutes les commandes

## Objectif

Avoir un Docker utilisable sans `sudo`, piloter un conteneur de bout en bout (`run`, `logs`, `exec`, `stop`, `rm`) et comprendre ce qui survit (ou non) à la suppression d'un conteneur.

## Environnement

Docker Desktop sous Windows 11 : client `29.5.2 (windows/amd64)`, serveur `29.5.2 (linux/amd64)`, stockage `overlayfs`, cgroup v2. Les étapes 1 (installation) et 2 (groupe `docker`) sont assurées par Docker Desktop. Le port hôte **8091** remplace 8081, déjà utilisé par le port-forward WordPress.

## Ce qui a été fait et observé

| Étape | Commande clé | Résultat observé |
|---|---|---|
| 2 | `docker version` | deux parties : **Client** (Windows) et **Server** (Linux) |
| 3 | `docker run hello-world` | message « Hello from Docker! » décrivant les 4 étapes ; conteneur ensuite `Exited (0)` |
| 4.1 | `docker run -d --name web -p 8091:80 nginx:1.27-alpine` | `curl` renvoie *Welcome to nginx!* |
| 4.2 | `docker logs`, `top`, `stats`, `inspect` | requête `GET / 200` dans les logs ; 4,4 Mio de RAM ; IP `172.17.0.2` |
| 4.3 | `docker exec web cat /etc/os-release` | **Alpine Linux** (et non Windows ni Ubuntu) ; `nginx: master process` est le **PID 1** |
| 4.4 | modification de `index.html` dans le conteneur | la page affiche *Modifié à la main* |
| 4.5 | `stop` puis `start` | la modification est **toujours là** |
| 4.5 | `rm -f` puis nouveau `run` | la page d'origine *Welcome to nginx!* revient : modification **perdue** |
| 5 | `docker run --rm ubuntu:24.04 …` | *Ubuntu 24.04.5 LTS* ; aucun conteneur restant après la sortie |

## Réponses aux questions

**1. Que se passe-t-il quand on tape une commande `docker` ?**
La commande `docker` n'est qu'un **client**. Elle envoie une requête à l'API du **démon** `dockerd` (le serveur), via le socket `/var/run/docker.sock` sous Linux (un *named pipe* sous Windows). C'est le démon qui télécharge les images, crée et lance les conteneurs. D'où les deux parties de `docker version`. Avoir accès à ce socket revient à être root sur la machine.

**2. `docker stop` / `docker rm` / `docker rmi` ?**
- `docker stop` arrête le processus du conteneur (SIGTERM, puis SIGKILL après 10 s). Le conteneur existe toujours (`Exited`) et peut être relancé avec `docker start`.
- `docker rm` **supprime** le conteneur et sa couche inscriptible.
- `docker rmi` supprime une **image**, le modèle à partir duquel on crée des conteneurs.

**3. Pourquoi la modification survit-elle à `stop/start` mais pas à `rm` + `run` ?**
Chaque conteneur a sa propre couche inscriptible au-dessus de l'image. `stop/start` réutilise le même conteneur, donc la même couche. `rm` détruit cette couche, et `run` crée un conteneur neuf à partir de l'image d'origine.
Conclusion : le contenu d'un site doit être **dans l'image** (Dockerfile, TP 2.3) et les données dans un **volume** (TP 2.4). On ne modifie jamais un conteneur à la main.

## Vérifications

- [x] `docker ps` sans *permission denied*
- [x] `docker ps -a` : aucun conteneur `web` restant après l'étape 4
- [ ] `docker compose ps` : `db` et `odoo` en *running* — étape 6 non réalisée (image Odoo ≈ 1,5 Go, 0,8 Go libres)

## Problèmes rencontrés

| Symptôme | Cause | Solution |
|---|---|---|
| Port 8081 déjà utilisé | port-forward Kubernetes vers WordPress actif | port hôte 8091 (`-p 8091:80`) |
| `lookup registry-1.docker.io: no such host` (défi Redis) | coupure de la connexion Internet | relancer `docker pull redis:7.4-alpine` quand la connexion revient |
| Odoo non lancé | disque C: presque plein | libérer au moins 3 Go avant l'étape 6 |
