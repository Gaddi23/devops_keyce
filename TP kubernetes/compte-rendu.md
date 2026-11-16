# Module 4 — Kubernetes et Ansible : TP 4.2 à 4.6

**Statut :** terminé le 16/11/2026 — tous les TP appliqués et vérifiés sur Minikube.

## Environnement

- Minikube 1.39.0, pilote Docker, Kubernetes **1.37.0**, nœud `minikube` Ready (2 CPU, 3 Go).
- `kubectl` 1.34.1.
- Démarrage : `minikube start --driver=docker --base-image=kicbase/stable:v0.0.51` (l'image de base a été téléchargée d'abord avec `docker pull`, car le téléchargement depuis `gcr.io` échouait).

## Organisation

| Dossier | TP | Fichiers |
|---|---|---|
| [`4.2-pod/`](4.2-pod/) | 4.2 Pod | `pod.yaml` |
| [`4.3-deployment-service/`](4.3-deployment-service/) | 4.3 Deployment + Service | `deployment.yaml`, `service.yaml` |
| [`4.4-webapp/`](4.4-webapp/) | 4.4 WebApp | `webapp-deployment.yaml`, `webapp-service.yaml` |
| [`4.5-4.6-wordpress/`](4.5-4.6-wordpress/) | 4.5 WordPress, 4.6 Ansible | `mysql-deployment.yaml`, `wordpress-deployment.yaml`, `wordpress-service.yaml`, `wordpress-playbook.yaml` |

## TP 4.2 — Pod

`kubectl apply -f 4.2-pod/pod.yaml` → `my-pod` **Running** (IP 10.244.0.3).
`kubectl describe pod my-pod` montre les événements : *Scheduled → Pulling → Pulled → Created → Started*.
`kubectl port-forward pod/my-pod 8080:80` → `http://localhost:8080` affiche *Welcome to nginx*.

## TP 4.3 — Deployment, Service, réplicas

`kubectl apply -f 4.3-deployment-service/` → `my-deployment` **3/3**, `my-service` NodePort `80:31796`.
- Le Service répond **200** sur le NodePort.
- **Auto-réparation :** après `kubectl delete pod my-deployment-…`, un nouveau pod est créé automatiquement et le Deployment revient à 3/3.
- Remarque : `my-pod` (TP 4.2) porte aussi le label `app: myapp`, il reçoit donc aussi du trafic du Service : les labels relient les objets, pas les noms.

## TP 4.4 — WebApp

`kubectl apply -f 4.4-webapp/` → `webapp-deployment` **2/2** avec l'image `legeni/webapp:v1`, `webapp-service` `8180:31917`.
Le Service répond **200** (`<title>Welcome</title>`). Le Service traduit le port 8180 vers le port 80 du conteneur.

## TP 4.5 — WordPress + MySQL

`kubectl apply -f mysql-deployment.yaml -f wordpress-deployment.yaml -f wordpress-service.yaml`
- PVC `mysql-pv-claim` **Bound** (2 Gi, StorageClass `standard`).
- `mysql` et `wordpress` **Running** ; la base `wordpress` existe (`SHOW DATABASES`).
- WordPress répond `302 → /wp-admin/install.php` puis affiche *WordPress › Installation* : la connexion à la base fonctionne.

**Corrections apportées à l'énoncé**

| Problème dans l'énoncé | Conséquence | Correction |
|---|---|---|
| aucun Service `mysql` | WordPress ne trouve pas sa base (*Error establishing a database connection*) | Service `mysql` (headless, port 3306) |
| PVC créé mais jamais monté | données perdues si le pod redémarre | `volumeMounts` sur `/var/lib/mysql` |
| `wordpress-service.yaml` jamais appliqué | WordPress inaccessible | ajouté à la commande `kubectl apply` |
| `mysql:5.7` | version qui n'est plus maintenue | `mysql:8.0` + `MYSQL_DATABASE=wordpress` |
| 20 Gi demandés | inutile pour un lab | 2 Gi |

## TP 4.6 — Déploiement par Ansible

Exécution depuis le conteneur `ansible` (relié au réseau `minikube`, collection `kubernetes.core` + module Python `kubernetes`) :

```
docker exec -w /tmp/wp ansible ansible-playbook wordpress-playbook.yaml
```

| Passage | Résultat | Explication |
|---|---|---|
| 1er (après suppression des ressources WordPress/MySQL) | `ok=4 changed=3 failed=0` | Ansible recrée MySQL, WordPress et le Service |
| 2e | `ok=4 changed=0 failed=0` | **idempotence** : l'état est déjà conforme, rien n'est modifié |
| contrôle du 16/11 (17 h 45) : Service `wordpress-service` supprimé | `ok=4 changed=1 failed=0` | Ansible ne recrée **que** ce qui manque, puis `changed=0` au passage suivant |

