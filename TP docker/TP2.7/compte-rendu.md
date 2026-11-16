# TP 2.7 — Réseaux et stockage en profondeur

**Énoncé :** [TP2.7_Reseaux_Volumes_avances.md](../TP2.7_Reseaux_Volumes_avances.md)
**Statut :** à faire

## Objectif

Comprendre pourquoi deux conteneurs se voient ou non (isolation 3 tiers), choisir entre volume nommé, bind mount et tmpfs, et savoir sauvegarder puis restaurer un volume.

## Fichiers produits

_À compléter : lister les fichiers créés (Dockerfile, compose.yml, scripts) et expliquer en une ligne les instructions importantes._

## Vérifications (une capture par ligne dans `captures/`)

- [ ] le proxy ne joint pas Redis (isolation démontrée)
- [ ] bind mount : le site change sans reconstruire l'image
- [ ] tmpfs vide après `docker restart`
- [ ] clé `cours` restaurée après suppression du volume

## Réponses aux questions

1. Pourquoi `ping a2` échoue-t-il sur le bridge par défaut ?
2. Quel mode de publication de port pour une base de données qui ne doit pas être exposée ?
3. Schéma des 3 conteneurs (proxy, web, redis) et des 2 réseaux (front, back).

## Problèmes rencontrés

| Symptôme | Cause | Solution |
|---|---|---|
| | | |
