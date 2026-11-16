# TP 2.9 — Registre privé, tags et scan de sécurité

**Énoncé :** [TP2.9_Registre_Tags_Securite.md](../TP2.9_Registre_Tags_Securite.md)
**Statut :** à faire

## Objectif

Publier des images traçables (tag de version + `sha-<commit>`), héberger un registre privé et bloquer les images vulnérables avec Trivy et hadolint.

## Fichiers produits

_À compléter : lister les fichiers créés (Dockerfile, compose.yml, scripts) et expliquer en une ligne les instructions importantes._

## Vérifications (une capture par ligne dans `captures/`)

- [ ] image sur Docker Hub avec au moins 2 tags (`version` et `sha-…`)
- [ ] `curl localhost:5000/v2/_catalog` liste `legeni-webapp`
- [ ] rapport Trivy : aucune vulnérabilité CRITICAL corrigeable
- [ ] `hadolint` sans erreur sur le Dockerfile

## Réponses aux questions

1. Pourquoi l'image `python:3.6-alpine` est-elle à proscrire ?
2. Différence entre un tag et un digest ?

> Prérequis manquant : le dossier `app-fil-rouge/` utilisé par ce TP n'est pas présent dans le dépôt du cours.

## Problèmes rencontrés

| Symptôme | Cause | Solution |
|---|---|---|
| | | |
