# Spécification technique — test ia

## Stack technique
| Couche | Technologie |
|---|---|
| Backend | Node.js 20 |
| Framework backend | Express 4.x |
| Base de données | PostgreSQL 16 |
| Frontend | HTML/CSS/JS vanilla |
| Librairie graphiques | Chart.js 4.4 (CDN) |
| Conteneurisation | Docker + Docker Compose |

## Modèle de données
### Entité `demande`
| Champ | Type | Nullable | Défaut | Description métier |
|---|---|---|---|---|
| `id` | SERIAL | NON | auto | Identifiant unique |
| `created_at` | TIMESTAMPTZ | NON | `now()` | Date de création |
| `statut` | VARCHAR(50) | NON | `'nouvelle'` | Issue / état de traitement |
| `concerne_arret_travail` | BOOLEAN | OUI | — | La demande concerne un arrêt de travail |
| `urgence` | VARCHAR(20) | OUI | — | `basse`, `normale`, `haute` |
| `sentiment` | VARCHAR(20) | OUI | — | `neutre`, `inquiet`, `urgent`, `confus` |
| `synthese_mail` | TEXT | OUI | — | Synthèse du mail reçu |
| `nom` | VARCHAR(150) | OUI | — | Nom assuré |
| `prenom` | VARCHAR(150) | OUI | — | Prénom assuré |
| `numero_adherent` | VARCHAR(50) | OUI | — | Numéro adhérent |
| `nir` | VARCHAR(20) | OUI | — | Numéro de sécurité sociale |
| `date_naissance` | DATE | OUI | — | Date de naissance |
| `date_arret_travail` | DATE | OUI | — | Date arrêt de travail |
| `email` | VARCHAR(255) | OUI | — | Email assuré |
| `telephone` | VARCHAR(30) | OUI | — | Téléphone assuré |
| `pj_presente` | BOOLEAN | OUI | — | Une PJ est-elle jointe au mail |
| `pj_est_arret_travail` | BOOLEAN | OUI | — | La PJ est-elle un certificat d'arrêt |
| `pj_type_document` | VARCHAR(255) | OUI | — | Exemple : « Certificat médical », « Facture » |
| `pj_reference_cerfa` | VARCHAR(30) | OUI | — | Exemple : `CERFA-10170` |
| `pj_medecin` | VARCHAR(150) | OUI | — | Nom médecin |
| `pj_employeur` | VARCHAR(150) | OUI | — | Nom employeur |

### Initialisation base de données
1. Exécution de `db/init.sql` avec création de table et migration idempotente.
2. Si `RUN_SEED !== 'false'` et table vide, insertion du seed avec 5 demandes de démonstration.

## API / contrats REST
### Métadonnées
#### GET `/api/meta`
Réponse HTTP `200`
```json
{
  "statuts": [{ "value": "nouvelle", "label": "Nouvelle" }],
  "urgences": ["basse", "normale", "haute"],
  "sentiments": ["neutre", "inquiet", "urgent", "confus"]
}
```

### Health check
#### GET `/health`
Réponses :
- `200`
```json
{
  "status": "ok"
}
```
- `503`
```json
{
  "status": "error",
  "message": "..."
}
```

### Statistiques dashboard
#### GET `/api/stats`
Réponse HTTP `200`
```json
{
  "total": 5,
  "par_statut": [
    {
      "statut": "demande_complete_traitee",
      "label": "...",
      "count": 1
    }
  ],
  "par_urgence": [
    {
      "urgence": "haute",
      "count": 2
    }
  ],
  "par_sentiment": [
    {
      "sentiment": "neutre",
      "count": 2
    }
  ]
}
```
Contraintes :
- `par_statut` exclut les demandes `nouvelle`.
- `par_statut` inclut les statuts absents avec `count: 0`.
- `par_urgence` et `par_sentiment` regroupent les valeurs nulles.

### Liste des demandes
#### GET `/api/demandes`
Tri : `created_at DESC, id DESC`

