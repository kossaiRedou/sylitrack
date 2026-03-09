# 🐘 SYLITRACK — Guide d'installation Docker

> Système de suivi de flotte minière — République de Guinée

---

## Prérequis

Installe ces outils sur ta machine / VPS :

```bash
# Docker
curl -fsSL https://get.docker.com | sh

# Docker Compose
sudo apt install docker-compose-plugin

# Vérifier
docker --version
docker compose version
```

---

## Installation en 5 étapes

### Étape 1 — Cloner / Copier le projet

```bash
mkdir sylitrack && cd sylitrack
# Copier tous les fichiers de configuration ici
```

### Étape 2 — Configurer les variables d'environnement

```bash
cp .env.example .env
nano .env   # ← Modifier les mots de passe !
```

⚠️ **Obligatoire :** change tous les `ChangeMe_` par de vrais mots de passe.

### Étape 3 — Démarrer tous les services

```bash
docker compose up -d
```

Tu verras :
```
✅ sylitrack_postgres  — Démarré
✅ sylitrack_redis     — Démarré
✅ sylitrack_traccar   — Démarré
✅ sylitrack_django    — Démarré
✅ sylitrack_nginx     — Démarré
```

### Étape 4 — Vérifier que tout tourne

```bash
docker compose ps
docker compose logs traccar    # Logs Traccar
docker compose logs django     # Logs Django
```

### Étape 5 — Accéder aux interfaces

| Interface | URL | Usage |
|-----------|-----|-------|
| **Traccar** (admin GPS) | `http://localhost:8082` | Configurer les boîtiers GPS |
| **SyliTrack** (Django) | `http://localhost:80` | Dashboard patron + app chauffeur |

---

## Configuration des boîtiers GPS

### Boîtier Teltonika FMB (recommandé)
```
Serveur IP   : <IP de ton serveur>
Port         : 5001
Protocole    : TCP
```

### Boîtier Concox / GT06 (courant en Guinée)
```
Serveur IP   : <IP de ton serveur>
Port         : 5013
Protocole    : TCP
```

### Application smartphone OsmAnd (test rapide)
```
URL : http://<IP>:5055/?id={device_id}&lat={lat}&lon={lon}&speed={speed}
```

---

## Commandes utiles

```bash
# Démarrer
docker compose up -d

# Arrêter
docker compose down

# Voir les logs en temps réel
docker compose logs -f

# Redémarrer un service
docker compose restart traccar

# Mettre à jour Traccar
docker compose pull traccar
docker compose up -d traccar

# Backup base de données
docker exec sylitrack_postgres pg_dump -U sylitrack sylitrack > backup.sql

# Accéder à la DB
docker exec -it sylitrack_postgres psql -U sylitrack -d sylitrack
```

---

## Architecture des services

```
┌─────────────────────────────────────────────────┐
│                  VPS / Machine locale            │
│                                                  │
│  Port 80   ──► Nginx ──► Django (Dashboard)     │
│  Port 8082 ──► Traccar (Interface GPS)          │
│  Port 5001 ──► Teltonika GPS                    │
│  Port 5013 ──► Concox GPS                       │
│  Port 5055 ──► OsmAnd (smartphone)              │
│                                                  │
│  [PostgreSQL + PostGIS]  [Redis]                │
└─────────────────────────────────────────────────┘
```

---

## Déploiement sur Railway

```bash
# Installer Railway CLI
npm install -g @railway/cli

# Login
railway login

# Créer projet
railway new sylitrack

# Déployer
railway up
```

---

## Support boîtiers GPS testés en Afrique de l'Ouest

| Marque | Modèle | Port | Dispo Guinée |
|--------|--------|------|-------------|
| Teltonika | FMB120, FMB920 | 5001 | ✅ Oui |
| Concox | GT06, GT06N | 5013 | ✅ Oui |
| Queclink | GV300, GV500 | 5004 | ✅ Oui |
| Meitrack | T366 | 5020 | ✅ Oui |
| Smartphone | OsmAnd App | 5055 | ✅ Pour tests |

---

*SYLITRACK v1.0 — La mine guinéenne sous contrôle 🐘*
