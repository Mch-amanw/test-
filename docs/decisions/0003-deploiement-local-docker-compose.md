# ADR-0003 : Déploiement local via Docker Compose

## Contexte
Le projet doit être facilement exécutable en environnement de démonstration sans infrastructure cloud.

## Décision
Le projet est déployé exclusivement en local via Docker et Docker Compose.

## Conséquences
- Aucun hébergement cloud prévu.
- Environnement d'exécution reproductible.
- Démarrage simplifié via commandes Make.
- PostgreSQL et application exécutés en conteneurs.