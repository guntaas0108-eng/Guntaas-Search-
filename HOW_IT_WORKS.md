# Project Explainer: Private Self-Hosted Metasearch Engine (SearXNG)
> **Zero-Cost (₹0) Personal Search Engine Deployment**

---

## 1. The 30-Second Elevator Pitch
> *"I built and self-hosted a completely private, tracker-free metasearch engine using **SearXNG** running inside an isolated **Docker** container on my local machine. Instead of relying on a single commercial engine that profiles my searches and sells my data for targeted advertising, my private engine queries multiple search providers (Google, Bing, Brave, Wikipedia, Reddit) simultaneously in parallel, strips out all tracking cookies and advertisements, deduplicates the results, and displays a unified, clean result page — completely free of charge (₹0 investment)."*

---

## 2. System Architecture & How It Works Behind the Scenes

```
                                  +---------------------------------------------+
                                  |                 THE INTERNET                |
                                  |  +----------+ +--------+ +---------------+  |
                                  |  |  Google  | |  Bing  | | DuckDuckGo... |  |
                                  |  +----+-----+ +---+----+ +-------+-------+  |
                                  +-------|-----------|--------------|----------+
                                          ^           ^              ^
                       4. Anonymous Query |           |              | 5. Raw Results
                          (No cookies/IP) |           |              |    (HTML/JSON)
                                          v           v              v
+-------------------------------------------------------------------------------+
| LOCAL WINDOWS MACHINE                                                         |
|                                                                               |
|   +-----------------------------------------------------------------------+   |
|   | DOCKER CONTAINER ("searxng")                                          |   |
|   |                                                                       |   |
|   |   +---------------------------------------------------------------+   |   |
|   |   | SearXNG Core (Granian Python WSGI Server)                     |   |   |
|   |   |                                                               |   |   |
|   |   |   1. Query Normalizer  -> Parses your search phrase           |   |   |
|   |   |   2. Engine Dispatcher -> Asynchronously calls 70+ engines    |   |   |
|   |   |   3. Privacy Shield    -> Strips headers, cookies, user-agent |   |   |
|   |   |   4. Result Deduplicator & Scorer                             |   |   |
|   |   |   5. UI Template Renderer (Dark Theme, No Ads)                |   |   |
|   |   +---------------------------------------------------------------+   |   |
|   |                                   ^ Internal Port: 8080               |   |
|   +-----------------------------------|-----------------------------------+   |
|                                       |                                       |
|                              Port Mapping (8888:8080)                         |
|                                       |                                       |
|   +-----------------------------------|-----------------------------------+   |
|   | Web Browser (Brave / Chrome / Edge)                                   |   |
|   | URL: http://localhost:8888                                           |   |
|   +-----------------------------------------------------------------------+   |
+-------------------------------------------------------------------------------+
```

---

## 3. Step-by-Step Life of a Single Search

1. **User Types Query:** You type `machine learning algorithms` into your browser at `http://localhost:8888`.
2. **Network Routing:** Your browser sends an HTTP GET request to `localhost:8888`.
3. **Docker Port Mapping:** Docker inspects incoming traffic on host port `8888` and tunnels it into container port `8080`.
4. **SearXNG Processor:** The Python-based Granian WSGI web server receives the request.
5. **Privacy Strip:** SearXNG scrubs all identifiable information:
   - No Google tracking cookies.
   - No hardware/browser fingerprints.
   - No advertising ID.
6. **Parallel Upstream Fetching:** SearXNG dispatches asynchronous HTTP queries to Google, Bing, Brave, and Wikipedia at the same time.
7. **Deduplication & Ranking:** SearXNG collects the responses, merges duplicate links, ranks them by relevance, and strips out sponsored ads.
8. **Render:** A lightweight, dark-themed HTML results page is generated and displayed on your screen in ~1 second.

---

## 4. Key Technologies Used

