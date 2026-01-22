**Nom du document :** `SPEC-TECH-001`
**Stack :** Python (FastAPI) / React / liboqs

## 1. Architecture Globale
Architecture **Client-Serveur** classique, mais le serveur agit uniquement comme un "Passe-Plat" (Relay) et un annuaire de clés publiques.

### A. Le Backend (Python/FastAPI)
Il ne fait **aucune opération cryptographique** (pour garantir le principe de bout en bout).
* **Rôle :**
    * Stocker les `PublicKeys` (Kyber + Dilithium).
    * Router les messages chiffrés via WebSockets.
* **Base de données :** Redis (pour la vitesse et l'éphémère) ou PostgreSQL.

### B. Le Frontend (React + WASM)
C'est ici que réside l'intelligence cryptographique.
* **Librairie Crypto :** Utilisation de **`liboqs-wasm`** (bindings JS pour Open Quantum Safe).
* **Logique :**
    1.  Génération des clés au lancement.
    2.  KEM (Key Encapsulation Mechanism) pour générer le secret partagé.
    3.  Chiffrement hybride : Une fois le secret partagé établi via Kyber, on utilise **AES-256-GCM** ou **ChaCha20-Poly1305** pour chiffrer le flux de messages (beaucoup plus rapide pour les données volumineuses).

## 2. Workflow Cryptographique (Protocole)

C'est le cœur du système. Nous utilisons un mécanisme **KEM (Key Encapsulation Mechanism)** au lieu du Diffie-Hellman classique.

**Scénario : Alice veut parler à Bob.**

1.  **Inscription (Bob) :**
    * Bob génère une paire de clés Kyber-768 : `(pk_Bob, sk_Bob)`.
    * Bob envoie `pk_Bob` au serveur.

2.  **Encapsulation (Alice) :**
    * Alice récupère `pk_Bob` depuis le serveur.
    * Alice utilise la fonction `Encapsulate(pk_Bob)` de Kyber.
    * Cela produit deux choses :
        * Un **Secret Partagé (SS)** (que Alice garde).
        * Un **Cryptogramme (C)** (le "ciphertext" de l'encapsulation).

3.  **Transmission :**
    * Alice envoie le `Cryptogramme (C)` à Bob via le serveur.

4.  **Décapsulation (Bob) :**
    * Bob reçoit `C`.
    * Bob utilise `Decapsulate(C, sk_Bob)`.
    * Mathématiquement, cela permet à Bob de retrouver exactement le même **Secret Partagé (SS)** qu'Alice.

5.  **Transport :**
    * Alice et Bob utilisent maintenant `SS` comme clé pour AES-256 afin de chiffrer leurs messages "Bonjour", "Ça va ?", etc.

## 3. Structures de Données (JSON)

**Objet : UserRegistration**
```json
{
  "username": "alice",
  "public_key_kyber": "base64_blob_...",  // ~1184 bytes pour Kyber768
  "public_key_dilithium": "base64_blob_..." // Pour vérifier que c'est bien Alice
}
```
Objet : Message
```json
{
  "sender": "alice",
  "recipient": "bob",
  "kem_ciphertext": "base64_blob_...", // Optionnel, envoyé seulement au début de session
  "encrypted_content": "base64_blob_...", // Le texte chiffré en AES
  "nonce": "..."
}
```
