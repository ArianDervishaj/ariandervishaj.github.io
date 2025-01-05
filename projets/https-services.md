---
title: Ajout du HTTPS à mes services auto-hébergés
description: Guide complet pour configurer HTTPS avec Pi-hole, Tailscale, et Caddy sur des services auto-hébergés.
layout: default
---

[Retour à l'accueil](/)

# Ajout du HTTPS à mes services auto-hébergés

## Objectif
Permettre un accès sécurisé à mes services auto-hébergés via HTTPS, en simplifiant leur accès avec des noms de domaine personnalisés. Cette configuration fonctionne à la fois en local et via Tailscale.

## Aperçu des outils utilisés

- **Pi-hole** : Gère les noms de domaine locaux pour simplifier l'accès aux services via des noms faciles à retenir comme `jellyfin.local`.
- **Tailscale** : Fournit un réseau privé virtuel (VPN) pour accéder à vos services de manière sécurisée depuis l'extérieur.
- **Caddy** : Agit comme un proxy inverse et gère automatiquement les certificats HTTPS pour sécuriser vos connexions.

---

## Liste des services et ports
Voici les services hébergés sur mon serveur, avec leurs ports respectifs :

| Service     | Port | Description                   |
|-------------|------|-------------------------------|
| Jellyfin    | 8096 | Serveur multimédia            |
| Jellyseer   | 5055 | Gestionnaire de films         |
| Filebrowser | 8080 | Navigateur de fichiers        |
| qBittorrent | 8081 | Gestionnaire de torrents      |
| Uptime Kuma | 3001 | Surveillance des applications |
| Speedtest   | 8765 | Suivi de la vitesse internet  |
| Portainer   | 9000 | Gestionnaire Docker           |
| Sonarr      | 8989 | Gestionnaire de séries TV     |
| Radarr      | 7878 | Gestionnaire de films         |
| Bazarr      | 6767 | Gestionnaire de sous-titres   |
| Prowlarr    | 9696 | Indexeur de fichiers médias   |

---

## Configuration DNS dans Pi-hole

### Pourquoi ?
Utiliser des noms de domaine locaux pour accéder aux services simplifie la navigation, évite de retenir des adresses IP et permet l’accès sécurisé via HTTPS.

### Étapes :

#### Ajout des DNS locaux :
1. Dans **Pi-hole** > **Local DNS** > **DNS Records**, ajoutez un enregistrement pour chaque service :
   - **Domaine** : `jellyfin.local`  
     **IP** : `[IP_LOCAL]`  
   - **Domaine** : `jellyseer.local`  
     **IP** : `[IP_LOCAL]`  

2. Répétez l'opération pour tous les services, en utilisant leur port par défaut et l’IP locale de votre serveur.

#### Ajout des DNS Tailscale :
1. Dans **Pi-hole** > **Local DNS** > **DNS Records**, ajoutez un enregistrement DNS pour l’accès via Tailscale :
   - **Domaine** : `jellyfin.ts`  
     **IP** : `[IP_TAILSCALE]`  
   - **Domaine** : `jellyseer.ts`  
     **IP** : `[IP_TAILSCALE]`  

2. Répétez cette étape pour chaque service.

> **Remarque**
> - `[IP_LOCAL]` : Adresse IP locale de votre serveur (par exemple, `192.168.x.x`).
- `[IP_TAILSCALE]` : Adresse IP attribuée par Tailscale (par exemple, `100.x.x.x`).

---

## Installation et configuration de Caddy

### Pourquoi ?
Caddy est un serveur web qui permet d’ajouter automatiquement le HTTPS à tous les services en configurant un reverse proxy pour chacun d’eux.

### Étapes :

#### Installation de Caddy avec Docker Compose :
Ajoutez la configuration suivante à votre fichier `docker-compose.yml` dans la stack "security" sur Portainer :

```yaml
caddy:
  image: caddy:latest
  container_name: caddy
  ports:
    - "80:80"
    - "443:443"
  volumes:
    - /home/nuggets/caddy/Caddyfile:/etc/caddy/Caddyfile:ro
    - /home/nuggets/caddy/data:/data
    - /home/nuggets/caddy/config:/config
  restart: unless-stopped
```

#### Modifier le fichier `Caddyfile`

```bash
jellyfin.local {
    reverse_proxy [IP_LOCAL]:8096
    tls internal
}

jellyfin.ts {
    reverse_proxy [IP_TAILSCALE]:8096
    tls internal
}
```

## Configuration de Pi-hole comme DNS pour Tailscale

### Pourquoi ?
Pour résoudre automatiquement les noms de domaine (comme `jellyfin.ts`) vers les adresses IP Tailscale de vos services.

### Étapes :

1. **Dans l’interface Tailscale :**
   - Allez dans **DNS**.
   - Ajoutez l'IP Tailscale du pihole en tant que **Global Nameserver**.
   - Activez **Override local DNS** pour forcer l’utilisation de Pi-hole comme DNS principal.

---

## Importation du certificat racine dans les navigateurs

### Pourquoi ?
Caddy génère un certificat racine auto-signé pour les connexions HTTPS. L’importer permet d’éviter les avertissements de sécurité dans les navigateurs.

### Étapes :
1. **Téléchargez le certificat** :
   - Récupérez le fichier `caddy-root.crt` depuis le dossier `/data/caddy`.

2. **Importez-le dans vos navigateurs :**
   - **Firefox** : Paramètres → Vie privée et sécurité → Certificats → Importer.
   - **Brave** : Paramètres → Vie privée et sécurité → Gérer les certificats → Importer.

---

## Bilan

Avec cette configuration :
- Tous les services sont accessibles via des noms de domaine personnalisés en local (`.local`) et via Tailscale (`.ts`).
- Les connexions sont sécurisées avec HTTPS, grâce à Caddy.
- Pi-hole agit comme serveur DNS principal, simplifiant la gestion des noms de domaine.