Correction : le playbook utilise `src:` au lieu de `lookup('file') + definition`, qui ne gère pas bien les fichiers à plusieurs documents (`---`). Une dernière tâche attend que WordPress soit `Available`.

## Preuves texte (journaux complets)

- [`kubectl-get-all.txt`](preuves/kubectl-get-all.txt) — état complet du cluster (nœud, pods, services, deployments, PVC)
- [`ansible-passage1-service-recree.txt`](preuves/ansible-passage1-service-recree.txt) — Service WordPress supprimé puis recréé par Ansible : `changed=1`
- [`ansible-passage2-idempotence.txt`](preuves/ansible-passage2-idempotence.txt) — second passage : `changed=0`

## Captures

**kubectl get nodes / get all : nœud Ready, 8 pods Running, 4 Deployments disponibles**

![kubectl get nodes / get all : nœud Ready, 8 pods Running, 4 Deployments disponibles](captures/01-get-nodes-get-all.png)

**ReplicaSets et kubectl describe pod my-pod**

![ReplicaSets et kubectl describe pod my-pod](captures/02-replicasets-describe-pod.png)

**TP 4.2 — my-pod recréé depuis pod.yaml**

![TP 4.2 — my-pod recréé depuis pod.yaml](captures/03-pod-recree-describe.png)

**TP 4.2 — événements du pod : Scheduled sur minikube**

![TP 4.2 — événements du pod : Scheduled sur minikube](captures/04-pod-events-scheduled.png)

**TP 4.3 — pod b4r79 supprimé, nouveau pod hd4zn créé (3 s) ; port-forward vers la WebApp**

![TP 4.3 — pod b4r79 supprimé, nouveau pod hd4zn créé (3 s) ; port-forward vers la WebApp](captures/05-auto-reparation-et-port-forward.png)

**TP 4.4 — WebApp legeni/webapp:v1 sur http://localhost:8180**

![TP 4.4 — WebApp legeni/webapp:v1 sur http://localhost:8180](captures/06-webapp-localhost-8180.png)

**TP 4.5 — port-forward vers wordpress-service**

![TP 4.5 — port-forward vers wordpress-service](captures/07-wordpress-port-forward.png)

**TP 4.5 — WordPress installé (site CC_DEVOPS) sur http://localhost:8081**

![TP 4.5 — WordPress installé (site CC_DEVOPS) sur http://localhost:8081](captures/08-wordpress-site-cc-devops.png)

**Erreurs rencontrées : syntaxe de kubectl delete, ssh absent du PATH pour minikube service**

![Erreurs rencontrées : syntaxe de kubectl delete, ssh absent du PATH pour minikube service](captures/09-erreurs-delete-et-minikube-service.png)

**Docker Desktop : conteneurs minikube, jenkins et ansible**

![Docker Desktop : conteneurs minikube, jenkins et ansible](captures/10-conteneurs-minikube-jenkins-ansible.png)

**Volumes : minikube (3,2 Go) et jenkins_home**

![Volumes : minikube (3,2 Go) et jenkins_home](captures/11-volumes-minikube-jenkins.png)

**Logs minikube : adresse du nœud 192.168.49.2**

![Logs minikube : adresse du nœud 192.168.49.2](captures/12-logs-minikube-adresse-ip.png)

**Logs minikube : démarrage des services du nœud**

![Logs minikube : démarrage des services du nœud](captures/13-logs-minikube-demarrage-systeme.png)

**Logs minikube : nœud Debian 12**

![Logs minikube : nœud Debian 12](captures/14-logs-minikube-debian.png)

## Problèmes rencontrés

| Symptôme | Cause | Solution |
|---|---|---|
| `failed to connect to the docker API at npipe:…dockerDesktopLinuxEngine` | Docker Desktop arrêté | démarrer Docker Desktop |
| `Docker Desktop ne dispose que de 1906Mo` | mémoire Docker trop faible | Settings → Resources → 6 Go |
| `TLS handshake timeout` sur `gcr.io/k8s-minikube/kicbase` | connexion instable | `docker pull kicbase/stable:v0.0.51` puis `--base-image` |
| `ImagePullBackOff` sur `mysql:8.0` | coupure réseau pendant le pull dans le cluster | `docker pull mysql:8.0` puis `minikube image load mysql:8.0` |
| cluster qui ne répond plus | disque C: plein | libérer de l'espace puis `minikube start` |
| `error: there is no need to specify a resource type…` | `kubectl delete pod pod/<nom>` : type donné deux fois | `kubectl delete pod <nom>` |
| `error starting ssh tunnel: exec: "ssh": executable file not found` | `minikube service` a besoin de ssh, absent du PATH | `kubectl port-forward svc/<service> <port>:<port>` |
| `Events: <none>` dans `describe pod` | événements effacés après 1 h | recréer le pod puis `describe` aussitôt |
