# TP 2.2 — Installer Docker et maîtriser le cycle de vie d'un conteneur

**Énoncé :** [TP2.2_Installation_Premiers_conteneurs.md](../TP2.2_Installation_Premiers_conteneurs.md)
**Statut :** à faire

## Objectif

Avoir un Docker utilisable sans `sudo`, piloter un conteneur de bout en bout (`run`, `logs`, `exec`, `stop`, `rm`) et lancer une première stack Compose (Odoo + PostgreSQL).

## Fichiers produits

_À compléter : lister les fichiers créés (Dockerfile, compose.yml, scripts) et expliquer en une ligne les instructions importantes._

## Vérifications (une capture par ligne dans `captures/`)

- [ ] `docker ps` sans *permission denied*
- [ ] `docker compose version` affiche v2.x
- [ ] `docker ps -a` : aucun conteneur `web` restant après l'étape 4
- [ ] `docker compose ps` : `db` et `odoo` en *running*
- [ ] base Odoo toujours présente après `down` / `up`

## Réponses aux questions

1. Que se passe-t-il quand on tape une commande `docker` (client, démon, `/var/run/docker.sock`) ?
2. Différence entre `docker stop` et `docker rm` ? Entre `docker rm` et `docker rmi` ?
3. Pourquoi la modification à chaud survit-elle à `stop/start` mais pas à `rm` + `run` ?

## Problèmes rencontrés

| Symptôme | Cause | Solution |
|---|---|---|
| | | |
