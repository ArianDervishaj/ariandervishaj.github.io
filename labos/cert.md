---
title: TP - Certifications
description: Création et configuration de certificats pour un serveur Apache sécurisé avec TLS.
layout: default
---

[Retour à l'accueil](/)

# TP - Certifications

Ce projet explore la création et la gestion de certificats numériques pour sécuriser les communications via le protocole TLS. Il comprend la configuration d'une autorité de certification (CA) et l'installation d'un serveur Apache configuré pour HTTPS.

---

## Objectif du projet

L'objectif est de comprendre et de mettre en œuvre :
- La génération de clés privées et publiques.
- La création et la gestion d'une autorité de certification (CA).
- L'auto-signature de certificats serveur.
- La configuration et l'utilisation du protocole HTTPS sur un serveur Apache.

---

## Étape 1 - Création des certificats

### Création du certificat serveur

- Générer une clé privée :  
  ```bash
  openssl genrsa 2048 > fichier.key
  ```

  **Résultat** : Une clé privée est générée et stockée dans `fichier.key`.  
  ![Clé privée](/assets/cert/priv_key.png)

- Générer une clé privée chiffrée :  
  ```bash
  openssl genrsa -aes256 2048 > fichier_encrypted.key
  ```

  **Résultat** : Une clé privée chiffrée avec `aes256` est créée. Une passphrase est requise.  
  ![Clé chiffrée](/assets/cert/encrypt_priv.png)

- Protéger la clé privée :  
  ```bash
  chmod 600 fichier_encrypted.key
  ```  
  **Explication** : Les droits `600` permettent au propriétaire de lire et écrire le fichier, mais empêchent l'accès à d'autres utilisateurs.

### Création d’un fichier de demande de signature de certificat (CSR)

- Générer une demande de signature de certificat (CSR) :  
  ```bash
  openssl req -new -key fichier.key -config serv.conf > fichier.csr
  ```

  **Effet** : Produit un fichier CSR (`fichier.csr`) à envoyer à une autorité de certification (ou à signer soi-même).  
  ![CSR](/assets/cert/ca_csr.png)

**Alternative** : Dans un contexte réel, le fichier CSR serait envoyé à une autorité de certification externe.

---

## Étape 2 - Création de l'autorité de certification (CA)

- Générer une clé privée pour la CA :  
  ```bash
  openssl genrsa -aes256 2048 > ca.key
  ```

- Créer un certificat pour la CA :  
  ```bash
  openssl req -new -x509 -days 365 -key ca.key > ca.crt
  ```

  **Résultat** : Un certificat CA (`ca.crt`) auto-signé est créé. La passphrase est requise.  
  ![Certificat CA](/assets/cert/ca_csr.png)

---

## Étape 3 - Signature du certificat serveur

- Signer le certificat serveur avec la CA :  
  ```bash
  openssl x509 -req -in fichier.csr -out fichier.crt -CA ca.crt -CAkey ca.key -CAcreateserial -CAserial ca.srl -extensions req_ext -extfile serv.conf
  ```

  **Options importantes** :  
  - `CAcreateserial` : Génère un numéro de série unique pour le certificat signé.  
  - **Sortie** : Produit le fichier `fichier.crt`, un certificat signé par la CA.  
  ![Certificat signé](/assets/cert/fichier_crt.png)

## Étape 4 - Installation et configuration d’Apache2

1. Activer le module SSL :  
   ```bash
   a2enmod ssl
   ```  
   **Effet** : Active le module `ssl_module`, permettant à Apache d'utiliser HTTPS.

2. Vérifier la configuration du port HTTPS :  
   - Dans le fichier `ports.conf`, le port 443 doit être en écoute.  
     ![Ports.conf](/assets/cert/ports_conf.png)

   - Le port 443 est utilisé par le protocole HTTPS. On peut vérifier qu'il est en écoute avec la commande `ss`.  
     ![Port 443](/assets/cert/ss.png)

3. Configuration du fichier **VirtualHost** :
   - Ajouter la ligne :  
     `ServerName example.com`  
     **Explication** : Spécifie le nom de domaine utilisé pour les connexions HTTPS.

   - Modifier `<VirtualHost _default_:443>` en `<VirtualHost *:443>` :  
     **Effet** : Permet à Apache d'accepter les connexions HTTPS sur toutes les interfaces réseau.

---

## Étape 5 - Phase de test

### Résultat final

Le serveur HTTPS est fonctionnel et accessible via le navigateur avec un certificat signé par la CA locale.  
![Serveur HTTPS](/assets/cert/server.png)

---

## Résultats et apprentissages

Ce laboratoire a permis de :
- Comprendre les concepts de base des certificats et de la cryptographie.
- Mettre en œuvre une autorité de certification locale.
- Configurer un serveur Apache sécurisé avec HTTPS.
- Explorer le protocole TLS et son fonctionnement.

---
