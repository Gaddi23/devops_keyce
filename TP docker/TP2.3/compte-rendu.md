# TP 2.3 — Dockerfile, image, conteneur et Docker Hub (site restaurant)

**Énoncé :** [TP2.3_Dockerfile_Image_DockerHub.md](../TP2.3_Dockerfile_Image_DockerHub.md)
**Statut :** fait le 16/11/2026 — v1 et v2 construites et testées ; publication Docker Hub : voir étape 6
**Preuves :** [`preuves/tp2.3-sortie-commandes.txt`](preuves/tp2.3-sortie-commandes.txt) (toutes les commandes), [`preuves/build-v1.log`](preuves/build-v1.log), [`preuves/build-v2.log`](preuves/build-v2.log)

## Objectif

Écrire un Dockerfile, construire une image, comprendre ses couches et son cache, lancer un conteneur à partir de cette image, puis publier l'image sur Docker Hub pour qu'elle tourne sur n'importe quelle machine.

## Fichiers produits

- [`Dockerfile`](Dockerfile) : **v1**, Ubuntu 24.04 + nginx + clone du site restaurant.
- [`Dockerfile.v2`](Dockerfile.v2) : **v2 optimisée**, une seule couche apt avec nettoyage du cache, `--no-install-recommends`, clone sans historique et `CMD`.
- [`.dockerignore`](.dockerignore) : exclut le compte-rendu, les captures et les preuves du contexte de build.

| Instruction (v1) | Rôle | Exécutée au |
|---|---|---|
| `FROM ubuntu:24.04` | image de départ | build |
| `LABEL maintainer="Legeni"` | métadonnée | build |
| `RUN apt-get update` | met à jour la liste des paquets | build |
| `RUN apt-get install -y nginx git` | installe le serveur web et git | build |
| `RUN rm -Rf /var/www/html/*` | vide la page par défaut de nginx | build |
| `RUN git clone …/restaurant.git /var/www/html/` | copie le site **dans l'image** | build |
| `EXPOSE 80` | documente le port (ne le publie pas) | — |
| `ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]` | lance nginx au premier plan (PID 1) | démarrage du conteneur |

## Ce qui a été fait et observé

| Étape | Expérience | Résultat |
|---|---|---|
| 3 | `docker build -t legeni_static_website:v1 .` | image de **356 Mo** |
| 3 | `docker history` | une ligne par instruction : `apt-get install` **108 Mo**, `apt-get update` 55 Mo, Ubuntu de base 88 Mo, `git clone` 2,3 Mo |
| 3 | même `docker build` relancé | toutes les étapes `CACHED`, terminé en quelques secondes |
| 4 | `docker run -d -p 8090:80 legeni_static_website:v1` | `HTTP/1.1 200 OK`, `<title>Restaurant</title>` |
| 4 | `docker exec … ls /var/www/html` vs `ls` sur l'hôte | les fichiers du site sont **dans le conteneur** ; sur l'hôte, seulement le Dockerfile |
| 5 | `docker build -f Dockerfile.v2 -t …:v2 .` | image de **294 Mo** (−62 Mo), une seule couche apt de 130 Mo |
| 5 | `docker run --rm …:v2 cat /etc/os-release` | *Ubuntu 22.04.5 LTS* : `cat` **remplace** le CMD |
| 5 | `docker run --rm …:v1 cat /etc/os-release` | `nginx: invalid option: "cat"` : `cat` est **ajouté** à l'ENTRYPOINT |
| 5 | site v2 sur le port 8092 | `HTTP/1.1 200 OK`, `<title>Restaurant</title>` |

## Réponses aux questions

**1. Pourquoi `DEBIAN_FRONTEND=noninteractive` ?**
Certains paquets (comme `tzdata`) posent des questions pendant leur installation, par exemple le fuseau horaire. Pendant un `docker build`, personne ne peut répondre : le build reste bloqué. Cette variable dit à `apt` d'utiliser les réponses par défaut sans rien demander.

**2. Pourquoi nginx est-il lancé avec `daemon off;` ?**
Un conteneur vit tant que son processus principal (PID 1) vit. Par défaut, nginx passe en arrière-plan et le processus de départ se termine : Docker considère alors que le conteneur a fini (`Exited (0)`). `daemon off;` garde nginx au premier plan, comme PID 1.

**3. `EXPOSE 80` publie-t-il le port sur l'hôte ?**
Non. `EXPOSE` documente seulement le port utilisé par l'application. C'est `-p 8090:80` au `docker run` qui publie réellement le port 80 du conteneur sur le port 8090 de la machine.

**Ordre des instructions (expérience du LABEL)**
Chaque instruction crée une couche, et le cache est valable tant que l'instruction et toutes celles **au-dessus** sont identiques. Modifier le `LABEL`, placé en 2e ligne, invalide donc le cache de toutes les lignes suivantes : `apt-get update`, `install` et `git clone` sont rejoués. On place en haut ce qui change rarement (image de base, paquets système) et en bas ce qui change souvent (le code du site).
*Ce rebuild n'a pas été relancé ici : il aurait retéléchargé 160 Mo de paquets sur un disque presque plein.*

**4. Pourquoi `apt-get update` et `apt-get install` dans le même `RUN` ?**
S'ils sont séparés, la couche `apt-get update` est mise en cache. Plus tard, si l'on ajoute un paquet à l'`install`, Docker réutilise une liste de paquets périmée et l'installation peut échouer ou installer des versions obsolètes. Dans le même `RUN`, la mise à jour est toujours rejouée avec l'installation. Et le `rm -rf /var/lib/apt/lists/*` dans la même couche évite de garder ce cache dans l'image : c'est une des raisons pour lesquelles la v2 est plus légère.

**5. ENTRYPOINT ou CMD ?**
`ENTRYPOINT` définit **le programme** : les arguments du `docker run` lui sont ajoutés (`nginx cat …` → erreur). `CMD` définit **la commande par défaut** : les arguments du `docker run` la remplacent entièrement (`cat /etc/os-release` s'exécute).

## Étape 6 — Publication sur Docker Hub

Connexion par **jeton d'accès** (`docker login --password-stdin`), jamais par mot de passe ; l'image doit être préfixée par l'identifiant : `gaddiela/legeni_static_website:v2`. Résultat dans [`preuves/tp2.3-sortie-commandes.txt`](preuves/tp2.3-sortie-commandes.txt).

## Vérifications

- [x] `docker images legeni_static_website` : tags `v1` (356 Mo) et `v2` (294 Mo), v2 plus légère
- [x] `docker history legeni_static_website:v1` : une ligne par instruction
- [x] `curl -sI http://localhost:8090` : `HTTP/1.1 200 OK`
- [ ] page Docker Hub : tag `v2` visible sur `gaddiela/legeni_static_website`

## Problèmes rencontrés

| Symptôme | Cause | Solution |
|---|---|---|
| `apt-get update … exit code: 100` | coupure réseau vers les dépôts Ubuntu | relancer le build (les couches déjà faites restent en cache) |
| `RPC failed; curl 92 HTTP/2 stream 0 was not closed cleanly` pendant `git clone` | connexion instable, Git en HTTP/2 | `git -c http.version=HTTP/1.1 clone …` dans le Dockerfile v2 |
| `lookup registry-1.docker.io: no such host` au `docker login` | coupure Internet | relancer `docker login` puis `docker push` |
| Port 80 / 8081 déjà pris | autres services en cours | ports 8090 et 8092 (`-p 8090:80`) |
