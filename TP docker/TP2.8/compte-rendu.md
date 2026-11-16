# TP 2.8 — Docker Compose multi-services

**Énoncé :** [TP2.8_Compose_multi_services.md](../TP2.8_Compose_multi_services.md)
**Statut :** à faire

## Objectif

Décrire proxy nginx + web Flask ×N + Redis dans un seul `compose.yml`, avec healthchecks, ordre de démarrage, persistance, mise à l'échelle et fichier `.env`.

## Fichiers produits

_À compléter : lister les fichiers créés (Dockerfile, compose.yml, scripts) et expliquer en une ligne les instructions importantes._

## Vérifications (une capture par ligne dans `captures/`)

- [ ] `docker compose ps` : tous les services running, `web` et `redis` healthy
- [ ] le compteur survit à `docker compose down` mais pas à `down -v`
- [ ] avec `--scale web=3`, au moins 2 hostnames différents
- [ ] port et couleur pilotés par `.env`

## Réponses aux questions

1. Que se passerait-il sans `REDIS_HOST` (compteur en mémoire) avec 3 réplicas ?
2. Différence entre `/health` (liveness) et `/ready` (readiness) ?

> Prérequis manquant : le dossier `app-fil-rouge/` utilisé par ce TP n'est pas présent dans le dépôt du cours.

## Problèmes rencontrés

| Symptôme | Cause | Solution |
|---|---|---|
| | | |
