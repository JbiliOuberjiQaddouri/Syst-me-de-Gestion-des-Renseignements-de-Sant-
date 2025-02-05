# Système de gestion des renseignements de santé

Ce projet implémente un système complet de gestion des informations de santé reposant sur une architecture microservices. Chaque service gère une fonctionnalité spécifique et interagit avec les autres via un service de découverte et un serveur de configuration centralisés.

## Table des matières

- [Description du projet](#description-du-projet)
- [Architecture](#architecture)
- [Services](#services)
- [Prérequis](#prérequis)
- [Installation et lancement](#installation-et-lancement)
- [Structure des dossiers](#structure-des-dossiers)
- [Volumes Docker](#volumes-docker)
- [Remarques](#remarques)

## Description du projet

Le projet vise à gérer divers aspects des renseignements de santé, incluant :
- La gestion des patients
- La prise de rendez-vous
- La gestion des dossiers médicaux électroniques (DME)
- La prescription de médicaments

L’authentification et la sécurisation des accès sont assurées par Keycloak.

## Architecture

L'architecture repose sur plusieurs microservices interconnectés :

- **Service de découverte (Discovery Service)** : Permet l'enregistrement et la découverte dynamique des microservices.
- **Service de configuration (Config Service)** : Fournit une configuration centralisée à tous les services.
- **Microservices métiers** :
  - **Patient Service** : Gestion des données patients.
  - **Rendezvous Service** : Gestion des rendez-vous.
  - **DME Service** : Gestion des dossiers médicaux électroniques.
  - **Prescription Service** : Gestion des prescriptions.
- **Services de bases de données** : Plusieurs conteneurs de bases de données (PostgreSQL et MariaDB) sont utilisés pour stocker les données.
- **Services complémentaires** :
  - **Keycloak** : Gestion de l’authentification.
  - **PgAdmin4 et phpMyAdmin** : Outils d'administration pour les bases de données.

## Services

### Services d'authentification
- **Keycloak Service** :  
  - Image : `postgres`  
  - Conteneur dédié pour stocker la base de données Keycloak.
  - Variables d'environnement : `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD`.
  
- **Keycloak** :  
  - Image : `quay.io/keycloak/keycloak:23.0.5`  
  - Configure Keycloak pour utiliser la base de données PostgreSQL du Keycloak Service.
  - Variables d'environnement : `KC_DB`, `KC_DB_URL`, `KC_DB_USERNAME`, `KC_DB_PASSWORD`, `KEYCLOAK_ADMIN`, `KEYCLOAK_ADMIN_PASSWORD`.

### Services de bases de données
- **Postgres Service & PgAdmin4** :  
  - PostgreSQL pour le service de rendez-vous et PgAdmin4 pour l'administration (ports `5433` et `8899` respectivement).

- **Bases de données MySQL/MariaDB** :  
  - `mysql-db-patient` pour le Patient Service (port `3307`).
  - `mysql-db-dme` pour le DME Service (port `3308`).
  - `mysql-db-prescription` pour le Prescription Service (port `3309`).
  - **phpMyAdmin** pour l'administration des bases de données MySQL (port `9990`).

### Microservices métiers
- **Discovery Service** :  
  - Construit depuis `./DiscoveryService`
  - Exposé sur le port `8761`
  - Vérifiable via l'endpoint `/actuator/health`.

- **Config Service** :  
  - Construit depuis `./ConfigService`
  - Exposé sur le port `9999`
  - Dépend du Discovery Service pour sa configuration.

- **Patient Service** :  
  - Construit depuis `./PatientsService`
  - Exposé sur le port `8080`
  - Dépend du Config Service et de la base de données `mysql-db-patient`.

- **Rendezvous Service** :  
  - Construit depuis `./Rendez-vousService`
  - Exposé sur le port `8081`
  - Dépend du Config Service et de la base de données PostgreSQL (`postgres-service`).

- **DME Service** :  
  - Construit depuis `./DME-Service`
  - Exposé sur le port `8083`
  - Dépend du Config Service et de la base de données `mysql-db-dme`.

- **Prescription Service** :  
  - Construit depuis `./PrescriptionsService`
  - Exposé sur le port `8084`
  - Dépend du Config Service et de la base de données `mysql-db-prescription`.

## Prérequis

- **Docker** : Assurez-vous que Docker est installé sur votre machine.
- **Docker Compose** : Nécessaire pour orchestrer l'ensemble des conteneurs.

## Installation et lancement

1. **Cloner le dépôt**  
   Clonez le dépôt sur votre machine :
   ```bash
   git clone [<URL_DU_DEPOT_GITHUB>](https://github.com/JbiliOuberjiQaddouri/Syst-me-de-Gestion-des-Renseignements-de-Sant-)
   cd jbiliouberjiqaddouri-syst-me-de-gestion-des-renseignements-de-sant-/


## Lancement du projet

Depuis le répertoire racine, exécutez la commande suivante :

```bash
docker-compose up --build
