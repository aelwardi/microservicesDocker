# LA MISE EN PLACE D'UNE Infrastructure Docker et Orchestration Avancée (2025)

## Table des Matières

- [Introduction Générale](#introduction-générale)
- [Chapitre 1 : Contexte général et enjeux du projet](#chapitre-1--contexte-général-et-enjeux-du-projet)
  - [Contexte du projet](#contexte-du-projet)
  - [Microservices](#microservices)
  - [Architecture](#architecture)
- [Chapitre 2 : Création d'un Dockerfile pour chaque microservice et présentation des fichiers d'environnement](#chapitre-2--création-dun-dockerfile-pour-chaque-microservice-et-présentation-des-fichiers-denvironnement)
  - [FrontEnd — Préparation et Dockerisation](#frontend--préparation-et-dockerisation)
  - [BackEnd — Préparation et Dockerisation](#backend--préparation-et-dockerisation)

---

# LA MISE EN PLACE D'UNE Infrastructure Docker et Orchestration Avancée (2025)

---

## Introduction Générale

Depuis l'aube de l'ère numérique, les avancées technologiques n'ont cessé de transformer notre manière de travailler, d'échanger et de construire des solutions informatiques. L'arrivée d'Internet, suivie par l'explosion du Cloud et des machines virtuelles, a bouleversé les habitudes : il est devenu indispensable de choisir un environnement de travail, que ce soit sur une machine locale, à distance, ou dans le Cloud. Pour chaque nouveau projet ou application, il fallait alors configurer l'environnement, installer de nombreux outils, bibliothèques et dépendances, et veiller à la compatibilité entre les différents systèmes d'exploitation.

Cette complexité initiale a souvent été source de pertes de temps, de bugs inattendus, et de difficultés lors du déploiement ou de la montée en charge d'une application. L'arrivée des conteneurs a marqué un tournant majeur : grâce à des technologies telles que Docker, il est désormais possible d’encapsuler une application et tout son environnement dans un conteneur unique, portable et reproductible. Plus besoin de se soucier des différences entre les postes des développeurs, des serveurs de production ou des configurations spécifiques de chaque machine.

Par la suite, avec l'émergence de l'architecture microservices, le besoin de gérer plusieurs conteneurs interconnectés s'est imposé. Orchestrer ces conteneurs, assurer leur communication, leur montée en charge et leur résilience est devenu un enjeu central pour tirer parti de la flexibilité offerte par le Cloud et l'informatique moderne. Des outils comme Docker Swarm, Kubernetes ou encore les services Cloud managés sont alors apparus, permettant de piloter des applications complexes avec robustesse et efficacité.

C'est dans ce contexte d’évolution continue que s'inscrit le présent projet. Face à la nécessité de proposer une plateforme moderne, scalable et facilement déployable, il a été choisi de bâtir une architecture basée sur des microservices conteneurisés, orchestrés via Docker Swarm. Ce choix vise à simplifier la gestion, le déploiement et l'évolution de l'ensemble des services applicatifs, tout en garantissant une portabilité maximale et une maintenance facilitée.

Le rapport détaille ainsi les étapes majeures de cette démarche :
- **Chapitre 1 :** Contexte général et enjeux du projet.
- **Chapitre 2 :** Création d’un Dockerfile pour chaque microservice et présentation des fichiers d’environnement.
- **Chapitre 3 :** Création des images, publication sur Docker Hub et gestion via Docker Compose.
- **Chapitre 4 :** Configuration et déploiement par différentes méthodes && Démonstration et validation du fonctionnement de la solution.

À travers cette approche, l'objectif est d'illustrer comment la conteneurisation et l'orchestration transforment la façon de concevoir, livrer et exploiter des applications dans un monde où la rapidité, la fiabilité et la scalabilité sont devenues les maîtres-mots.

---

## Chapitre 1 : Contexte général et enjeux du projet

### Contexte du projet

Le projet est structuré autour d'une architecture microservices, permettant de séparer les différentes fonctionnalités en services indépendants. Cette approche facilite la maintenance, la scalabilité et l'évolution de l'application.

**Composants Principaux :**

- ***Frontend :*** Application utilisateur développée avec Vue.js.
- ***Backend :*** Trois microservices principaux :
  - ***Auth Service :*** Gestion de l'authentification et des utilisateurs.
  - ***Product Service :*** Gestion des produits et du panier.
  - ***Order Service :*** Gestion des commandes.
- ***Base de Données :*** Chaque microservice dispose de sa propre base de données MongoDB.
- ***Conteneurisation :*** Utilisation de Docker et Docker Compose pour la conteneurisation et l'orchestration, avec Docker Swarm pour la production.
- Les machines du projets utilisées sont toutes des **debian 12** 

### Microservices

1. **Auth Service :** Gère l'inscription, la connexion, la gestion des profils utilisateurs et l'authentification via JWT.
2. **Product Service :** Gère l'affichage des produits, les opérations sur le panier (ajout, modification, suppression).
3. **Order Service :** Gère la création et la consultation des commandes des utilisateurs.

---

### Architecture

L'architecture du projet repose sur la séparation stricte des responsabilités via le modèle microservices, chaque composant étant développé, testé, déployé et maintenu de manière indépendante. Cette organisation permet une évolution rapide, une meilleure fiabilité et une adaptabilité accrue de l'ensemble de la solution.

**Schéma global :**

```
[ Utilisateur ]
      |
      v
+--------------------+
|     Frontend       |  (Vue.js)
+--------------------+
      |
      v
+-------------------------------+
|          API Gateway          | (optionnel/server.cjs)
+-------------------------------+
      |         |         |
      v         v         v
+----------+ +----------+ +----------+
|  Auth    | | Product  | |  Order   |  (Express.js)
| Service  | | Service  | | Service  |
+----------+ +----------+ +----------+
      |         |         |
      v         v         v
+----------+ +----------+ +----------+
| MongoDB  | | MongoDB  | | MongoDB  |
+----------+ +----------+ +----------+
```

**Points clés :**
- ***Isolation :*** chaque service possède sa propre base de données MongoDB, évitant les dépendances croisées et facilitant la gestion des données.
- ***Interopérabilité :*** les échanges entre le frontend et les microservices se font via des APIs REST sécurisées (JWT).
- ***Conteneurisation :*** l'ensemble des services et bases de données sont packagés dans des conteneurs Docker, orchestrés via Docker Compose en développement et Docker Swarm en production.
- ***Scalabilité :*** chaque microservice peut être dupliqué et mis à l'échelle indépendamment selon la charge ou les besoins métiers.

Cette architecture permet de garantir à la fois robustesse, flexibilité et facilité de maintenance pour l'application e-commerce, tout en tirant parti des meilleures pratiques du Cloud moderne.

## Chapitre 2 : Création d'un Dockerfile pour chaque microservice et présentation des fichiers d'environnement

### FrontEnd — Préparation et Dockerisation

1. **Création et organisation des fichiers nécessaires :**
- ***Se placer dans le dossier du frontend :***
```bash
  cd frontend
```
- ***Création du `Dockerfile` :***
Crée un fichier `Dockerfile` et copie le contenu suivant :

```bash
  touch Dockerfile
```

```dockerfile
  # Étape 1 : Image de base pour installer les dépendances
  FROM node:20-alpine AS base
  WORKDIR /app
  COPY package*.json ./
  RUN npm ci

  # Étape 2 : Construction de l'application (build)
  FROM base AS build
  COPY . .
  RUN npm run build

  # Étape 3 : Développement (hot-reload, tests...)
  FROM base AS development
  WORKDIR /app
  COPY . .
  EXPOSE 5173 8080
  CMD ["npm", "run", "dev"]

  # Étape 4 : Production (image allégée, uniquement le build)
  FROM node:20-alpine AS production
  WORKDIR /app
  COPY --from=build /app/dist ./dist
  COPY --from=build /app/server.cjs ./server.cjs
  COPY --from=build /app/package*.json ./
  RUN npm ci --only=production
  # Création d'un utilisateur non-root par sécurité
  RUN addgroup -S gelwardi && adduser -S elwardi1 -G gelwardi && chown -R elwardi1:gelwardi /app
  USER elwardi1
  EXPOSE 8080
  CMD ["node", "server.cjs"]
```
***Explications :***
- ***base :*** installe les dépendances communes.
- ***build :*** construit l'application pour la production (`npm run build`).
- ***development :*** image pour le développement (hot-reload sur les ports 5173 ou 8080).
- ***production :*** image optimisée, ne contient que le build et le serveur Node pour servir l'app. Utilise un utilisateur non-root pour plus de sécurité.

---

- ***Création du fichier `.dockerignore` :***
Crée un fichier `.dockerignore` pour éviter de copier des fichiers inutiles dans l'image :

```bash
  touch .dockerignore
```

```
  node_modules
  dist
```
---

- ***Création des fichiers d’environnement :***
Crée un fichier `.env` pour le développement :

```bash
  touch .env
```

```
  PORT=8080
  MONGODB_URI=mongodb://mongodb:27017/ecommerce
  JWT_SECRET=efrei_super_pass
  VITE_AUTH_SERVICE_URL=http://auth-service:3001
  VITE_PRODUCT_SERVICE_URL=http://product-service:3000
  VITE_ORDER_SERVICE_URL=http://order-service:3002
  NODE_ENV=development
```

Crée un fichier `.env.production` pour la production :

```bash
  touch .env.production
```

```
  PORT=8080
  MONGODB_URI=mongodb://mongodb:27017/ecommerce
  JWT_SECRET=efrei_super_pass
  VITE_AUTH_SERVICE_URL=http://auth-service:3001
  VITE_PRODUCT_SERVICE_URL=http://product-service:3000
  VITE_ORDER_SERVICE_URL=http://order-service:3002
  NODE_ENV=production
```

***Explications :***
- Les variables `VITE_*` sont injectées côté frontend pour accéder aux microservices.
- `NODE_ENV` permet de distinguer les modes développement/production.
- Les valeurs peuvent être adaptées selon l'environnement.

---

***Résumé du process :***

| Étape                | Commande/File                   | Rôle                                                                 |
|----------------------|---------------------------------|---------------------------------------------------------------------|
| Aller dans frontend  | `cd frontend`                   | Se placer au bon endroit                                            |
| Dockerfile           | `touch Dockerfile`              | Définir la construction multi-stage pour dev/prod                   |
| .dockerignore        | `touch .dockerignore`           | Ignorer les fichiers inutiles dans l'image Docker                   |
| .env                 | `touch .env`                    | Variables d'environnement pour le développement                     |
| .env.production      | `touch .env.production`         | Variables d'environnement pour la production                        |

---

**Bonnes pratiques appliquées pour la Dockerisation du Frontend :**
Dans la mise en place de la conteneurisation du frontend, j'ai systématiquement veillé à appliquer les bonnes pratiques suivantes :
- ***Utilisation d'un utilisateur non-root en production :***  
  Dans le Dockerfile, j'ai créé un utilisateur dédié (non-root) pour exécuter l'application en production. Cela permet de réduire les risques de sécurité en cas de compromission du conteneur.
- ***Gestion sécurisée des secrets et variables d’environnement :***
  Les variables d'environnement sont gérées localement, ou injectées lors du déploiement.
- ***Optimisation des images grâce à `.dockerignore` :***
  J'ai configuré un fichier `.dockerignore` pour exclure les dossiers et fichiers inutiles (tels que `node_modules` ou `dist`) lors de la construction de l'image Docker, ce qui permet de garder l'image légère et d'éviter d’inclure des fichiers pouvant contenir des informations sensibles.
- ***Tests des deux modes de fonctionnement :***
  J'ai pris soin de tester le projet à la fois en mode développement (`npm run dev`) et en mode production (`node server.cjs`) pour m'assurer du bon fonctionnement dans les deux contextes, et garantir ainsi la fiabilité du processus de build multi-stage.

### BackEnd — Préparation et Dockerisation

Pour chacun des microservices backend (`auth-service`, `order-service`, `product-service`), j'ai appliqué une démarche identique de préparation, de configuration et de dockerisation, en veillant à respecter les meilleures pratiques de sécurité, de performance et de maintenabilité.

1. **product-service :**
- ***Accès au dossier du service :***
```bash
  cd services/product-service
```
- ***Création du Dockerfile :***
```dockerfile
  FROM node:20-alpine AS base
  WORKDIR /app
  COPY package*.json ./

  # Développement
  FROM base AS development
  ENV NODE_ENV=development
  RUN npm install
  COPY . .
  EXPOSE 3000
  CMD ["npm", "run", "dev"]

  # Build production
  FROM base AS build
  ENV NODE_ENV=production
  RUN npm ci --omit=dev
  COPY . .
  RUN npm run build || true

  # Image finale production
  FROM node:20-alpine AS production
  WORKDIR /app
  ENV NODE_ENV=production
  COPY --from=build /app ./
  RUN addgroup -S gelwardi && adduser -S elwardi1 -G gelwardi
  USER elwardi1
  EXPOSE 3000
  CMD ["node", "src/app.js"]
```

***Explications :***
- Même logique : build multi-stage, nettoyage de l'image finale, sécurité utilisateur.
- Port 3000 exposé pour ce service.

- ***Fichiers d'environnement :***
  - `.env` (développement) :
```bash
  touch .env
```
```
  PORT=3000
  MONGODB_URI=mongodb://mongodb:27017/ecommerce
  JWT_SECRET=efrei_super_pass
  NODE_ENV=development
```
  - `.env.production` (production) :
```bash
  touch .env.production
```
```
  PORT=3000
  MONGODB_URI=mongodb://mongodb:27017/ecommerce
  JWT_SECRET=efrei_super_pass
  NODE_ENV=production
```
---
