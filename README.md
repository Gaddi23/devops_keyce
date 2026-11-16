# Rendu DevOps — Tchatchoua Gaddiela (B3)

Comptes-rendus des travaux pratiques du cours *Initiation au DevOps*. Chaque dossier de TP contient les fichiers produits, un `compte-rendu.md` (objectif, fichiers, vérifications, réponses, problèmes rencontrés) et un dossier `captures/`.

| Module | TP | État | Compte-rendu |
|---|---|---|---|
| Docker | 2.2 Premiers conteneurs | **fait** — étapes 2 à 5 (Odoo : manque d'espace disque) | [TP docker/TP2.2](TP%20docker/TP2.2/compte-rendu.md) |
| Docker | 2.3 Dockerfile + Docker Hub | **fait** — v1 356 Mo, v2 294 Mo | [TP docker/TP2.3](TP%20docker/TP2.3/compte-rendu.md) |
| Docker | 2.4 Réseaux et volumes | **fait** — défi compris | [TP docker/TP2.4](TP%20docker/TP2.4/compte-rendu.md) |
| Docker | 2.5 Configuration | à faire (`app-fil-rouge/` manquant) | [TP docker/TP2.5](TP%20docker/TP2.5/compte-rendu.md) |
| Docker | 2.6 Dockerfile avancé | à faire (`app-fil-rouge/` manquant) | [TP docker/TP2.6](TP%20docker/TP2.6/compte-rendu.md) |
| Docker | 2.7 Réseaux et stockage avancés | à faire | [TP docker/TP2.7](TP%20docker/TP2.7/compte-rendu.md) |
| Docker | 2.8 Compose multi-services | à faire (`app-fil-rouge/` manquant) | [TP docker/TP2.8](TP%20docker/TP2.8/compte-rendu.md) |
| Docker | 2.9 Registre, tags, sécurité | à faire (`app-fil-rouge/` manquant) | [TP docker/TP2.9](TP%20docker/TP2.9/compte-rendu.md) |
| Docker | 2.10 Exploitation et dépannage | à faire | [TP docker/TP2.10](TP%20docker/TP2.10/compte-rendu.md) |
| Jenkins | 3.2 Build | **fait** — build #1 SUCCESS | [TP_jenkins](TP_jenkins/compte-rendu.md) |
| Jenkins | 3.3 Test d'acceptance | **fait** — HTTP 200, SUCCESS | [TP_jenkins](TP_jenkins/compte-rendu.md) |
| Jenkins | 3.4 Artefact Docker Hub | **fait** — `gaddiela/alpinehelloworld` publié | [TP_jenkins](TP_jenkins/compte-rendu.md) |
| Kubernetes | 4.2 Pod | **fait** | [TP kubernetes](TP%20kubernetes/compte-rendu.md) |
| Kubernetes | 4.3 Deployment + Service | **fait** — 3/3, auto-réparation | [TP kubernetes](TP%20kubernetes/compte-rendu.md) |
| Kubernetes | 4.4 WebApp | **fait** — 2/2 | [TP kubernetes](TP%20kubernetes/compte-rendu.md) |
| Kubernetes | 4.5 WordPress | **fait** — énoncé corrigé | [TP kubernetes](TP%20kubernetes/compte-rendu.md) |
| Ansible | 4.6 Playbook WordPress | **fait** — idempotent | [TP kubernetes](TP%20kubernetes/compte-rendu.md) |

## Fil conducteur

1. **Docker** emballe l'application dans une image qui tourne partout.
2. **Jenkins** construit, teste et publie cette image automatiquement.
3. **Kubernetes** la fait tourner en plusieurs exemplaires et la répare seule.
4. **Ansible** rejoue le déploiement en une commande.

Tout ce qui fait tourner l'application est un fichier versionné dans ce dépôt.

---

<!-- RENDU-FIN -->

# INITIATION AU DEVOPS  Par Thomas BOGNI-DANCHI

➢ SECTION1: Introduction à DevOps
    - Historique
    - Définition et Contexte
    - Principes Fondamentaux
    - Cycle de vie du DEVOPS
    - Les Outils du Devops
    - TP : Préparation de l’environnement
        ✓ Installation de WSL
        ✓ Installation de la VM et du système

➢ SECTION2: Le versioning
    - Principes de Git
    - Workflow de versionning
    - Bonnes pratiques
    - TP 
        ✓ Installation de GIT
        ✓ Création de dépôts
        ✓ Branches, merge, pull requests
        ✓ Exercices sur GitHub/GitLab
        ✓ Gestion collaborative de code source

➢ SECTION3: Conteneurisation
    - Principes des conteneurs
    - Docker : architecture et concepts
    - Comparaison machines virtuelles vs conteneurs
    - Travaux Pratiques 
        ✓ Installation de Docker
        ✓ Création de Dockerfile
        ✓ Construction et gestion d'images
        ✓ Docker Compose
        ✓ Déploiement d'applications conteneurisées

➢ SECTION4: Intégration continue
    - Gestion des erreurs et notifications
    - Concepts d'intégration continue
    - Outils : Jenkins, GitLab CI
    - Principes des pipelines CI/CD
    - Travaux Pratiques 
        ✓ Installation de Jenkins
        ✓ Configuration de Jenkins
        ✓ Création de premier pipeline
        ✓ Tests automatiques
        ✓ Déploiement automatisé

➢ SECTION5: Orchestration et gestion des configuration
    - Architecture Kubernetes
    - Orchestration de conteneurs
    - Concepts : pods, deployments, services
    - Notion sur Ansible
    - Travaux Pratiques (3h)
        ✓ Installation d'un cluster Kubernetes
        ✓ Installation et configuration de Ansible
        ✓ Déploiement d'applications
        ✓ Mise à l'échelle
        ✓ Gestion des mises à jour
        ✓ Configuration de services
        ✓ Gestion des configurations

➢ SECTION6: PROJET FINAL (Par l’étudiant)
