**Nom du document :** `SPEC-METIER-001`
**Version :** 1.0

## 1. Objectifs du Produit
Fournir une plateforme de discussion textuelle où la confidentialité est garantie mathématiquement par des problèmes de réseaux euclidiens (LWE - Learning With Errors), résistants aux attaques classiques et quantiques.

## 2. Utilisateurs Cibles
* Utilisateurs soucieux de la vie privée à long terme.
* Chercheurs en sécurité et cryptographie.
* Organisations préparant la migration PQC (Post-Quantum Cryptography).

## 3. Parcours Utilisateur (User Stories)

### US-01 : Création d'Identité (Setup)
* **En tant que** nouvel utilisateur,
* **Je veux** générer une paire de clés (Publique/Privée) basée sur les réseaux (Dilithium pour l'identité, Kyber pour le chiffrement) localement sur mon appareil,
* **Afin que** le serveur ne connaisse jamais ma clé privée.

### US-02 : Échange de Clés (Handshake)
* **En tant que** utilisateur A voulant parler à utilisateur B,
* **Je veux** récupérer la clé publique Kyber de B sur le serveur et "encapsuler" un secret partagé,
* **Afin d'** établir un canal sécurisé sans que personne d'autre ne puisse dériver ce secret.

### US-03 : Envoi de Message
* **En tant que** utilisateur,
* **Je veux** chiffrer mon message avec une clé symétrique dérivée de l'échange Kyber,
* **Afin que** le message transite sur le réseau sous forme illisible.

## 4. Contraintes Non-Fonctionnelles
* **Latence :** Les clés basées sur les réseaux sont plus grosses que les clés RSA/ECC (plusieurs Ko contre quelques octets). L'application doit gérer ce surcoût de bande passante sans ralentir l'UX.
* **Compatibilité :** Le chiffrement doit se faire côté client (navigateur). L'utilisation de WebAssembly est impérative pour les performances des calculs matriciels.

---