#### Paramètres de filtre
| Paramètre | Type | Description |
|---|---|---|
| `statut` | string | Filtre exact sur le statut |
| `q` | string | Recherche ILIKE sur nom, prénom, n° adhérent, email, synthèse mail |
| `urgence` | string | Filtre exact |
| `sentiment` | string | Filtre exact (API ; non exposé dans l'UI liste) |
| `traite` | `true`/`false` | Traitées vs en attente |
| `pj_presente` | `true`/`false` | Présence de pièce jointe |
| `concerne_arret_travail` | `true`/`false` | Filtre arrêt travail (API ; non exposé dans l'UI) |

### Détail demande
#### GET `/api/demandes/:id`
Réponses :
- `200` : objet demande.
- `404`
```json
{
  "error": "Demande introuvable."
}
```

### Création demande
#### POST `/api/demandes`
Content-Type : `application/json`

Champs modifiables :
- `statut`
- `concerne_arret_travail`
- `urgence`
- `sentiment`
- `synthese_mail`
- `nom`
- `prenom`
- `numero_adherent`
- `nir`
- `date_naissance`
- `date_arret_travail`
- `email`
- `telephone`
- `pj_presente`
- `pj_est_arret_travail`
- `pj_type_document`
- `pj_reference_cerfa`
- `pj_medecin`
- `pj_employeur`

Réponses :
- `201` : demande créée.
- `400` : statut invalide ou erreur de validation.

### Modification demande
#### PUT `/api/demandes/:id`
Content-Type : `application/json`

Comportement :
- Mise à jour partielle.
- Seuls les champs envoyés sont modifiés.

Réponses :
- `200` : demande mise à jour.
- `400` : erreur validation.
- `404` : demande introuvable.

### Suppression demande
#### DELETE `/api/demandes/:id`
Réponses :
- `204` : supprimée.
- `404` : introuvable.

## Filtres et requêtes
| Paramètre | Exposé UI | Description |
|---|---|---|
| `q` | Oui | Recherche texte multi-champs |
| `statut` | Oui | Filtre exact statut |
| `urgence` | Oui | Filtre exact urgence |
| `sentiment` | Non | Filtre exact sentiment |
| `traite` | Oui | Filtre demandes traitées ou en attente |
| `pj_presente` | Oui | Filtre présence pièce jointe |
| `concerne_arret_travail` | Non | Filtre arrêt de travail |

## Authentification et sécurité
- Aucune authentification.
- Aucune autorisation.
- Usage démonstration uniquement.
- Validation applicative des statuts et énumérations.
- Pas de contraintes base de données sur les énumérations.

## Intégrations externes
| Intégration | Usage |
|---|---|
| PostgreSQL 16 | Persistance des données |
| Docker Compose | Exécution locale |
| Chart.js CDN | Affichage des graphiques dashboard |

## Configuration et déploiement
### Variables d'environnement
| Variable | Valeur par défaut | Description |
|---|---|---|
| `PORT` | `3080` | Port HTTP de l'application |
| `DATABASE_URL` | — | Connection string PostgreSQL prioritaire |
| `POSTGRES_HOST` | `localhost` | Hôte PostgreSQL |
| `POSTGRES_PORT` | `5432` | Port PostgreSQL |
| `POSTGRES_USER` | `postgres` | Utilisateur PostgreSQL |
| `POSTGRES_PASSWORD` | `postgres` | Mot de passe PostgreSQL |
| `POSTGRES_DB` | `sinistres` | Base PostgreSQL |
| `POSTGRES_SSL` | `false` | Activer SSL |
| `RUN_SEED` | `true` | Insérer le seed si table vide |

### Ports
| Service | Port |
|---|---|
| Application HTTP | `3080` |
| PostgreSQL exposé hôte | `5436` |

### Commandes Make
| Commande | Description |
|---|---|
| `make up` | Démarrer application et PostgreSQL |
| `make down` | Arrêter les conteneurs |
| `make reset` | Supprimer volumes et redémarrer |
| `make db` | Ouvrir un shell `psql` |
| `make logs` | Afficher logs conteneurs |
| `make shell` | Ouvrir shell dans conteneur application |
| `make local` | Délégation racine vers `app/` |

### URLs locales
| Service | URL |
|---|---|
| Application | `http://localhost:3080` |
| Dashboard | `http://localhost:3080/dashboard` |

## CI/CD et environnements
- Environnement local uniquement.
- Déploiement via Docker Compose.
- Aucun environnement staging ou production défini.

## Performance, volumétrie et contraintes non fonctionnelles
| Aspect | Comportement |
|---|---|
| Architecture | Monolithique |
| Frontend | Aucun build frontend |
| ORM | Aucun ORM |
| Pagination | Non implémentée sur `GET /api/demandes` |
| Idempotence DB | `CREATE TABLE IF NOT EXISTS` et seed conditionnel |
| Dashboard | Dépendance internet pour Chart.js CDN |

## Observabilité
| Élément | Description |
|---|---|
| Logs | Logs stdout du conteneur application |
| Endpoint santé | `GET /health` |
| Vérification santé | Vérification connectivité PostgreSQL |