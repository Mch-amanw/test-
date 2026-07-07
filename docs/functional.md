# Spécification fonctionnelle — test ia

## Contexte et objectifs
**Sinistres Demo** est une application de démonstration ultra simple destinée à illustrer la gestion de demandes sinistres, notamment les arrêts de travail et les pièces jointes, dans un contexte d'assurance complémentaire santé.

### Objectifs
- Proposer une interface web CRUD pour consulter, créer, modifier et supprimer des demandes.
- Afficher un dashboard de répartition statistique des issues de traitement, niveaux d'urgence et sentiments.
- Fonctionner localement via Docker.

## Utilisateurs et rôles
| Rôle | Description |
|---|---|
| Utilisateur de démonstration back-office sinistres | Consulte, filtre, crée, modifie et supprime des demandes sinistres via l'interface web et consulte les statistiques du dashboard |

## Périmètre
### Inclus
- Interface web CRUD des demandes.
- Liste filtrable des demandes.
- Recherche texte multi-champs.
- Dashboard statistique.
- API REST JSON.
- Gestion des métadonnées de pièces jointes.
- Health check PostgreSQL.
- Seed automatique de données de démonstration.
- Fonctionnement local via Docker Compose.

### Exclus
- Authentification et autorisation.
- Upload réel de pièces jointes.
- Workflow automatisé (mails, transmission gestion, reconnaissance adhérent).
- Intégration avec des systèmes tiers (CRM, SI gestion, etc.).
- Déploiement cloud.

## Fonctionnalités principales
### Gestion des demandes
- Consultation de la liste des demandes.
- Consultation du détail d'une demande.
- Création d'une demande.
- Modification partielle d'une demande.
- Suppression d'une demande.
- Recherche texte sur plusieurs champs.
- Filtres multicritères.

### Dashboard statistique
- Affichage du total des demandes.
- Affichage d'un compteur par issue de traitement hors statut `nouvelle`.
- Visualisation graphique des répartitions par statut, urgence et sentiment.

### Métadonnées et référentiels
- Consultation des statuts disponibles.
- Consultation des niveaux d'urgence.
- Consultation des sentiments.

## Parcours utilisateurs clés
### Consultation et filtrage des demandes
1. L'utilisateur accède à la page liste.
2. L'utilisateur applique des filtres en temps réel.
3. Le tableau des demandes est mis à jour.
4. L'utilisateur peut modifier ou supprimer une demande.

### Création d'une demande
1. L'utilisateur ouvre le formulaire CRUD.
2. L'utilisateur renseigne les champs disponibles.
3. Si aucun statut n'est fourni, le statut `nouvelle` est appliqué.
4. La demande est enregistrée.

### Consultation du dashboard
1. L'utilisateur accède au dashboard.
2. Les KPI sont chargés.
3. Les graphiques Chart.js affichent les répartitions statistiques.

## Statuts, énumérations et libellés
### Statuts de traitement
| Code | Libellé UI | Rôle / description métier |
|---|---|---|
| `nouvelle` | Nouvelle | Demande en attente — visible en liste/CRUD uniquement |
| `demande_complete_traitee` | Demande complète traitée | Dossier complet, traité |
| `sans_piece` | Traitée sans pièce | Demande sans PJ (ex. question remboursement) |
| `mail_piece_complementaire` | Mail pièce complémentaire | PJ manquante, mail envoyé au client |
| `transmis_gestion` | Transmise à la gestion | Dossier transmis au back-office |
| `adherent_introuvable_gestion` | Adhérent introuvable — gestion | N° adhérent non reconnu, escalade gestion |

### Énumérations
| Champ | Valeurs autorisées |
|---|---|
| `urgence` | `basse`, `normale`, `haute` |
| `sentiment` | `neutre`, `inquiet`, `urgent`, `confus` |

## Règles de gestion
### Statut par défaut
- À la création, le statut par défaut est `nouvelle` si aucun statut n'est fourni.

### Validation des statuts
- Seuls les statuts suivants sont acceptés :
  - `nouvelle`
  - `demande_complete_traitee`
  - `sans_piece`
  - `mail_piece_complementaire`
  - `transmis_gestion`
  - `adherent_introuvable_gestion`
- La validation est effectuée côté API.

### Filtre `traite`
- `traite=true` exclut les demandes avec le statut `nouvelle`.
- `traite=false` retourne uniquement les demandes avec le statut `nouvelle`.

### Statistiques dashboard
- `par_statut` compte uniquement les demandes hors statut `nouvelle`.
- `par_statut` retourne un `count` à `0` pour les statuts absents.
- `par_urgence` et `par_sentiment` incluent toutes les demandes.
- Les valeurs nulles sont regroupées sous :
  - `non renseignée` pour l'urgence.
  - `non renseigné` pour le sentiment.

### Gestion des valeurs API
- Les booléens acceptent :
  - `true`
  - `false`
  - `"true"`
  - `"false"`
  - `"1"`
  - `"0"`
- Les chaînes vides sont converties en `NULL`.

### Mise à jour partielle
- Les mises à jour via `PUT /api/demandes/:id` modifient uniquement les champs présents dans la requête.

## Données de référence
### Données de démo (seed)
5 enregistrements couvrant chaque statut de traitement sauf `nouvelle` :

| Nom | Description | Statut |
|---|---|---|
| Marie Dupont | arrêt maladie complet | `demande_complete_traitee` |
| Luc Bernard | question sans PJ | `sans_piece` |
| Sophie Moreau | prolongation arrêt, PJ manquante | `mail_piece_complementaire` |
| Jean Petit | adhérent inconnu | `adherent_introuvable_gestion` |
| Claire Lemaire | transmise à la gestion | `transmis_gestion` |

## Contraintes fonctionnelles
- Application de démonstration uniquement.
- Fonctionnement local via Docker Compose.
- Aucune authentification.
- Aucun upload réel de pièces jointes.
- Pas de workflow automatisé.
- Pas d'intégration tierce.

## Critères d'acceptation globaux
- CRUD web fonctionnel.
- Dashboard accessible et alimenté.
- API REST conforme aux spécifications.
- Fonctionnement local via Docker.
- Health check opérationnel.
- Validation des statuts et filtres conforme.

## Limites connues et hors périmètre
- Pas de pagination sur `GET /api/demandes`.
- Pas de contraintes base de données sur les énumérations.
- Validation des énumérations réalisée uniquement au niveau applicatif.
- Le KPI « Total demandes traitées » du dashboard affiche en réalité le total de toutes les demandes (`stats.total`).
- Chart.js est chargé depuis un CDN et nécessite une connexion internet pour le dashboard.

## Évolutions futures
- Possibilité future de gestion des utilisateurs et rôles non incluse dans le périmètre actuel.