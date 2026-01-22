# LatticeChat 🛡️⚛️

> La messagerie instantanée sécurisée pour l'ère post-quantique.

## 📖 À propos
LatticeChat est une preuve de concept (PoC) d'une application de messagerie chiffrée de bout en bout (E2EE). Contrairement aux messageries classiques (Signal, WhatsApp) qui utilisent la courbe elliptique (ECC), LatticeChat utilise la **cryptographie à base de réseaux (Lattice-based cryptography)**.

L'objectif est de garantir la confidentialité des échanges même face à un futur ordinateur quantique capable de casser RSA et ECC (attaque "Harvest Now, Decrypt Later").

## 🚀 Fonctionnalités Clés
* **Chiffrement Post-Quantique :** Utilisation de l'algorithme **CRYSTALS-Kyber** pour l'échange de clés (KEM).
* **Authentification Robuste :** Signatures numériques via **CRYSTALS-Dilithium**.
* **Confidentialité Persistante (Forward Secrecy) :** Renouvellement des clés de session à chaque message ou session.
* **Zéro-Knowledge Server :** Le backend Python ne voit que des blobs chiffrés.

## 🛠️ Stack Technique
* **Backend :** Python 3.11+, FastAPI, WebSockets.
* **Frontend :** React + Vite (pour la rapidité) + WebAssembly (WASM).
* **Cryptographie :** `liboqs` (Open Quantum Safe) via wrapper Python et WASM.

## ⚠️ Avertissement de Sécurité
Ce projet est à but éducatif et expérimental. Bien qu'il utilise des algorithmes standardisés par le NIST, une implémentation "maison" ne doit pas être utilisée pour protéger des données critiques sans audit préalable.

## 📦 Installation

### Backend
```bash
cd backend
pip install -r requirements.txt
# Nécessite liboqs installé sur le système
uvicorn main:app --reload
