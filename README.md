

# 🚀 Thought 5G

### QuantumLoop — Single-Node 5G Operator Proof-of-Concept (Local-First)

> A full-stack 5G telecom operator simulation running on a single machine.
> Designed for legal local testing, telecom R&D, and architecture experimentation.

---

## ⚠️ Legal Notice

This project is **strictly for local development and educational purposes**.
It does **NOT** authorize public telecom operation. Running a real telecom network requires regulatory approval, licensed spectrum, certified hardware, and lawful intercept compliance.

---

# 📡 Architecture Overview

Thought 5G simulates a complete ISP / 5G operator stack:

* Core Network (Control + User Plane)
* RAN Emulation
* OSS/BSS (Billing + Provisioning)
* Local Content Ecosystem (Video, Social, AI)
* Observability Stack
* LLM-Assisted Dev Workflow

Everything runs containerized on a **single machine**.

---

## 🧠 Logical Architecture

```
Client (Wi-Fi Device)
        ↓
hostapd + dnsmasq (AP & DHCP)
        ↓
Host Machine
        ↓
Docker Network (thought-net)
        ↓
 ├── Open5GS (Core NFs)
 ├── UERANSIM (gNB + UE)
 ├── OSS/BSS (Flask + React)
 ├── nginx (Edge Reverse Proxy)
 ├── Local Services (Video, Social, AI)
 ├── Local LLM (Offline model)
 └── Observability (Prometheus + Grafana + OpenSearch)
```

---

# 🎯 Design Goals

### ✅ Full-featured 5G PoC

* UE attach
* PDU session establishment
* Data plane flow
* QoS & slicing simulation
* CDR generation & billing
* Optional IMS/VoIP

### ✅ Local-Only Operation

* No external internet required
* All content hosted locally

### ✅ Clean Scalability Pattern

* CNF-style separation
* Clear control vs user plane
* Easy transition to multi-node later

### ✅ Security First (PoC-level hardening)

* mTLS between services
* Vault-managed secrets
* Docker seccomp profiles
* Immutable CDR logs
* RBAC for admin UI

### ✅ LLM-Assistable Development

* Predefined role prompts
* Codegen prompts
* Config generation templates
* Automated test suggestions

---

# 🖥 System Requirements

| Component | Minimum                   |
| --------- | ------------------------- |
| CPU       | 4 cores recommended       |
| RAM       | 8–16GB                    |
| Storage   | SSD recommended           |
| OS        | Ubuntu 22.04 LTS / Debian |
| Runtime   | Docker + Docker Compose   |

---

# 🧩 Technology Stack

## Core Network

* Open5GS (AMF, SMF, UPF, UDM, UDR, PCF, AUSF)
* MongoDB

## RAN Emulation

* UERANSIM (gNB + UE)

## Edge & Services

* nginx (reverse proxy + static)
* Flask / FastAPI microservices
* React (Admin UI)

## AI Layer

* gpt4all / llama.cpp (quantized local model)

## Observability

* Prometheus
* Grafana
* OpenSearch
* Filebeat

---

# 📂 Repository Structure

```
thought-core/
├── README.md
├── infra/
│   ├── docker-compose.yml
│   ├── scripts/
│   └── k3s/
├── open5gs/
│   ├── config/
│   └── seed-subscribers/
├── ueransim/
├── oss-bss/
│   ├── billing/
│   ├── provisioning/
│   └── admin-ui/
├── local-services/
│   ├── media/
│   ├── nginx/
│   └── api/
├── observability/
├── docs/
├── infra-as-code/
└── llm-prompts/
```

---

# 🐳 Docker Architecture

All services run in a dedicated Docker bridge network:

```
thought-net
```

### Example Services

* mongodb
* open5gs
* ueransim
* nginx
* oss-provision
* billing
* instagram-api
* ai-api
* prometheus
* grafana

### Resource Control

Each container should use resource limits:

```yaml
deploy:
  resources:
    limits:
      cpus: "0.50"
      memory: 512M
```

Designed to prevent system overload.

---

# 🔧 Important Configuration Notes

### Open5GS

* MCC: 999
* MNC: 99
* Seed Subscriber:

  * IMSI: 999990000000001
  * MSISDN: 9900000001

### UERANSIM

* gNB connects to AMF container IP
* UE uses seeded IMSI

### hostapd

* SSID: `Thought-5G-Demo`
* Host IP: `192.168.50.1/24`

### nginx

* `/media` → local file storage
* `/api/ai` → local LLM API

---

# 🔐 Security Hardening (PoC Level)

> Strong configuration discipline, auditability, and service isolation.

### Network Security

* UFW default deny
* Limited exposed ports
* Internal-only Docker bridge

### Service Security

* mTLS between NFs
* Vault-based secret storage
* No plain-text credentials in repo
* Docker seccomp enabled

### Data Security

* Encrypted DB backups
* Immutable append-only CDR logs
* Signed container images (cosign)

---

# ⚡ Performance Tuning

* Limit active UEs: 1–5
* Limit GOMAXPROCS for Go services
* Single-stream iperf testing
* SSD-backed MongoDB
* Increased file descriptor limits

Target: CPU < 85% under demo load

---

# 🧪 Testing Checklist

## Functional

* [ ] UE registration complete
* [ ] PDU session established
* [ ] Data path working (UE → UPF → nginx)
* [ ] CDR generated
* [ ] Billing triggered

## Security

* [ ] mTLS verified
* [ ] Vault secret retrieval working
* [ ] Port scan matches expected exposure

## Performance

* [ ] iperf throughput meets LAN baseline
* [ ] No container OOM
* [ ] CPU stable under load

---

# 🚀 Quick Start (Developer Runbook)

```bash
git clone <repo>
cd thought-core

cp infra/.env.example infra/.env
# edit environment variables

sudo ./infra/scripts/setup_host.sh

docker compose -f infra/docker-compose.yml up --build -d

./open5gs/seed-subscribers/add_subscriber.sh
```

Then:

* Start UERANSIM
* Connect device to `Thought-5G-Demo`
* Visit:

```
http://thought.local
```

Test:

* Video streaming
* Local social feed
* AI chat
* Billing flow

---

# 🤖 LLM-Assisted Development

Located in:

```
/llm-prompts/
```

Includes:

* Build-Assistant role definition
* Open5GS seed generation prompt
* Hardening checklist generator
* Config templating prompts
* PR review automation prompts

This enables AI-accelerated telecom engineering.

---

# 📈 Scalability Roadmap

### Phase 1 — Single Node (Current)

* Full PoC on one machine

### Phase 2 — Multi-Node

* Dedicated UPF node
* SR-IOV / DPDK acceleration

### Phase 3 — Kubernetes

* k3s → Full k8s
* CNF scaling
* HA control plane

### Phase 4 — Edge + MEC

* Local breakout
* Low latency services

### Phase 5 — Regulatory Transition

* Spectrum licensing
* Regulatory approvals
* Certified hardware
* Lawful intercept compliance

---

# 📦 Upcoming Deliverables

* Fully populated docker-compose.yml
* Seed subscriber automation
* Admin provisioning UI
* Pre-configured PoC bundle
* One-command start script

---

# 🏢 About QuantumLoop

Thought 5G is a research initiative under **QuantumLoop** focused on:

* Telecom virtualization
* CNF architecture
* Local-first internet ecosystems
* AI-integrated network stacks
* Sovereign infrastructure models

---

