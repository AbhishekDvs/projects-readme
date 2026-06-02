# Traata: Zero-Knowledge Secure Vault & Notes Manager

Traata is a cross-platform, end-to-end encrypted password and notes manager designed with a strict zero-knowledge architecture. The system ensures that all sensitive data is encrypted on the client before it ever touches the network, meaning the server and database host maintain zero visibility into user payloads.

### 🧠 Architectural Design & Decisions

#### 1. Zero-Knowledge / Zero-Trust Encryption Model
The fundamental rule of Traata is that the backend must be completely blind to the data it stores.
* **Cryptography:** Data is encrypted directly on the client (Web/Android) using **AES-256-GCM**.
* **Key Derivation:** Master passwords are never transmitted. The client derives cryptographic keys locally using **Argon2id** (to resist GPU brute-force attacks) before executing the encryption routine. 
* **Payloads:** The Hono/Supabase backend only receives, stores, and syncs impenetrable ciphertext and authentication tags.

#### 2. Defense Against Automated Attacks
Because the vault is heavily encrypted, the primary attack vector shifts to authentication brute-forcing and API abuse.
* **Rate Limiting:** Strict IP-based and user-based rate limiting is enforced on all authentication and data-mutation endpoints to thwart credential stuffing.
* **reCAPTCHA v3 Integration:** Invisible, score-based bot detection (reCAPTCHA v3) is implemented across critical API routes. Requests falling below the human-confidence threshold are programmatically rejected before hitting the database, preserving backend resources.

#### 3. Infrastructure & Cost Optimization (Render Free Tier)
To maintain an independent, highly available backend infrastructure without incurring unnecessary costs, the deployment strategy leverages Render's free tier with custom circumvention engineering.
* **Automated CRON Reconciliation:** A dedicated CRON job architecture routinely pings the service to prevent the Render container from spinning down/sleeping, bypassing the free-tier cold start penalty. 
* **Data Syncing:** These CRON tasks also handle background data reconciliation, ensuring that cross-device synchronization remains consistent across both Web and Android clients.

### 🛠 Tech Stack
* **Frontend/Mobile:** React, React Native, TypeScript
* **Backend:** Hono, Supabase
* **Security Layer:** AES-256-GCM, Argon2id, reCAPTCHA v3
* **Infra:** Render (with CRON optimization)

* [Take a look!](https://traata.pages.dev/?v=1)
