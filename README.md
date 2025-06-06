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
  - [Bilan et justification des choix techniques](#bilan-et-justification-des-choix-techniques)
- [Chapitre 3 : Création des images, publication sur Docker Hub et gestion via Docker Compose](#chapitre-3--création-des-images-publication-sur-docker-hub-et-gestion-via-docker-compose)
  - [Création des images, publication sur Docker Hub](#création-des-images-publication-sur-docker-hub)
  - [Création des fichiers Docker Compose](#création-des-fichiers-docker-compose)
- [Chapitre 4 : Configuration et déploiement par différentes méthodes et démonstration](#chapitre-4--configuration-et-déploiement-par-différentes-méthodes-et-démonstration)
  - [Développement local avec Docker Compose](#développement-local-avec-docker-compose)
  - [Déploiement en production avec Docker Swarm](#déploiement-en-production-avec-docker-swarm)
- [Conclusion Générale](#conclusion-générale)

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

```bash
  touch Dockerfile
```

```dockerfile
  FROM node:20-alpine AS base
  WORKDIR /app
  COPY package*.json ./

  FROM base AS dependencies
  RUN npm ci

  FROM base AS build
  COPY --from=dependencies /app/node_modules ./node_modules
  COPY . .
  RUN npm run build

  FROM base AS development
  COPY --from=dependencies /app/node_modules ./node_modules
  COPY . .
  EXPOSE 5173 8080
  CMD ["npm", "run", "dev"]

  FROM node:20-alpine AS production
  WORKDIR /app
  ENV NODE_ENV=production
  COPY --from=build /app/dist ./dist
  COPY --from=build /app/server.cjs ./server.cjs
  COPY --from=base /app/package*.json ./
  RUN npm ci --omit=dev --prefer-offline --no-audit
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
Fichier `.dockerignore` :

```bash
  touch .dockerignore
```

```
  node_modules
  dist
```
---

- ***Création des fichiers d’environnement :***
Fichier `.env` :

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

Fichier `.env.production` :

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

**Bonnes pratiques appliquées pour la Dockerisation du Frontend :**
Dans la mise en place de la conteneurisation du frontend, on a systématiquement veillé à appliquer les bonnes pratiques suivantes :
- ***Utilisation d'un utilisateur non-root en production :***  
  Dans le Dockerfile, on a créé un utilisateur dédié (non-root) pour exécuter l'application en production. Cela permet de réduire les risques de sécurité en cas de compromission du conteneur.
- ***Gestion sécurisée des secrets et variables d’environnement :***
  Les variables d'environnement sont gérées localement, ou injectées lors du déploiement.
- ***Optimisation des images grâce à `.dockerignore` :***
  on a configuré un fichier `.dockerignore` pour exclure les dossiers et fichiers inutiles (tels que `node_modules` ou `dist`) lors de la construction de l'image Docker, ce qui permet de garder l'image légère et d'éviter d’inclure des fichiers pouvant contenir des informations sensibles.
- ***Tests des deux modes de fonctionnement :***
  on a pris soin de tester le projet à la fois en mode développement (`npm run dev`) et en mode production (`node server.cjs`) pour nous assurer du bon fonctionnement dans les deux contextes, et garantir ainsi la fiabilité du processus de build multi-stage.


### BackEnd — Préparation et Dockerisation

Pour chacun des microservices backend (`auth-service`, `order-service`, `product-service`), on a appliqué une démarche identique de préparation, de configuration et de dockerisation, en veillant à respecter les meilleures pratiques de sécurité, de performance et de maintenabilité.

1. **auth-service :**
- ***Accès au dossier du service :***
```bash
  cd services/auth-service
```

- ***Création du Dockerfile :***
```bash
  touch Dockerfile
```

```dockerfile
  FROM node:20-alpine AS base
  WORKDIR /app
  COPY package*.json ./

  FROM base AS development
  ENV NODE_ENV=development
  RUN npm install
  COPY . .
  EXPOSE 3001
  CMD ["npm", "run", "dev"]

  FROM base AS build
  ENV NODE_ENV=production
  RUN npm ci --omit=dev
  COPY . .

  FROM node:20-alpine AS production
  WORKDIR /app
  ENV NODE_ENV=production
  COPY --from=build /app ./
  RUN addgroup -S Gbakayoko && adduser -S usrBakayoko -G Gbakayoko
  USER usrBakayoko
  EXPOSE 3001
  CMD ["node", "src/app.js"]
```

***Explications :***
  - J'utilise un build multi-stage : un pour le dev, un pour le build, un pour la prod.
  - En dev : installation complète des dépendances pour le hot reload, port 3001 exposé.
  - En prod : installation uniquement des dépendances nécessaires, exécution de l'app avec un utilisateur non-root pour la sécurité.

- ***Fichiers d'environnement :***
  - `.env` (développement) :
```bash
  touch .env
```
```
  PORT=3001
  MONGODB_URI=mongodb://mongodb:27017/auth
  JWT_SECRET=efrei_super_pass
  NODE_ENV=development
```
  - `.env.production` (production) :
```bash
  touch .env.production
```
```
  PORT=3001
  MONGODB_URI=mongodb://mongodb:27017/auth
  JWT_SECRET=efrei_super_pass
  NODE_ENV=production
```
***Pourquoi ?***  
Cela me permet de séparer les variables sensibles et les configurations selon l’environnement.

---

2. **order-service :**
- ***Accès au dossier du service :***
```bash
  cd services/order-service
```
- ***Création du Dockerfile :***
```dockerfile
  FROM node:20-alpine AS base
  WORKDIR /app
  COPY package*.json ./

  FROM base AS dependencies
  RUN npm ci --omit=dev

  FROM base AS development
  ENV NODE_ENV=development
  RUN npm install
  COPY . .
  EXPOSE 3002
  CMD ["npm", "run", "dev"]

  FROM base AS build
  ENV NODE_ENV=production
  COPY --from=dependencies /app/node_modules ./node_modules
  COPY . .

  FROM node:20-alpine AS production
  WORKDIR /app
  ENV NODE_ENV=production
  COPY --from=build /app ./
  RUN addgroup -S griazaka && adduser -S riazaka -G griazaka
  USER riazaka
  EXPOSE 3002
  CMD ["node", "src/app.js"]
```

***Explications :***
  - Structure similaire : séparation dev/build/prod.
  - Je copie seulement le nécessaire en production (node_modules, src, configs), ce qui réduit la taille de l’image et le temps de build.
  - L'utilisateur non-root est utilisé ici aussi.

- ***Fichiers d'environnement :***
  - `.env` (développement) :
```bash
  touch .env
```
```
  PORT=3002
  MONGODB_URI=mongodb://mongodb:27017/orders
  JWT_SECRET=efrei_super_pass
  VITE_PRODUCT_SERVICE_URL=http://product-service:3000
  NODE_ENV=development
```
  - `.env.production` (production) :
```bash
  touch .env.production
```
```
  PORT=3002
  MONGODB_URI=mongodb://mongodb:27017/orders
  JWT_SECRET=efrei_super_pass
  VITE_PRODUCT_SERVICE_URL=http://product-service:3000
  NODE_ENV=production
```
***Pourquoi ?***  
on a inclus l’URL du product-service pour permettre l'interconnexion des microservices via les variables d’environnement.

---

3. **product-service :**
- ***Accès au dossier du service :***
```bash
  cd services/product-service
```
- ***Création du Dockerfile :***
```dockerfile
  FROM node:20-alpine AS base
  WORKDIR /app
  COPY package*.json ./

  FROM base AS dependencies
  RUN npm ci --omit=dev

  FROM base AS development
  ENV NODE_ENV=development
  RUN npm install
  COPY . .
  EXPOSE 3000
  CMD ["npm", "run", "dev"]

  FROM base AS build
  ENV NODE_ENV=production
  COPY --from=dependencies /app/node_modules ./node_modules
  COPY . .

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

### Bilan et justification des choix techniques
**Pourquoi cette organisation ?**
- ***Multi-stage builds :*** Pour optimiser la taille des images Docker, ne garder en production que le strict nécessaire, et accélérer les déploiements.
- ***Séparation dev/prod :*** Facilite le travail en équipe, les tests et le déploiement, tout en évitant les erreurs de configuration.
- ***Sécurité :*** Utilisation d'un utilisateur non-root dans toutes les images de production.
- ***Maintenabilité :*** Chaque microservice est isolé, ce qui permet de faire évoluer ou corriger un service sans impacter les autres.
- ***Environnement reproductible :*** Grâce aux fichiers `.env` spécifiques à chaque service et chaque environnement, le déploiement est fiable et les secrets sont protégés.


## Chapitre 3 : Création des images, publication sur Docker Hub et gestion via Docker Compose

### Création des images, publication sur Docker Hub
Dans cette étape, on a construit les images Docker pour chaque composant de l'application (frontend et microservices), puis je les ai taguées et poussées sur mon dépôt Docker Hub. Voici le détail de chaque étape :

---

1. **Création des images :**
Pour chaque service, on a utilisé la commande `docker build` en spécifiant plusieurs tags : un tag de version (`1.0.1`), un tag de version majeure (`1.0`) et le tag `latest`.  
Cela permet de suivre les évolutions, de revenir facilement à une version précise si besoin, et de toujours disposer d'un tag générique pour le déploiement automatique.

- ***Pour le frontend :***
```bash
  docker build -t aelwardi1/e-commerce-frontend:1.2.1 \
              -t aelwardi1/e-commerce-frontend:1.2 \
              -t aelwardi1/e-commerce-frontend:latest \
              -f ./frontend/Dockerfile --target production ./frontend
```
*Le build s'est bien déroulé, toutes les étapes ont été validées (voir logs ci-dessous pour un extrait).*

- ***Pour le product-service :***
```bash
  docker build -t aelwardi1/e-commerce-product-service:1.2.1 \
              -t aelwardi1/e-commerce-product-service:1.2 \
              -t aelwardi1/e-commerce-product-service:latest \
              -f ./services/product-service/Dockerfile --target production ./services/product-service
```
- ***Pour le auth-service :***
```bash
  docker build -t bakayoko99/e-commerce-auth-service:1.0.1 \
              -t bakayoko99/e-commerce-auth-service:1.0 \
              -t bakayoko99/e-commerce-auth-service:latest \
              -f ./services/auth-service/Dockerfile --target production ./services/auth-service
```
- ***Pour le order-service :***
```bash
  docker build -t riazaka/e-commerce-order-service:1.0.1 \
              -t riazaka/e-commerce-order-service:1.0 \
              -t riazaka/e-commerce-order-service:latest \
              -f ./services/order-service/Dockerfile --target production ./services/order-service
```

---
***Sortie et validation du build* :**
Après chaque build, Docker affiche un ensemble d'étapes validant la construction, l'assemblage des couches, le nommage et le push local de l'image.  
Extrait de logs pour le frontend :
```
  => [internal] load build definition from Dockerfile
  => [internal] load metadata for docker.io/library/node:20-alpine
  => [base 1/4] FROM docker.io/library/node:20-alpine
  => ...
  => => naming to docker.io/aelwardi1/e-commerce-frontend:1.0.1
  => => naming to docker.io/aelwardi1/e-commerce-frontend:1.0
  => => naming to docker.io/aelwardi1/e-commerce-frontend:latest
```
Les builds pour les microservices sont similaires, chaque étape étant validée et l'image étant prête à être poussée.

---

2. **Publication sur Docker Hub :**
Après avoir construit les images Docker pour le frontend et chaque microservice backend, je les ai publiées sur mon espace Docker Hub. Cette étape permet de rendre les images accessibles à toute l'équipe et de faciliter le déploiement sur n’importe quel serveur ou orchestrateur.

- ***FrontEnd :***
Pour le frontend, on a poussé trois tags différents : `1.2.1`, `1.2`, et `latest` pour suivre les versions et permettre un déploiement automatisé avec le tag `latest`.
```bash
  docker push aelwardi1/e-commerce-frontend:1.2.1
  docker push aelwardi1/e-commerce-frontend:1.2
  docker push aelwardi1/e-commerce-frontend:latest
```
- ![frontendHub](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/frontendHub.PNG)
***Extrait de sortie :***
```
  The push refers to repository [docker.io/aelwardi1/e-commerce-frontend]
  301aebe3d905: Waiting
  ...
  1.0.1: digest: sha256:483bd2808e9bf093e9eec877877bc34b2e57bc1d898a23a8ec8c7ce8daab044f size: 856
```
*Les couches déjà existantes ne sont pas repoussées, seules les nouvelles sont transférées, ce qui accélère les publications.*

---

- ***BackEnd :***
1. ***Product-Service :***
```bash
  docker push aelwardi1/e-commerce-product-service:1.2.1
  docker push aelwardi1/e-commerce-product-service:1.2
  docker push aelwardi1/e-commerce-product-service:latest
```
- ![productHub](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/productHub.PNG)
***Extrait de sortie :***
```
  The push refers to repository [docker.io/aelwardi1/e-commerce-product-service]
  e8399866283b: Pushed
  ...
  1.0.1: digest: sha256:6e0674c4e5a9439caf6a99c97317aa247f25cc817fa4ee4107bf899853fa7c7c size: 856
```
2. ***Auth-Service :***
```bash
  docker push bakayoko99/e-commerce-auth-service:1.0.1
  docker push bakayoko99/e-commerce-auth-service:1.0
  docker push bakayoko99/e-commerce-auth-service:latest
```
- ![authHub](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/authHub.png)
***Extrait de sortie :***
```
  The push refers to repository [docker.io/bakayoko99/e-commerce-auth-service]
  a282b930871b: Pushed
  ...
  1.0.1: digest: sha256:7558491194e9ee16ef6a45dff060a6bc537a7ee09cbad3cff2d96e6dd0a262be size: 856
```

3. ***Order-Service :***
```bash
  docker push riazaka/e-commerce-order-service:1.0.1
  docker push riazaka/e-commerce-order-service:1.0
  docker push riazaka/e-commerce-order-service:latest
```
- ![orderHub](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/orderHub.png)
***Extrait de sortie :***
```
  The push refers to repository [docker.io/riazaka/e-commerce-order-service]
  e61a25c39866: Waiting
  ...
  1.0.1: digest: sha256:cf3219ecee809a3d1e157cc71edbbac19b64a7c68e89bdc3682c328eceb69929 size: 856
```

---

#### Analyse et justification

- ***Pourquoi plusieurs tags ?***  
  Utiliser plusieurs tags (`<version>`, `<version majeure>`, `latest`) permet de :
  - Faciliter le rollback en cas de bug,
  - S'adapter aux différents environnements,
  - Automatiser les déploiements sur des environnements de recette ou de production.

- ***Pourquoi Docker Hub ?***  
  Docker Hub est une plateforme centralisée, fiable et largement supportée par tous les outils CI/CD et orchestrateurs. Cela évite de devoir reconstruire les images sur chaque machine et garantit une cohérence totale entre les environnements.

---

### Création des fichiers Docker Compose
Pour orchestrer l'ensemble de l'application, on a mis en place deux fichiers `docker-compose` : un pour le développement (`docker-compose.yaml`) et un pour la production (`docker-compose.prod.yaml`).  
Chacun adapte les bonnes pratiques selon l'environnement et permet un déploiement rapide, reproductible et multi-service.
- ***Fichier docker-compose.yaml (développement local) :***
Création du fichier :
```bash
  touch docker-compose.yaml
```

```docker-compose
  version: "3.9"
  services:
    mongodb:
      image: mongo:6
      container_name: mongodb
      ports:
        - "27017:27017"
      volumes:
        - mongodb_data:/data/db
      healthcheck:
        test: ["CMD", "mongosh", "--eval", "db.adminCommand('ping')"]
        interval: 10s
        timeout: 5s
        retries: 5
      networks:
        - backend
    frontend:
      build:
        context: ./frontend
        dockerfile: Dockerfile
        target: development
      container_name: frontend
      volumes:
        - ./frontend:/app
        - /app/node_modules
      ports:
        - "8080:8080"
      env_file:
        - ./frontend/.env
      depends_on:
        auth-service:
          condition: service_healthy
          restart: true
        product-service:
          condition: service_healthy
          restart: true
        order-service:
          condition: service_healthy
          restart: true
      networks:
        - frontend
        - backend
    auth-service:
      build:
        context: ./services/auth-service
        dockerfile: Dockerfile
        target: development
      container_name: auth-service
      volumes:
        - ./services/auth-service:/app
        - /app/node_modules
      ports:
        - "3001:3001"
      depends_on:
        mongodb:
          condition: service_healthy
          restart: true
      env_file:
        - ./services/auth-service/.env
      healthcheck:
        test: ["CMD", "wget", "--spider", "-q", "http://localhost:3001/api/health"] 
        interval: 1m30s
        timeout: 30s
        retries: 5
        start_period: 30s
      networks:
        - backend
    product-service:
      build:
        context: ./services/product-service
        dockerfile: Dockerfile
        target: development
      container_name: product-service
      volumes:
        - ./services/product-service:/app
        - /app/node_modules
      ports:
        - "3000:3000"
      depends_on:
        mongodb:
          condition: service_healthy
          restart: true
      env_file:
        - ./services/product-service/.env
      healthcheck:
        test: ["CMD", "wget", "--spider", "-q", "http://localhost:3000/api/health"] 
        interval: 1m30s
        timeout: 30s
        retries: 5
        start_period: 30s
      networks:
        - backend
    order-service:
      build:
        context: ./services/order-service
        dockerfile: Dockerfile
        target: development
      container_name: order-service
      ports:
        - "3002:3002"
      volumes:
        - ./services/order-service:/app
        - /app/node_modules
      depends_on:
        mongodb:
          condition: service_healthy
          restart: true
      networks:
        - backend
      env_file:
        - ./services/order-service/.env
      healthcheck:
        test: ["CMD", "wget", "--spider", "-q", "http://localhost:3002/api/health"]
        interval: 1m30s
        timeout: 30s
        retries: 5
        start_period: 30s

  networks:
    frontend:
    backend:

  volumes:
    mongodb_data:
```
***Contenu et explications :***
- ***mongodb :*** Lance le conteneur MongoDB, expose le port 27017, sauvegarde les données dans un volume dédié, et réalise un healthcheck pour garantir que la base est opérationnelle avant de lancer les autres services.
- ***frontend :*** 
  - Build à partir du dossier local (`./frontend`), en mode développement (hot-reload possible).
  - Montre le code source local à l'intérieur du conteneur (volumes) pour un développement itératif.
  - Attend que les microservices soient “healthy” avant de démarrer grâce à `depends_on`.
- ***product-service, auth-service, order-service :*** :
  - Chacun utilise le build local (mode développement), monte les sources en volume, expose son port, utilise un healthcheck et dépend de MongoDB.
- ***Réseaux et volumes :*** : 
  - Deux réseaux (frontend et backend) assurent l'isolation logique.
  - Un volume `mongodb_data` garantit la persistance des données MongoDB.

---
- ***Fichier docker-compose.prod.yaml (production/Swarm) :***
Ce fichier est destiné à l’orchestration en production, idéalement avec Docker Swarm.

```bash
  touch docker-compose.yaml
```

```docker-compose
  version: "3.9"
  services:
    mongodb:
      image: mongo:6
      container_name: mongodb
      ports:
        - "27017:27017"
      volumes:
        - mongodb_data:/data/db
      networks:
        - backend
      healthcheck:
        test: ["CMD", "mongosh", "--eval", "db.adminCommand('ping')"]
        interval: 10s
        timeout: 5s
        retries: 5

    frontend:
      image: aelwardi1/e-commerce-frontend:${IMAGE_TAG:-latest}
      deploy:
        replicas: 2
        restart_policy:
          condition: on-failure
      ports:
        - "8080:8080"
      networks:
        - frontend
        - backend
      env_file:
        - ./frontend/.env.production
      depends_on:
        - auth-service
        - product-service
        - order-service

    auth-service:
      image: bakayoko99/e-commerce-auth-service:${IMAGE_TAG:-latest}
      deploy:
        replicas: 2
        restart_policy:
          condition: on-failure
      ports:
        - "3001:3001"
      depends_on:
        - mongodb
      networks:
        - backend
      env_file:
        - ./services/auth-service/.env.production
      healthcheck:
        test: ["CMD", "wget", "--spider", "-q", "http://localhost:3001/api/health"] 
        interval: 1m30s
        timeout: 30s
        retries: 5
        start_period: 30s

    product-service:
      image: aelwardi1/e-commerce-product-service:${IMAGE_TAG:-latest}
      deploy:
        replicas: 2
        restart_policy:
          condition: on-failure
      ports:
        - "3000:3000"
      depends_on:
        - mongodb
      networks:
        - backend
      env_file:
        - ./services/product-service/.env.production
      healthcheck:
        test: ["CMD", "wget", "--spider", "-q", "http://localhost:3000/api/health"] 
        interval: 1m30s
        timeout: 30s
        retries: 5
        start_period: 30s
    
    order-service:
      image: riazaka/e-commerce-order-service:${IMAGE_TAG:-latest}
      deploy:
        replicas: 2
        restart_policy:
          condition: on-failure
      ports:
        - "3002:3002"
      depends_on:
        - mongodb
      networks:
        - backend
      env_file:
        - ./services/order-service/.env.production
      healthcheck:
        test: ["CMD", "wget", "--spider", "-q", "http://localhost:3002/api/health"] 
        interval: 1m30s
        timeout: 30s
        retries: 5
        start_period: 30s
        
  networks:
    frontend:
    backend:
  volumes:
    mongodb_data:
```
- ***mongodb :*** Même configuration que pour le dev, mais sans montage de code local.
- ***frontend, product-service, auth-service, order-service :***
  - Utilisent les images Docker publiées sur Docker Hub, identifiées par la variable `${IMAGE_TAG}` (qui peut être définie dans l'environnement ou la CI/CD).
  - Chaque service est répliqué (scalabilité) grâce à la directive `deploy.replicas`.
  - Les stratégies de redémarrage (`restart_policy`) garantissent une haute disponibilité.
  - Les `depends_on` sont simplifiés, mais chaque service dépend de MongoDB (pour les backends) et des microservices nécessaires (pour le frontend).
  - Les fichiers `.env.production` sont utilisés pour injecter les variables d’environnement adaptées à la production.
- ***Réseaux et volumes :*** : Toujours deux réseaux logiques et un volume pour MongoDB.

---

***Résumé des différences :***

| Fichier                  | Usage                 | Build local/code monté | Images Docker Hub | Scalabilité | Pour CI/CD |
|--------------------------|-----------------------|-----------------------|-------------------|-------------|------------|
| docker-compose.yaml      | Développement local   | Oui                   | Non               | Non         | Non        |
| docker-compose.prod.yaml | Production/Swarm/CI   | Non                   | Oui               | Oui         | Oui        |

---

#### Pourquoi cette organisation ?
- ***Séparation dev/prod :*** Permet de travailler confortablement en local avec du hot-reload et de déployer en prod avec des images optimisées.
- ***Santé des services :*** Les healthchecks garantissent que chaque service ne démarre que si ses dépendances sont prêtes.
- ***Scalabilité/haute dispo :*** Le mode Swarm active la réplication automatique des services critiques.
- ***Simplicité de maintenance :*** Changement de config ou montée de version = modification d'un tag ou d’un fichier, pas besoin de toucher à tout le projet.

---

## Chapitre 4 : Configuration et déploiement par différentes méthodes && Démonstration et validation du fonctionnement de la solution
Dans ce chapitre, je détaille les différentes méthodes que on a utilisées pour configurer et déployer l'application, en commençant par l'environnement de développement basé sur Docker Compose. Cette approche garantit reproductibilité, isolation des services, et rapidité de mise en route.

### Développement local avec Docker Compose

1. **Préparation de l'environnement :**
**Étapes :**
- ***Création d’un dossier de travail et clonage du dépôt :***
```bash
  mkdir test-dev
  cd test-dev
  git clone https://github.com/aelwardi/microservices-docker
  cd microservices-docker
```
- ***Installation des dépendances frontend :***
```bash
  cd frontend
  npm install
```
Si Node.js ou npm ne sont pas installés, exécuter :
```bash
  sudo apt update
  sudo apt install nodejs npm
```
Vérification des versions :
```bash
  node -v
  npm -v
```
Puis retour à la racine du projet :
```bash
  cd ..
```
- ***Démarrage de l'environnement multi-conteneurs :***
```bash
  docker compose up -d
```
***Extrait de sortie :***
```
  WARN[0000] ... the attribute `version` is obsolete, it will be ignored ...
  [+] Running 9/9
  ✔ mongodb Pulled
  ...
  ✔ Container frontend Started
```
  Docker Compose télécharge les images nécessaires, construit les services à partir des Dockerfile, crée les réseaux et volumes, puis démarre chaque conteneur.
- ***Vérification du bon démarrage des services :***
```bash
  docker ps
```
***Extrait de sortie :***
```
  CONTAINER ID   IMAGE                           COMMAND                  ...           STATUS                        PORTS                      NAMES
  8efdd974d628   microservices-docker-frontend   "docker-entrypoint.s…"   ...           Up 53 seconds                 0.0.0.0:8080->8080/tcp     frontend
  e6f96f02f4a7   microservices-docker-order...   "docker-entrypoint.s…"   ...           Up 59 seconds (healthy)       0.0.0.0:3002->3002/tcp     order-service
  2a92722b7a43   microservices-docker-auth-s...  "docker-entrypoint.s…"   ...           Up 59 seconds (healthy)       0.0.0.0:3001->3001/tcp     auth-service
  022aae6390f1   microservices-docker-product... "docker-entrypoint.s…"   ...           Up 59 seconds (healthy)       0.0.0.0:3000->3000/tcp     product-service
  28221964b957   mongo:6                         "docker-entrypoint.s…"   ...           Up About a minute (healthy)   0.0.0.0:27017->27017/tcp   mongodb
```
- ***Initialisation des données de test :***
   - Donne les droits d’exécution aux scripts :
```bash
  chmod +x scripts/*
```
   - Initialise la base de produits via le script dédié :
```bash
    ./scripts/init-products.sh
```
***Extrait de sortie :***
```
  Attente du service product...
  Création des produits...
  {"name":"Smartphone Galaxy S21", ...}
  {"name":"MacBook Pro M1", ...}
  ...
  Initialisation des produits terminée !
```

---
- ***Bilan :***
- Cette méthode permet de démarrer rapidement l'intégralité du projet sur n'importe quelle machine avec Docker installé, sans configuration manuelle des services ou des bases de données.
- L'utilisation de `docker compose` garantit l'isolation, la portabilité et la facilité de maintenance, idéale pour le développement collaboratif ou les tests d'intégration.

---

2. **Démonstration et validation du fonctionnement de la solution :**
Pour valider le bon fonctionnement de l'application, on a réalisé une démonstration complète du parcours utilisateur, depuis l'inscription jusqu'à la gestion des commandes. Voici les étapes principales, accompagnées des captures d'écran à chaque étape clé.

---
- ***Accès à l'application :***
- Récupération de l’adresse IP du serveur :
```bash
  ip a
```
- Ouverture de l’application dans le navigateur à l’adresse :  
  `http://<votre-ip-de-hosts>:8080`

---
- ***Page d'authentification :***
- La page d'authentification s'affiche.  
  Saisir un email et un mot de passe pour s'inscrire.
![Page d'authentification](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/1.PNG)

---
- ***Inscription & redirection vers les produits :***
- Après l'inscription, l'utilisateur est redirigé vers la page de liste des produits.
- Dans la console du navigateur, on voit que l'inscription a réussi et que le token est affiché.

![Redirection après inscription](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/2.PNG)

---
- ***Déconnexion et reconnexion :***
- Cliquer sur “Déconnexion” pour revenir à la page de connexion.
- Entrer à nouveau l'email et le mot de passe, puis cliquer sur “Se connecter”.

![Page de connexion](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/3.PNG)

- Après connexion, redirection vers la page des produits.  
  Dans la console, le message “Connexion réussie” et le token sont visibles.

![Connexion réussie](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/4.PNG)

---
- ***Gestion du panier :***
- Ajouter des produits dans le panier.
- Cliquer sur l'icône du panier pour consulter, modifier ou valider le panier.

![Panier](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/5.PNG)

---
- ***Passer une commande :***
- Cliquer sur “Passer la commande” puis remplir les informations de l'adresse de livraison.

![Formulaire de commande](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/6.PNG)

- Cliquer sur “Confirmer la commande”.  
  Pour voir l'historique, cliquer sur “Commandes” dans le menu.

![Historique des commandes](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/7.PNG)

---
- ***Arrêt de l'environnement :***
- Pour arrêter tous les services et nettoyer l’environnement :
```bash
  docker compose down
```

---
***Conclusion :***  
Cette démonstration prouve que l'ensemble du parcours utilisateur fonctionne, depuis l'inscription jusqu'à la création et la consultation de commandes, en passant par l'authentification, la gestion du panier et la navigation sécurisée grâce aux tokens JWT.

### Déploiement en production avec Docker Swarm

1. **Préparation de l'environnement**
Pour la production, on a choisi d'orchestrer l'ensemble des services avec **Docker Swarm**, ce qui permet de bénéficier de la haute disponibilité, de la montée en charge (scalabilité) et d’une gestion centralisée des conteneurs sur plusieurs machines si besoin.

---
***Étapes de déploiement sur Swarm :***
- ***Préparation du dossier et récupération du code :***
```bash
  mkdir test-swarm
  cd test-swarm
  git clone https://github.com/aelwardi/microservicesDocker
  cd microservices-docker
```
- ***Initialisation du cluster Swarm :***
```bash
  docker swarm init
```
***Exemple de sortie :***
```
  Swarm initialized: current node (ixc8knyzw464u8kkdju4wdpc1) is now a manager.
  To add a worker to this swarm, run the following command:
      docker swarm join --token ...
```
- ***Déploiement de la stack (multi-services) :***
```bash
  docker stack deploy -c docker-compose.prod.yaml e-commerce
```
***Extrait de sortie :***
```
  Ignoring deprecated options:
  container_name: Setting the container name is not supported.
  Creating network e-commerce_backend
  Creating network e-commerce_frontend
  Creating service e-commerce_auth-service
  ...
```
- ***Vérification des services Swarm :***
```bash
  docker stack services e-commerce
```
- ***Extrait de sortie :***
```
  ID             NAME                         MODE         REPLICAS   IMAGE  
  fcmprts94kgv   e-commerce_auth-service      replicated   2/2        aelwardi1/e-commerce-auth-service:latest      *:3001->3001/tcp
  lohr4m1kr5cg   e-commerce_frontend          replicated   2/2        aelwardi1/e-commerce-frontend:latest          *:8080->8080/tcp
  nap9o5p6tunl   e-commerce_mongodb           replicated   1/1        mongo:6                                       *:27017->27017/tcp
  zzwofmr4tahl   e-commerce_order-service     replicated   2/2        aelwardi1/e-commerce-order-service:latest     *:3002->3002/tcp
  qs6wenh1gr34   e-commerce_product-service   replicated   2/2        aelwardi1/e-commerce-product-service:latest   *:3000->3000/tcp
```
- ***Initialisation des produits en base :***
   - Donner les droits d'exécution aux scripts si nécessaire :
```bash
    chmod +x scripts/*.sh
```
   - Lancer l'initialisation des produits :
```bash
    ./scripts/init-products.sh
```
***Extrait de sortie :***
```
  Attente du service product...
  Création des produits...
  {"name":"Smartphone Galaxy S21", ...}
  ...
  Initialisation des produits terminée !
```

---
- ***Bilan et intérêts de Swarm :***

  - ***Haute disponibilité :*** Chaque microservice critique (frontend, backend) est répliqué automatiquement (ici 2 instances/services).
  - ***Gestion centralisée :*** Un simple fichier `docker-compose.prod.yaml` permet de (re)déployer, scaler, ou mettre à jour l'ensemble de l'application.
  - ***Prêt pour le multi-nœud :*** Possibilité d'ajouter facilement d'autres serveurs pour équilibrer la charge ou assurer la tolérance aux pannes.
- ***Maintenance facilitée*** : Redémarrage automatique des services défaillants, rolling updates, gestion des réseaux et des volumes.

---

***Conclusion :*** 
Grâce à Docker Swarm, le déploiement en production devient fiable, automatisé et scalable, tout en restant simple à piloter avec quelques commandes.

2. **Démonstration et validation du fonctionnement de la solution :**
Pour valider le bon fonctionnement de l'application, on a réalisé une démonstration complète du parcours utilisateur sur l'interface web, exposée par le frontend déployé sur le cluster Docker Swarm.

- ***Vérification de l'adresse IP du serveur :***
```bash
  ip a
```
  - Récupérer l’adresse IP du serveur pour accéder à l’interface web.
- ***Ouverture de l'application dans le navigateur :***
  - Accéder à l’application via :  
```
  http://<IP-OU-DOMAINE-DU-SERVEUR>:8080
```

- ***Page d'authentification :***
  - La page d'authentification s'affiche.  
     Saisir un email et un mot de passe pour s'inscrire.
  - ![image1](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/1.PNG)

- ***Inscription et redirection vers la liste des produits :***
  - Après l'inscription, une redirection s'effectue vers la liste des produits.
  - Dans la console navigateur, un message confirme la réussite de l'inscription et le token JWT est affiché.
  - ![image2](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/2.PNG)

- ***Déconnexion et reconnexion :***
  - Cliquer sur « Déconnexion », puis entrer à nouveau votre email et mot de passe, et cliquer sur « Se connecter ».
  - ![image3](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/3.PNG)

- ***Connexion et accès à la page produits :***
  - Après connexion, une redirection vers la page des produits s'effectue.
  - Dans la console, « Connexion réussie » et le token sont affichés.
  - ![image4](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/4.PNG)

- ***Ajout de produits au panier :***
  - Ajouter des produits au panier, puis cliquer sur le panier pour consulter, modifier ou valider le contenu.
  - ![image5](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/5.PNG)

- ***Passage et validation de la commande :***
  - Cliquer sur « Passer la commande », remplir l'adresse de livraison.
  - ![image6](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/6.PNG)
  - Cliquer sur « Confirmer la commande ».

- ***Consultation de l'historique des commandes :***
   - Cliquer sur « Commandes » pour consulter l'historique des commandes passées.
   - ![image7](https://github.com/aelwardi/microservicesDocker/blob/main/demonstration/7.PNG)

---

3. **Nettoyage et arrêt de la stack :**
Pour terminer proprement la démonstration, arrêter tous les services et nettoyer l'environnement :
```bash
  docker stack rm e-commerce
  docker swarm leave --force
  docker volume prune
```

---
***Conclusion :*** 
Cette démonstration valide le fonctionnement complet de la plateforme, de l'inscription à la gestion des commandes, en passant par l'authentification, l'ajout au panier et la consultation de l'historique.  
Chaque étape a été vérifiée visuellement (voir captures d'écran ci-dessus) et par les messages de succès affichés dans la console du navigateur.

## Conclusion Générale

La mise en place d’une infrastructure Docker et d’une orchestration avancée pour ce projet e-commerce démontre la capacité des technologies modernes à répondre aux exigences de rapidité, de fiabilité et d’évolutivité qui caractérisent l’informatique d’aujourd’hui. À travers l’ensemble des étapes — de la conception de l’architecture microservices à la conteneurisation, la gestion des images, l’orchestration via Docker Compose et Docker Swarm, jusqu’aux démonstrations de fonctionnement — ce travail a permis de valider les bénéfices concrets de l’approche conteneurisée et orchestrée.

Les principaux apports de cette démarche sont multiples :

- **Portabilité et reproductibilité** : Grâce à Docker, le passage d’un environnement local à un environnement de production s’effectue sans friction, avec la garantie que chaque service fonctionnera de façon identique sur n’importe quelle machine ou cloud.
- **Isolation et sécurité** : Chaque microservice, avec sa propre base de données et ses variables d’environnement, fonctionne dans un conteneur isolé, minimisant les risques d’interférence et renforçant la sécurité globale de la plateforme.
- **Scalabilité et haute disponibilité** : Avec Docker Swarm, il est possible de répliquer dynamiquement les services en fonction de la charge, assurant ainsi la résilience et la continuité de service, même en cas de panne d’un nœud.
- **Simplicité de gestion et de maintenance** : L’utilisation de fichiers de configuration standardisés (Dockerfile, .env, docker-compose) rend la maintenance, la mise à jour ou l’ajout de nouveaux services beaucoup plus aisés et moins risqués.
- **Déploiement automatisé** : L’intégration de Docker Hub et l’utilisation de tags de version permettent de contrôler précisément les versions déployées, d’automatiser les mises à jour et de revenir rapidement en arrière si nécessaire.
- **Expérience utilisateur fluide** : La démonstration du parcours utilisateur, de l’inscription à la gestion des commandes, prouve la robustesse de l’architecture mise en place, tout en assurant une navigation sécurisée et intuitive.

Au-delà de la réussite technique, ce projet illustre l’importance d’adopter des outils et des méthodes adaptés aux réalités du développement logiciel moderne : l’agilité, la modularité et l’automatisation sont désormais au cœur de toute démarche d’innovation et de transformation digitale.

En conclusion, la conteneurisation et l’orchestration avancée ne se limitent pas à simplifier le déploiement : elles ouvrent la voie à de nouvelles pratiques de développement, favorisent la collaboration, accélèrent la mise sur le marché et garantissent la pérennité des solutions dans un écosystème en perpétuelle évolution. Ce travail pose ainsi les bases solides pour l’évolution future du projet, et plus largement, pour toute organisation souhaitant tirer parti du meilleur de l’informatique cloud-native.