# TP 2.4 — Réseaux et volumes (B3_jour / B3_soir)

**Énoncé :** [TP2.4_Reseaux_Volumes.md](../TP2.4_Reseaux_Volumes.md)
**Statut :** fait le 16/11/2026 — toutes les étapes et le défi
**Preuve :** [`preuves/tp2.4-sortie-commandes.txt`](preuves/tp2.4-sortie-commandes.txt) — sortie réelle de toutes les commandes

## Objectif

Relier deux conteneurs `B3_jour` et `B3_soir` par un réseau bridge, leur faire partager des données avec un volume temporaire puis avec un volume persistant, et comprendre ce qui survit à la suppression d'un conteneur.

## Ce qui a été fait et observé

| Étape | Expérience | Résultat |
|---|---|---|
| 1 | `ping c2` sur le bridge **par défaut** | `ping: bad address 'c2'` ; par IP (`172.17.0.3`) : ✅ |
| 2 | réseau `mon_reseau` (172.19.0.0/16), `ping B3_soir` | ✅ réponse de `172.19.0.3` : résolution par nom |
| 2 | `visiteur` sur le bridge par défaut → `network connect mon_reseau` | `bad address` avant, ✅ après connexion à chaud |
| 3 | volume **anonyme** `/partage` + `--volumes-from` | B3_soir lit *Bonjour du jour*, B3_jour lit *Bonjour du jour / Bonsoir* |
| 3 | `docker rm -f -v` | le volume `f4f0c6ef…` **disparaît** de `docker volume ls` |
| 4 | volume **nommé** `mon_volume_persistant`, B3_soir en `:ro` | lecture ✅ ; écriture : `Read-only file system` |
| 4 | `docker rm -f -v` puis nouveau conteneur | le volume existe toujours, `fichier.txt` contient *Bonjour* |
| 5 | PostgreSQL `pg` + volume `pg_data`, table `eleves` | après `rm -f pg` et recréation : **2 lignes** toujours là ; un client joint la base par le nom `pg` |
| Défi | `api` sur `backend` + `frontend`, `db` sur `backend`, `webf` sur `frontend` | `webf → api` ✅, `webf → db` ❌ `bad address`, `api → db` ✅ |

## Réponses aux questions

**1. Pourquoi l'absence de résolution par nom sur le bridge par défaut pose-t-elle problème ?**
Sur le bridge par défaut, un conteneur ne peut joindre l'autre que par son **adresse IP**. Or cette IP est attribuée à la création et **change** dès qu'un conteneur est recréé (mise à jour, redémarrage, crash). Une application configurée avec une IP casse donc à la première recréation. Un réseau personnalisé fournit un **DNS interne** (127.0.0.11) : on utilise le nom du conteneur, qui reste stable.

**2. Synthèse**

| | Volume anonyme | Volume nommé | Bind mount |
|---|---|---|---|
| Syntaxe `-v` | `-v /chemin` | `-v nom:/chemin` | `-v /dossier/hote:/chemin` |
| Créé par | Docker, nom aléatoire de 64 caractères | `docker volume create` ou au premier `run` | l'utilisateur (dossier existant de l'hôte) |
| Supprimé par `docker rm -v` | **oui** | **non** | non, c'est un dossier de l'hôte |
| Usage typique | données temporaires, partage ponctuel | **données à conserver** : bases de données, uploads | développement : modifier le code sans reconstruire l'image |

**Défi :** c'est l'**isolation 3 tiers**. La couche exposée (`webf`) ne peut jamais joindre la base (`db`). Seule l'API, placée sur les deux réseaux, fait le lien.

## Vérifications

- [x] `docker exec B3_jour ping -c1 B3_soir` répond, alors qu'il échoue sur le bridge par défaut
- [x] `docker exec B3_soir cat /partage/fichier.txt` : *Bonjour du jour*
- [x] `docker volume ls` après `rm -v` : le volume anonyme a disparu
- [x] `docker run --rm -v mon_volume_persistant:/mnt alpine cat /mnt/fichier.txt` : *Bonjour*
- [x] `SELECT * FROM eleves` après recréation de `pg` : 2 lignes

## Problèmes rencontrés

| Symptôme | Cause | Solution |
|---|---|---|
| `ping: bad address 'c2'` | conteneurs sur le bridge par défaut, sans DNS | réseau créé avec `docker network create` |
| `Read-only file system` | volume monté avec `:ro` sur B3_soir | comportement attendu : B3_soir ne peut que lire |
