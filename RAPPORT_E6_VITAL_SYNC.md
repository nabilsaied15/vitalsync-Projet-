# 🏥 ÉPREUVE E6 – CHAÎNE CI/CD CONTENEURISÉE - VIT_SYNC

**Module :** Projet Intégration et Déploiement Continus  
**Date :** 01 Avril 2026  
**Mention :** ANONYME  

---

## SOMMAIRE
1. [PARTIE 1 – GIT ET GESTION DE VERSIONS](#partie-1--git-et-gestion-de-versions)
2. [PARTIE 2 – CONTENEURISATION DOCKER](#partie-2--conteneurisation-docker)
3. [PARTIE 3 – PIPELINE CI/CD](#partie-3--pipeline-cicd)
4. [PARTIE 4 – ORCHESTRATION ET SUPERVISION](#partie-4--orchestration-et-supervision)
5. [PARTIE 5 – DOCUMENTATION (README)](#partie-5--documentation-readme)

---

## PARTIE 1 – GIT ET GESTION DE VERSIONS

### Exercice 1 – Initialisation et structuration du dépôt
- **Dépôt Git** : Le projet a été initialisé sur GitHub à l'adresse : https://github.com/nabilsaied15/vitalsync-Projet-.git. 
- **Justification GitHub** : Choix de GitHub pour sa simplicité d'intégration native avec GitHub Actions et sa robustesse pour le versionnement.
- **Stratégie de branches** : Utilisation de **Gitflow** avec les branches `main`, `develop` et `feature/docker-setup`.
- **Règles de protection** : La branche `main` est protégée (Push direct interdit, PR obligatoire).

#### .gitignore (Contenu complet) :
```text
# Node dependencies
node_modules/      # Trop volumineux pour être versionné
.env               # Contient des secrets sensibles (sécurité)
dist/              # Artifacts de build (générables)
.vscode/           # Paramètres IDE spécifiques (personnel)
*.log              # Logs de debug (inutiles en repo)
```

### Exercice 2 – Workflow Git et résolution de conflits
- Une modification conflictuelle a été effectuée dans `server.js` sur les branches `feature/add-endpoint` (ajout endpoint v1) et `feature/update-health` (ajout endpoint v2).
- Le conflit a été résolu en fusionnant les deux fonctionnalités manuellement.
- **Convention utilisée** : **Conventional Commits** (ex: `feat:`, `fix:`, `chore:`) pour une meilleure lisibilité de l'historique et l'automatisation future des versions.

---

## PARTIE 2 – CONTENEURISATION DOCKER

### Exercice 3 – Dockerfile du back-end
Le Dockerfile utilise une approche **Multi-stage build** :
1. **Stage Builder** : Base `node:20` pour l'installation des dépendances et l'exécution des tests Jest.
2. **Stage Production** : Base `node:20-alpine` (image ultra-légère) ne contenant que le code nécessaire au run.
- **Justification Alpine** : Réduit la taille de l'image de 80% et améliore la sécurité en minimisant la surface d'attaque.

### Exercice 4 – Dockerfile du front-end
Utilisation de l'image `nginx:stable-alpine`.
Le fichier `nginx.conf` agit comme un **Reverse Proxy** : toutes les requêtes vers `/api/` sont redirigées vers le conteneur `backend` sur le port 3000.

### Exercice 5 – Docker Compose
- **Réseau Bridge** : Un réseau `vitalsync_network` a été créé pour isoler les services du monde extérieur, tout en leur permettant de communiquer entre eux de manière sécurisée par nom de service.
- **Volumes Persistants** : Utilisés pour PostgreSQL afin que les données médicales des utilisateurs ne soient pas perdues lors d'un arrêt de conteneur.

---

## PARTIE 3 – PIPELINE CI/CD

### Exercice 6 – Configuration de la pipeline (GitHub Actions)
La pipeline est configurée dans `.github/workflows/main.yml` et comprend 3 étapes :
1. **Lint & Tests** : Installation propre et exécution de `npm test` et `eslint`.
2. **Build Docker** : Construction des images et tag avec le **Commit SHA** (pour une traçabilité parfaite).
3. **Déploiement Staging** : Simulation via docker-compose avec un **Health Check** (échec si l'API ne répond pas à `/health`).

### Exercice 7 – Gestion des secrets
- Les identifiants Docker Hub (`DOCKER_USERNAME`, `DOCKER_PASSWORD`) sont stockés dans les **GitHub Secrets**.
- **Risques des secrets en clair** : Fuite d'identifiants (vol de compte, hacking infrastructure) et manque d'immuabilité des accès.

---

## PARTIE 4 – ORCHESTRATION ET SUPERVISION

### Exercice 8 – Manifestes Kubernetes
- **Deployment** : 2 réplicas avec une `livenessProbe` sur `/health` pour le **Self-healing**.
- **Service** : `ClusterIP` pour la sécurité interne.
- **Ingress** : Exposition HTTP pour l'accès utilisateur final.
- **Secret** : Injection du mot de passe DB via les variables d'environnement sécurisées.

### Exercice 9 – Supervision
- **Outils** : Prometheus (Collecte métriques) + Grafana (Visualisation).
- **Métriques** : Latence API, Taux d'erreur 5xx, Utilisation CPU/RAM.
- **Self-healing** : Kubernetes détecte la panne via les probes et recrée automatiquement le Pod sans intervention humaine.

---

## PARTIE 5 – DOCUMENTATION (README)

### README.md
Le fichier README à la racine contient un schéma d'architecture **Mermaid**, les guides d'installation et les justifications des choix techniques.