| Technology | Role | Why It Was Chosen |
|---|---|---|
| **SearXNG** | Metasearch Engine | Open-source, supports 70+ search engines, active security maintenance, no telemetry. |
| **Docker** | Containerization Platform | Runs the Linux-based SearXNG environment seamlessly on Windows without needing a VM or manual Python dependency configuration. |
| **Docker Compose** | Orchestration Tool | Defines container configuration, port mappings, and volume mounts declaratively in a single YAML file. |
| **Granian / WSGI** | High-Performance Python Web Server | Powers SearXNG's async HTTP request handling with minimal memory usage. |
| **YAML** | Configuration Language | Used in `docker-compose.yml` and `settings.yml` for human-readable service configuration. |

---

## 5. Comparison: NetworkChuck's Setup vs. Our ₹0 Setup

| Component | NetworkChuck's Video | Our ₹0 Setup | How We Saved Money |
|---|---|---|---|
| **Hosting** | Paid Linode/Akamai VPS (~$5/mo = ₹420/mo) | Local PC (Docker Desktop) | Zero hosting fees; runs on existing hardware. |
| **Domain** | Paid Domain Name (~$12/yr = ₹1,000/yr) | `localhost:8888` | No domain registration required for personal use. |
| **SSL Certificate** | Caddy / Let's Encrypt | Localhost loopback isolation | Local traffic stays on your machine, eliminating external eavesdropping. |
| **Mobile Access** | Public DNS Record | Same Wi-Fi (`192.168.x.x`) or Cloudflare Quick Tunnel | 100% free Cloudflare Tunnel binary without needing a credit card or domain. |

---

## 6. Real-World Engineering Problem We Solved (Port Collision)

* **The Issue:** The default port for SearXNG is `8080`. However, this computer already had an **Apache HTTP Server (EnterpriseDB/PostgreSQL)** bound to port `8080`, causing socket collisions and connection drops.
* **The Solution:** We modified the host port mapping in `docker-compose.yml` to `8888:8080`.
  - Host listens on: `8888` (free and conflict-free).
  - Container internally receives on: `8080`.
  - Result: Both services coexist on the machine without interference.

---

## 7. Cheat Sheet: Questions You Might Be Asked & How to Answer

### Q1: *"Is SearXNG a web crawler like Google?"*
> **Answer:** No. Google is a **search engine** with its own web crawler (Googlebot) and multi-petabyte index. SearXNG is a **metasearch engine**—it acts as an intelligent aggregator and proxy. It leverages existing search engines' indexes while acting as an anonymizing middleman between you and them.

### Q2: *"How does it provide privacy if Google still gets the search query?"*
> **Answer:** Google's business model relies on building a behavioral profile of *you* (linking your search queries to your Google Account, cookies, IP address, device fingerprint, and location history). 
> When using SearXNG:
> 1. Google only sees an anonymous server request from an automated scraper.
> 2. No cookies, no session data, and no account IDs are ever transmitted.
> 3. Your query is blended together with generic traffic, preventing ad networks from building a profile on you.

### Q3: *"What is the `secret_key` in `settings.yml` for?"*
> **Answer:** SearXNG uses cryptographic hashing for session state integrity and CSRF (Cross-Site Request Forgery) protection. The 32-byte hexadecimal secret key ensures that session tokens cannot be forged or tampered with.

### Q4: *"Can I access this on my phone or outside my house for free?"*
> **Answer:** Yes, without paying for a static IP or domain:
> - **At home:** By connecting to the same Wi-Fi and opening `http://<YOUR_PC_LOCAL_IP>:8888`.
> - **Outside home:** By launching a free Cloudflare Quick Tunnel (`cloudflared tunnel --url http://localhost:8888`), which generates a free, temporary HTTPS link with no credit card required.

---

## 8. Management & Quick Commands

* **Start the engine in background:**
  ```powershell
  docker compose up -d
  ```
* **Stop the engine:**
  ```powershell
  docker compose down
  ```
* **View real-time logs:**
  ```powershell
  docker compose logs -f
  ```
* **Pull latest updates:**
  ```powershell
  docker compose pull
  docker compose up -d
  ```
