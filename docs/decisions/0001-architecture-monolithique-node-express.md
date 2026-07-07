# ADR-0001 : Architecture monolithique Node.js et Express

## Contexte
Le projet Sinistres Demo est une application de démonstration simple destinée à illustrer un back-office minimal de gestion de demandes sinistres.

## Décision
Le projet adopte une architecture monolithique basée sur Node.js 20 et Express 4.x avec frontend statique HTML/CSS/JS servi directement par le backend.

## Conséquences
- Simplicité de mise en œuvre et d'exécution.
- Absence de séparation frontend/backend en services distincts.
- Maintenance simplifiée pour un usage démonstration.
- Pas de build frontend ni d'ORM.