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

[Take a look!](https://traata.pages.dev/?v=1)


# Igris / Beru: Resilient AI Agent & Digital Shadow

Igris is a fault-tolerant, RAG-based (Retrieval-Augmented Generation) conversational AI and automated agent. Designed to act as a "digital shadow," the system can adopt multiple dynamic personas (like the professional recruiter-facing "Beru" identity) to interact intelligently based on highly specific vector knowledge bases.

### 🧠 Architectural Design & Decisions

#### 1. High Availability via Multi-Provider LLM Fallbacks
AI systems built on a single API are inherently brittle. Provider outages (e.g., OpenAI or Anthropic going down) instantly break standard applications. 
* **The Solution:** Igris is engineered with a deterministic fallback chain. The core routing logic runs primarily on high-speed inference endpoints (like **Groq**), but actively monitors for timeouts or rate limits.
* **Failover Logic:** If the primary provider fails, the system automatically hot-swaps to **Google GenAI** (or secondary configured LLMs) mid-request, ensuring 100% uptime and seamless user experience during provider-side outages.

#### 2. Modular Prompt Composition
Hardcoding system prompts limits the flexibility of an AI agent. Igris utilizes a modular architecture to build prompts dynamically.
* **Identities & Rules:** System behavior is assembled at runtime by injecting specific "Identity" files so that every role sounds unique. 
* **Dynamic Context:** This modularity allows the agent to switch context and operational boundaries instantly without requiring codebase deployments.

#### 3. RAG & Vector Memory
To prevent hallucinations and provide accurate, personal context, the agent utilizes a robust Retrieval-Augmented Generation pipeline.
* **Orchestration:** Built utilizing **Node.js, Express,** and **LangChain.js**.
* **Execution:** User queries are embedded, matched against a vector memory store containing personal knowledge bases/documents, and injected into the LLM context window to ground the agent strictly in reality.

### 🛠 Tech Stack
* **Client Interface:** ReactJS
* **Orchestration Engine:** Node.js, Express
* **AI/ML Layer:** LangChain.js, Groq, Google GenAI, Vector Memory
* **Architecture:** RAG, LLM Fallback Routing

  [Try it out!](https://agentigris.pages.dev/?v=1)
