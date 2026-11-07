# 🔍 Guntaas Search

> **A Private, Tracker-Free, Self-Hosted Metasearch Engine**  
> *Built with SearXNG, Docker, and zero-cost (₹0) personal infrastructure.*

---

## 🌟 Overview

**Guntaas Search** is a private, customizable metasearch engine that aggregates and deduplicates results from over 70 search services (including Google, Bing, Brave, DuckDuckGo, Reddit, and Wikipedia) without tracking your searches, collecting cookies, or serving sponsored ads.

Unlike commercial search engines that profile your online behavior, **Guntaas Search** acts as an anonymizing proxy between you and upstream search providers.

---

## 🚀 Key Features

* **🛡️ 100% Private & Ad-Free:** Upstream search engines only see an anonymous request from your server. No search history is saved, no tracking cookies are accepted, and no profiles are built.
* **⚡ Multi-Engine Aggregation:** Queries multiple engines in parallel and merges results into a single, clean, fast interface.
* **💰 ₹0 Investment:** Runs entirely on local hardware using Docker Desktop with zero cloud VPS or domain costs.
* **🎨 Custom Dark Theme & Branding:** Custom frontend styling with a minimalist dark mode.
* **⚡ Instant Autocomplete:** Fast DuckDuckGo-powered search suggestions as you type.
* **🌐 Cross-Device Friendly:** Accessible on desktop, mobile (via home Wi-Fi), or globally using free Cloudflare Quick Tunnels.

---

## 🛠️ Tech Stack

* **Core Engine:** [SearXNG](https://github.com/searxng/searxng) (Python / Granian WSGI)
* **Containerization:** Docker & Docker Compose
* **Configuration:** YAML (`settings.yml`, `docker-compose.yml`)
* **Templating:** Jinja2 / HTML5 / CSS3

---

## 📋 Quick Start Guide

### Prerequisites
* [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running.
* Git installed.

### 1. Clone the Repository
```bash
git clone https://github.com/guntaas0108-eng/Guntaas-Search-.git
cd Guntaas-Search-
```

### 2. Launch with Docker Compose
```bash
docker compose up -d
```

### 3. Open in Your Browser
Navigate to:
```
http://localhost:8888
```

---

## ⚙️ Configuration

Custom instance settings can be modified in `core-config/settings.yml`:

```yaml
use_default_settings: true

general:
  instance_name: "Guntaas Search"

server:
  bind_address: "0.0.0.0"
  port: 8080
  secret_key: "<YOUR_SECRET_KEY>"
  limiter: false

search:
  safe_search: 0
  autocomplete: "duckduckgo"

ui:
  default_theme: "simple"
  theme_args:
    simple_style: "dark"
```

After modifying settings, restart the container:
```bash
docker compose restart
```

---

## 🔧 Managing the Container

* **Start:** `docker compose up -d`
* **Stop:** `docker compose down`
* **View Logs:** `docker compose logs -f`
* **Update Engine:** `docker compose pull && docker compose up -d`

---

## 📚 Architecture & Deep Dive

For detailed technical explanations, request lifecycle diagrams, and how this engine works under the hood, check out [HOW_IT_WORKS.md](./HOW_IT_WORKS.md).

---

## 📄 License

This project is open-source and distributed under the [GNU Affero General Public License v3.0 (AGPL-3.0)](https://www.gnu.org/licenses/agpl-3.0.en.html) in accordance with SearXNG licensing.
