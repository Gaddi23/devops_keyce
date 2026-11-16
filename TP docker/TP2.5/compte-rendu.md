# TP 2.5 — Configuration, ENTRYPOINT/CMD et processus n°1

**Énoncé :** [TP2.5_Configuration_Processus.md](../TP2.5_Configuration_Processus.md)
**Statut :** à faire

## Objectif

Construire une seule image valable en dev, staging et prod grâce aux variables d'environnement ; maîtriser `ARG`/`ENV`, `ENTRYPOINT`/`CMD` et l'arrêt propre d'un conteneur (SIGTERM, PID 1).

## Fichiers produits

_À compléter : lister les fichiers créés (Dockerfile, compose.yml, scripts) et expliquer en une ligne les instructions importantes._

## Vérifications (une capture par ligne dans `captures/`)

- [ ] `curl localhost:5001/api/info` et `:5002` : `"env":"dev"` et `"env":"prod"`
- [ ] `docker run --rm demo:args` : `BUILD_VERSION` vide mais `/version.txt` contient `2.0`
- [ ] `docker run --rm demo:entry "x"` : *Préfixe fixe : x*
- [ ] `time docker stop exec` : environ 1 s et message *SIGTERM reçu*
- [ ] `docker run --rm demo:entrypoint` : échec explicite sur `APP_ENV`

## Réponses aux questions

1. Quelles variables lit `app.py` et quelles valeurs prennent-elles par défaut ?
2. Tableau `ARG` / `ENV` (disponible au build, dans le conteneur, modifiable au run, visible dans l'image).
3. Pourquoi un arrêt brutal (SIGKILL) est-il dangereux pour une base de données ?
4. Quelle forme (exec ou shell) utilise le `CMD` de `app-fil-rouge` ?

> Prérequis manquant : le dossier `app-fil-rouge/` utilisé par ce TP n'est pas présent dans le dépôt du cours.

## Problèmes rencontrés

| Symptôme | Cause | Solution |
|---|---|---|
| | | |
