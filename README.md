# OSR Manager

Application full-stack de gestion OSR composée d'un frontend Angular, d'un backend ASP.NET Core et d'une base de données PostgreSQL, le tout orchestré via Docker.

---

## Stack technique

| Couche | Technologie | Version |
|---|---|---|
| Frontend | Angular | 20 |
| Backend | ASP.NET Core Web API | .NET 9 |
| Base de données | PostgreSQL | 16 |
| Serveur web | Nginx | alpine |
| Orchestration | Docker Compose | — |

---

## Structure du projet

```
OSR_manager/
├── frontend/               # Application Angular
│   ├── src/
│   │   └── app/            # Composants, routes, config
│   ├── nginx.conf          # Config Nginx (SPA + proxy API)
│   ├── Dockerfile
│   └── package.json
├── backend/                # API ASP.NET Core
│   ├── OrsManager.csproj
│   ├── Program.cs
│   ├── Dockerfile
│   └── appsettings.json
├── docker-compose.yml
└── README.md
```

---

## Frontend

**Framework :** Angular 20 — standalone components, zoneless change detection.

**Build :** `ng build` compile l'app dans `dist/osr-manager/browser/`.

**En production (Docker) :** Nginx sert les fichiers statiques et fait office de proxy pour les appels API :

- Toutes les routes → `index.html` (navigation SPA côté client)
- `/api/*` → redirigé vers le backend sur le port 8080

### Lancer en développement local

```bash
cd frontend
npm install
npm start
# → http://localhost:4200
```

---

## Backend

**Framework :** ASP.NET Core 9 Web API — minimal API.

**Documentation API :** Scalar (disponible uniquement en environnement `Development`).

**Base de données :** PostgreSQL via la variable d'environnement `ConnectionStrings__DefaultConnection`.

### Endpoints disponibles

| Méthode | Route | Description |
|---|---|---|
| GET | `/weatherforecast` | Exemple de données météo |
| GET | `/openapi/v1.json` | Schéma OpenAPI brut |
| GET | `/scalar/v1` | Interface Scalar (UI interactive) |

### Lancer en développement local

```bash
cd backend
dotnet run
# → http://localhost:8080
# → http://localhost:8080/scalar/v1
```

---

## Base de données

| Paramètre | Valeur |
|---|---|
| Image | postgres:16-alpine |
| Base | `ors_manager` |
| Utilisateur | `ors_user` |
| Mot de passe | `ors_password` |
| Port | `5432` |

---

## Lancer avec Docker

### Prérequis

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installé et démarré

### Commandes

**Démarrer le stack complet (build + run) :**
```bash
docker compose up --build
```

**Démarrer sans rebuild (si les images existent déjà) :**
```bash
docker compose up
```

**Arrêter le stack :**
```bash
docker compose down
```

**Arrêter et supprimer les volumes (reset base de données) :**
```bash
docker compose down -v
```

### Accès aux services

| Service | URL |
|---|---|
| Frontend (Angular) | http://localhost:4200 |
| Backend (API) | http://localhost:8080 |
| Scalar UI | http://localhost:8080/scalar/v1 |
| PostgreSQL | `localhost:5432` |

> **Note :** Le backend et le frontend ne démarrent qu'une fois PostgreSQL healthy.

---

## Variables d'environnement

Les variables sont définies dans `docker-compose.yml`. Pour les surcharger localement, créer un fichier `.env` à la racine :

```env
POSTGRES_DB=ors_manager
POSTGRES_USER=ors_user
POSTGRES_PASSWORD=ors_password
```
