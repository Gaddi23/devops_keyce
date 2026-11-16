# TP 2.3 — Dockerfile, image, conteneur et Docker Hub (site restaurant)

**Énoncé :** [TP2.3_Dockerfile_Image_DockerHub.md](../TP2.3_Dockerfile_Image_DockerHub.md)
**Statut :** Dockerfile v1 écrit ; build, v2 et publication à documenter

## Objectif

Écrire un Dockerfile, construire l'image, comprendre ses couches, lancer le conteneur et publier l'image sur Docker Hub (compte `gaddiela`).

## Fichiers produits

- [`Dockerfile`](Dockerfile) : version 1 (Ubuntu 24.04 + nginx + clone du site restaurant).

| Instruction | Rôle | Exécutée au |
|---|---|---|
| `FROM ubuntu:24.04` | image de départ | build |
| `LABEL maintainer="Legeni"` | métadonnée | build |
| `RUN apt-get update` | met à jour la liste des paquets | build |
| `RUN apt-get install -y nginx git` | installe le serveur web et git | build |
| `RUN rm -Rf /var/www/html/*` | vide la page par défaut de nginx | build |
| `RUN git clone …/restaurant.git /var/www/html/` | copie le site dans l'image | build |
| `EXPOSE 80` | documente le port (ne le publie pas) | — |
| `ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]` | lance nginx au premier plan (PID 1) | démarrage du conteneur |

## Vérifications (une capture par ligne dans `captures/`)

- [ ] `docker images legeni_static_website` : tags `v1` et `v2`, `v2` plus légère
- [ ] `docker history legeni_static_website:v1` : une ligne par instruction
- [ ] `curl -sI http://localhost` : `HTTP/1.1 200 OK`
- [ ] page Docker Hub : tag `v2` visible sur `gaddiela/legeni_static_website`
- [ ] `docker run` après `docker rmi` : l'image est re-téléchargée et le site répond

## Réponses aux questions

1. Pourquoi `DEBIAN_FRONTEND=noninteractive` ?
2. Pourquoi nginx est-il lancé avec `daemon off;` ? Que se passerait-il sans ?
3. `EXPOSE 80` publie-t-il le port sur la machine hôte ?
4. Pourquoi faire `apt-get update` et `apt-get install` dans le même `RUN` ?
5. Différence `ENTRYPOINT` / `CMD` constatée avec `docker run --rm … cat /etc/os-release` ?

## Problèmes rencontrés

| Symptôme | Cause | Solution |
|---|---|---|
| | | |
