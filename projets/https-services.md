---
title: Mise en place de HTTPS pour mes services
description: Guide complet pour configurer HTTPS avec Pi-hole, Tailscale, et Caddy sur des services auto-hébergés.
layout: default
---

[Retour à l'accueil](/)

## Objectif
Permettre un accès sécurisé à mes services auto-hébergés via HTTPS, en simplifiant leur accès avec des noms de domaine personnalisés. Cette configuration fonctionne à la fois en local et via Tailscale.

## Aperçu des outils utilisés

- **Pi-hole** : Gère les noms de domaine locaux pour simplifier l'accès aux services via des noms faciles à retenir comme `jellyfin.home`.
- **Tailscale** : Fournit un réseau privé virtuel (VPN) pour accéder à vos services de manière sécurisée depuis l'extérieur.
- **Caddy** : Agit comme un proxy inverse et gère automatiquement les certificats HTTPS pour sécuriser vos connexions.

---

## Liste des services et ports
Voici les services hébergés sur mon serveur :

| Service     | Description                   |
|-------------|-------------------------------|
| Jellyfin    | Serveur multimédia            |
| Jellyseer   | Gestionnaire de films         |
| Filebrowser | Navigateur de fichiers        |
| qBittorrent | Gestionnaire de torrents      |
| Uptime Kuma | Surveillance des applications |
| Speedtest   | Suivi de la vitesse internet  |
| Portainer   | Gestionnaire Docker           |
| Sonarr      | Gestionnaire de séries TV     |
| Radarr      | Gestionnaire de films         |
| Bazarr      | Gestionnaire de sous-titres   |
| Prowlarr    | Indexeur de fichiers médias   |

---

## Configuration DNS dans Pi-hole

### Pourquoi ?
Utiliser des noms de domaine locaux pour accéder aux services simplifie la navigation, évite de retenir des adresses IP et permet l’accès sécurisé via HTTPS.

### Étapes :

#### Ajout des DNS locaux :
1. Dans **Pi-hole** > **Local DNS** > **DNS Records**, ajoutez un enregistrement A :
   - **Domaine** : `mediaserver.home`  
     **IP** : `[IP_LOCAL]`  

2. Dans **Pi-hole** > **Local DNS** > **CNAME Records**, ajoutez un enregistrement pour chaque service:
  - **Domain** : `jellyfin.home`
  **Target Domain** : `mediaserver.home`

> **Explication**
> Utiliser un enregistrement _CNAME_ permet de centraliser les modifications. Si l'adresse IP de votre serveur change, il vous suffit de mettre à jour le _CNAME_ au lieu de devoir modifier l'IP de chaque service individuellement.

#### Ajout des DNS Tailscale :
1. Dans **Pi-hole** > **Local DNS** > **DNS Records**, ajoutez un enregistrement A pour l’accès via Tailscale :
   - **Domaine** : `mediaserver.ts`  
     **IP** : `[IP_TAILSCALE]`  
   
2. Dans **Pi-hole** > **Local DNS** > **CNAME Records**, ajoutez un enregistrement pour chaque service:
  - **Domain** : `jellyfin.ts`
  **Target Domain** : `mediaserver.ts`

> **Remarque**
> - `[IP_LOCAL]` : Adresse IP locale de votre serveur (par exemple, `192.168.x.x`).
>- `[IP_TAILSCALE]` : Adresse IP attribuée par Tailscale (par exemple, `100.x.x.x`).

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
jellyfin.home {
    reverse_proxy [IP_LOCAL]:[PORT_JELLYFIN]
    tls internal
}

jellyfin.ts {
    reverse_proxy [IP_TAILSCALE]:[PORT_JELLYFIN]
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
- Tous les services sont accessibles via des noms de domaine personnalisés en local (`.home`) et via Tailscale (`.ts`).
- Les connexions sont sécurisées avec HTTPS, grâce à Caddy.
- Pi-hole agit comme serveur DNS principal, simplifiant la gestion des noms de domaine.
