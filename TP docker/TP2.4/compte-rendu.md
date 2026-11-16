# TP 2.4 — Réseaux et volumes (B3_jour / B3_soir)

**Énoncé :** [TP2.4_Reseaux_Volumes.md](../TP2.4_Reseaux_Volumes.md)
**Statut :** à faire

## Objectif

Relier deux conteneurs par un réseau bridge personnalisé et leur faire partager des données, d'abord avec un volume anonyme (temporaire), puis avec un volume nommé (persistant).

## Fichiers produits

_À compléter : lister les fichiers créés (Dockerfile, compose.yml, scripts) et expliquer en une ligne les instructions importantes._

## Vérifications (une capture par ligne dans `captures/`)

- [ ] `docker exec B3_jour ping -c1 B3_soir` répond (alors qu'il échoue sur le bridge par défaut)
- [ ] `docker exec B3_soir cat /partage/fichier.txt` : *Bonjour du jour*
- [ ] `docker volume ls` après `rm -v` : le volume anonyme a disparu
- [ ] `docker run --rm -v mon_volume_persistant:/mnt alpine cat /mnt/fichier.txt` : *Bonjour*
- [ ] `SELECT * FROM eleves` après recréation de `pg` : 2 lignes

## Réponses aux questions

1. Pourquoi l'absence de résolution par nom sur le bridge par défaut est-elle un problème quand un conteneur est recréé ?
2. Tableau de synthèse :

| | Volume anonyme | Volume nommé | Bind mount |
|---|---|---|---|
| Syntaxe `-v` | `-v /chemin` | `-v nom:/chemin` | `-v /dossier/hote:/chemin` |
| Créé par | | | |
| Supprimé par `docker rm -v` | | | |
| Usage typique | | | |

## Problèmes rencontrés

| Symptôme | Cause | Solution |
|---|---|---|
| | | |
