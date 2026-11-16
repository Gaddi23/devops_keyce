# TP 2.6 — Dockerfile avancé : optimiser, sécuriser, tester

**Énoncé :** [TP2.6_Dockerfile_avance.md](../TP2.6_Dockerfile_avance.md)
**Statut :** à faire

## Objectif

Passer à un Dockerfile de production : image de base légère, cache de build, `.dockerignore`, build multi-stage avec tests, utilisateur non-root et `HEALTHCHECK`.

## Fichiers produits

_À compléter : lister les fichiers créés (Dockerfile, compose.yml, scripts) et expliquer en une ligne les instructions importantes._

## Vérifications (une capture par ligne dans `captures/`)

- [ ] tailles comparées `restaurant:ubuntu` / `restaurant:alpine`
- [ ] `docker build --target test .` : *6 passed*
- [ ] `docker run --rm legeni-webapp:1.0.0 env | grep APP_VERSION` : `1.0.0`
- [ ] `docker run --rm legeni-webapp:1.0.0 whoami` : `appuser`
- [ ] `docker ps` : `(healthy)`

## Réponses aux questions

1. Combien de couches a l'image ubuntu ? Laquelle est la plus lourde ?
2. Effet de l'ordre `COPY . .` / `pip install` sur la durée du build ?
3. Quel risque à faire tourner un conteneur en `root` ?

> Prérequis manquant : le dossier `app-fil-rouge/` utilisé par ce TP n'est pas présent dans le dépôt du cours.

## Problèmes rencontrés

| Symptôme | Cause | Solution |
|---|---|---|
| | | |
