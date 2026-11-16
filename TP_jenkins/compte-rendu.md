# Module 3 — Jenkins : TP 3.2, 3.3 et 3.4

**Statut :** terminé le 16/11/2026 — les 3 jobs ont été lancés depuis Jenkins et sont en **SUCCESS**.

## Environnement

- Jenkins LTS dans un conteneur, défini par [`jenkins-install/docker-compose.yml`](../jenkins-install/docker-compose.yml) : port **8185**, socket Docker de l'hôte monté, conteneur `ansible` sur le même réseau.
- Pour que Jenkins puisse lancer `docker build`, le client Docker a été ajouté dans le conteneur et le socket ouvert à l'utilisateur `jenkins` :
  `docker exec -u root jenkins chmod 666 /var/run/docker.sock` (à refaire après chaque redémarrage de Docker Desktop).
- Plugins installés : **HTTP Request** (TP 3.3) et **docker-build-step** (TP 3.4).
- Configuration des jobs exportée dans [`jobs/`](jobs/) (`config.xml` de chaque job).

## TP 3.2 — Job `build`

**Objectif :** un job *freestyle* paramétré (`IMAGE_NAME`, `IMAGE_TAG`, `Votre_ID_Github`) qui clone `alpinehelloworld` et construit son image.

**Résultat :** `build #1` → SUCCESS.

```
Successfully built 77c13afc0e0d
Successfully tagged alpinehelloworld:latest
Finished: SUCCESS
```

## TP 3.3 — Job `test-acceptance`

**Objectif :** prouver que l'image fonctionne : lancement du conteneur sur le port 80, requête HTTP qui doit répondre **200**, puis nettoyage.

**Résultat :** `test-acceptance #1` → SUCCESS.

```
HttpMethod: GET
URL: http://172.17.0.1
Response Code: 200
Success: Status code 200 is in the accepted range: 200
Finished: SUCCESS
```

`172.17.0.1` est l'adresse de l'hôte Docker vue depuis le conteneur Jenkins : c'est là que le port 80 du conteneur de test est publié.

## TP 3.4 — Job `artefact`

**Objectif :** publier sur Docker Hub l'image qui a passé le test (`gaddiela/alpinehelloworld:latest`).

- Credential Jenkins `dockerhub` (type *Username with password*) : identifiant `gaddiela` + jeton d'accès Docker Hub *Read & Write*. Le jeton n'apparaît jamais dans le script ni dans les logs.
- Le push est fait par une étape shell `docker login --password-stdin` + `docker push`, avec le credential injecté par le plugin *Credentials Binding*.

**Résultat :** `artefact #1` → SUCCESS, image visible sur https://hub.docker.com/r/gaddiela/alpinehelloworld

```
Success: Status code 200 is in the accepted range: 200
Login Succeeded
latest: digest: sha256:77c13afc0e0d8514c63b9e8fa2b294528b24d75736328dacd0941660dbc493cf size: 1622
Finished: SUCCESS
```

## Preuves texte (journaux complets)

- [`console-build-1.txt`](preuves/console-build-1.txt) — se termine par `Successfully tagged alpinehelloworld:latest` / `Finished: SUCCESS`
- [`console-test-acceptance-1.txt`](preuves/console-test-acceptance-1.txt) — `Response Code: 200` / `Finished: SUCCESS`
- [`console-artefact-1.txt`](preuves/console-artefact-1.txt) — `Login Succeeded`, push, `Finished: SUCCESS`

## Captures

**Tableau de bord : les 4 jobs en succès**

![Tableau de bord : les 4 jobs en succès](captures/01-tableau-de-bord-tous-les-jobs.png)

**Historique : build, test-acceptance, artefact et pipeline stables**

![Historique : build, test-acceptance, artefact et pipeline stables](captures/02-historique-des-builds.png)

**TP 3.2 — job build**

![TP 3.2 — job build](captures/03-job-build.png)

**TP 3.2 — build #1 réussi (8,2 s)**

![TP 3.2 — build #1 réussi (8,2 s)](captures/04-build-1-reussi.png)

**TP 3.3 — job test-acceptance**

![TP 3.3 — job test-acceptance](captures/05-job-test-acceptance.png)

**TP 3.3 — test-acceptance #1 réussi, révision Git 9a71aa6**

![TP 3.3 — test-acceptance #1 réussi, révision Git 9a71aa6](captures/06-test-acceptance-1-reussi.png)

**TP 3.4 — artefact #1 réussi (1 min 22 s)**

![TP 3.4 — artefact #1 réussi (1 min 22 s)](captures/07-artefact-1-reussi.png)

**TP 3.4 — console : clone du dépôt alpinehelloworld**

![TP 3.4 — console : clone du dépôt alpinehelloworld](captures/08-artefact-console-clone-git.png)

**TP 3.4 — espace de travail du job (Dockerfile, webapp/)**

![TP 3.4 — espace de travail du job (Dockerfile, webapp/)](captures/09-artefact-espace-de-travail.png)

**TP 3.4 — code de l'application (app.py, tests.py, wsgi.py)**

![TP 3.4 — code de l'application (app.py, tests.py, wsgi.py)](captures/10-artefact-code-webapp.png)

**TP 3.4 — jeton jenkins-tp (Read & Write) utilisé le 16/11 à 16:24**

![TP 3.4 — jeton jenkins-tp (Read & Write) utilisé le 16/11 à 16:24](captures/11-dockerhub-jeton-jenkins-tp-utilise.png)

**Docker Hub — dépôts du compte gaddiela**

![Docker Hub — dépôts du compte gaddiela](captures/12-dockerhub-depots.png)

**Premier pipeline DEVOPS-KEYCE-CI-CD**

![Premier pipeline DEVOPS-KEYCE-CI-CD](captures/13-pipeline-devops-keyce.png)

**Pipeline DEVOPS-KEYCE-CI-CD #1 (11/11/2026)**

![Pipeline DEVOPS-KEYCE-CI-CD #1 (11/11/2026)](captures/14-pipeline-devops-keyce-1.png)

**Console du pipeline : vérification Jenkins, utilisateur jenkins, Java 21**

![Console du pipeline : vérification Jenkins, utilisateur jenkins, Java 21](captures/15-pipeline-devops-keyce-console.png)

**Logs de démarrage de Jenkins**

![Logs de démarrage de Jenkins](captures/16-logs-jenkins-demarrage.png)

**Logs du redémarrage de Jenkins**

![Logs du redémarrage de Jenkins](captures/17-logs-jenkins-redemarrage.png)

**TP 3.4 — image gaddiela/alpinehelloworld:latest publiée sur Docker Hub (25,3 Mo)**

![TP 3.4 — image publiée sur Docker Hub](captures/18-dockerhub-image-alpinehelloworld.png)

**TP 3.4 — credential dockerhub dans Jenkins, jeton masqué (gaddiela/******)**

![TP 3.4 — credential dockerhub](captures/19-jenkins-credential-dockerhub.png)

## Problèmes rencontrés

| Symptôme | Cause | Solution |
|---|---|---|
| `docker: not found` dans le conteneur Jenkins | l'image Jenkins ne contient pas le client Docker | client Docker copié dans `/usr/local/bin` du conteneur |
| `permission denied … /var/run/docker.sock` | socket réservé à root | `chmod 666 /var/run/docker.sock` dans le conteneur |
| Lancement de job refusé par l'API | protection CSRF de Jenkins | utiliser le jeton *crumb* (ou lancer depuis l'interface) |
| Jeton Docker Hub en *Public Repo Read-only* | mauvais droits à la création | régénérer le jeton en **Read & Write** |
