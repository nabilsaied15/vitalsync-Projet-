# 📊 Stratégie de Supervision et Orchestration - VitalSync

## 1. Outils de Supervision Préconisés
Pour une production robuste, nous recommandons la stack suivante :

| Outil | Rôle | Justification |
| :--- | :--- | :--- |
| **Prometheus** | Collecte et Stockage | Standard de facto pour K8s, extrêmement performant pour les séries temporelles. |
| **Grafana** | Visualisation | Tableaux de bord riches et alertes visuelles personnalisables. |
| **ELK (Elastic, Logstash, Kibana)** | Centralisation des logs | Indispensable pour débuguer les erreurs 5xx et tracer les requêtes. |
| **AlertManager** | Alerting | Envoi d'alertes (Slack/Email) en cas de dépassement de seuils critiques. |

## 2. Métriques Clés à Surveiller (SRE Golden Signals)
1. **CPU / Mémoire** des Pods : Pour ajuster les limites et éviter les OOM Kill (Out Of Memory).
2. **Latence API** : Temps de réponse moyen des endpoints `/api/*`.
3. **Taux d'erreur 5xx/4xx** : Pour détecter les régressions immédiates.
4. **Disponibilité (Health Check)** : Statut binaire de l'application via `/health`.

## 3. Self-Healing Kubernetes
**Que se passe-t-il si un pod Kubernetes tombe ?**
1. **Détection** : Le control plane (Kubelet via les probes) détecte que le pod ne répond plus ou a crashé.
2. **ReplicaSet** : Le ReplicaSet voit que le nombre de réplicas actuels (1) est inférieur à la cible (2).
3. **Recréation** : Kubernetes ordonne immédiatement le démarrage d'un nouveau pod sur un nœud disponible pour restaurer l'état souhaité.
4. **Auto-réparation** : Ce mécanisme est automatique et garantit une résilience sans intervention humaine.
