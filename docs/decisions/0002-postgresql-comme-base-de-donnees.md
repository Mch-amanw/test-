# ADR-0002 : Utilisation de PostgreSQL 16

## Contexte
L'application nécessite une persistance relationnelle simple pour stocker les demandes sinistres et leurs métadonnées.

## Décision
Le projet utilise PostgreSQL 16 comme base de données unique.

## Conséquences
- Persistance relationnelle centralisée.
- Compatibilité avec Docker Compose local.
- Initialisation idempotente via scripts SQL.
- Validation des énumérations réalisée côté application.