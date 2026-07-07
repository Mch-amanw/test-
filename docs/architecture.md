# Architecture — test ia

## Type de dépôt
- Monorepo simple applicatif.
- Architecture monolithique.

## Structure cible
```text
sinistres-demo/
├── app/
│   ├── db/
│   ├── public/
│   ├── Dockerfile
│   └── docker-compose.yml
├── docs/
│   ├── decisions/
│   └── technical.md
├── .ai/
│   ├── agents/
│   ├── skills/
│   ├── hooks/
│   └── golden-context/
├── policies/
├── tests/
├── .github/
│   └── workflows/
├── Makefile
└── README.md
```

## Rôle des composants
| Chemin | Responsabilité |
|---|---|
| `app/` | Application monolithique Node.js |
| `app/db/` | Scripts SQL d'initialisation et seed |
| `app/public/` | Frontend statique HTML/CSS/JS |
| `app/Dockerfile` | Construction image application |
| `app/docker-compose.yml` | Orchestration locale application + PostgreSQL |
| `docs/` | Documentation projet et spécifications |
| `docs/decisions/` | Archivage des ADR et décisions structurantes |
| `docs/technical.md` | Documentation technique consolidée |
| `.ai/agents/` | Définition agents IA projet |
| `.ai/skills/` | Capacités et automatisations IA |
| `.ai/hooks/` | Hooks et automatisations d'exécution |
| `.ai/golden-context/` | Contexte partagé exécuteur-agnostique |
| `policies/` | Politiques et règles de gouvernance |
| `tests/` | Tests applicatifs |
| `.github/workflows/` | Workflows automatisés dépôt |
| `Makefile` | Commandes utilitaires racine |
| `README.md` | Documentation d'installation et usage |

## Conventions
- Architecture monolithique sans séparation frontend/backend en dépôts distincts.
- Frontend statique servi directement par l'application backend.
- Pas de build frontend.
- Pas d'ORM.
- Déploiement local via Docker Compose.
- Données de démonstration injectées conditionnellement au démarrage.

## Références
- `docs/decisions/`
- `docs/technical.md`