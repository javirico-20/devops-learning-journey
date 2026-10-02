# 🐧 DevOps Learning Journey

> Hands-on documentation of my career reconversion from telecom operations to DevOps engineering.

<p align="left">
  <img src="https://img.shields.io/badge/Status-In%20progress-blue?style=flat" />
  <img src="https://img.shields.io/badge/Started-2026-informational?style=flat" />
  <img src="https://img.shields.io/badge/Target-DevOps%20Engineer-success?style=flat" />
</p>

---

## 👋 What is this repo?

Public documentation of my **18-month structured plan** to transition from **6+ years in telecom operations** (Six Five Networks, routing operations manager) to a **DevOps Engineer** role.

This is not a polished portfolio — it's an **honest log** of what I'm learning, what I've built, what I've broken and fixed. If you're on a similar journey, I hope this helps you.

---

## 🗺️ Roadmap

### Phase 1 — Linux fundamentals (Months 1–3)
- [x] Week 1 — Filesystem & navigation
- [x] Week 2 — Permissions, users, groups
- [x] Week 3 — Pipes, grep, find, text processing
- [x] Week 4 — Processes, systemd, journalctl
- [x] Consolidation exercise — forensic incident investigation
- [ ] Week 5 — Networking basics
- [ ] Week 6 — SSH advanced & hardening
- [ ] Week 7 — Package management
- [ ] Week 8 — Shell scripting
- [ ] Week 9 — Filesystems
- [ ] Week 10 — System boot & configuration
- [ ] Week 11 — Web services with Nginx + HTTPS
- [ ] Week 12 — Integrator project

### Phase 2 — LPIC-1 Exam 101 (Month 4)
- [ ] LPIC-1 Exam 101 passed

### Phase 3 — AWS Cloud Practitioner (Months 5–6)
- [ ] AWS CCP passed

### Phase 4 — Job search & first role (Months 7–9)
- [ ] First tech role (Linux Support / Cloud Support Junior)

### Phase 5 — Working + AWS SAA (Months 10–17)
- [ ] AWS Solutions Architect Associate
- [ ] Docker & Terraform mastered

### Phase 6 — Kubernetes + DevOps role (Months 18–22)
- [ ] Kubernetes operational
- [ ] First DevOps Engineer role

---

## 📁 Repository structure

```
devops-learning-journey/
├── README.md                        ← you are here
├── notes/                           ← weekly notes (markdown)
│   ├── 01-filesystem.md
│   ├── 02-permissions.md
│   ├── 03-pipes-grep.md
│   └── 04-processes-systemd.md
├── scripts/                         ← reusable bash scripts
│   ├── backup.sh
│   └── health-check.sh
├── exercises/                       ← hands-on exercises
│   └── consolidation-forensic-incident/
└── projects/                        ← full projects
    └── production-web-server/
```

---

## 🚀 Featured projects

### 🔐 Production Web Server (in progress)

Ubuntu 22.04 server on Hetzner Cloud, hardened and deployed with:
- SSH key-only authentication, non-standard port, fail2ban
- Nginx serving HTTPS portfolio via Let's Encrypt (auto-renewal)
- UFW firewall with minimal attack surface
- Security headers scoring A on securityheaders.com
- Automated daily backups with retention policy
- Health monitoring (disk / memory / services / cert expiry) every 30 min

Full documentation: [`projects/production-web-server/`](./projects/production-web-server/) *(coming soon)*

### 🕵️ Forensic incident investigation exercise

Simulated compromise scenario where I:
- Investigated unknown user, suspicious processes, and insecure permissions
- Contained the incident **without destroying evidence** (SIGSTOP vs SIGKILL)
- Produced a professional markdown incident report
- Executed clean remediation with verification

See [`exercises/consolidation-forensic-incident/`](./exercises/consolidation-forensic-incident/) *(coming soon)*

---

## 📚 What I'm learning

### Fundamentals Linux
`cd` · `ls` · `grep` · `find` · `awk` · `cut` · `sort` · `uniq` · `wc` · `head` · `tail` · `tar` · `rsync` · `scp` · `tmux` · `vim/nano`

### Processes & services
`ps` · `top` · `htop` · `kill` (signals) · `nice` / `renice` · `systemctl` · `journalctl` · `cron` · `nohup` · `jobs` / `bg` / `fg`

### Permissions & security
`chmod` (octal + symbolic) · `chown` · SUID / SGID / sticky bit · ACLs · `sudo` · SSH keys · `fail2ban`

### Networking (coming)
TCP/UDP · DNS · firewalls (`ufw`) · `ss` · `ip` · `curl` · `dig` · `traceroute`

### Cloud (coming)
AWS: EC2 · S3 · IAM · VPC · RDS · CloudWatch · Route 53

---

## 🧰 Tooling

- **VPS:** Hetzner Cloud (Ubuntu 22.04 LTS)
- **Editor:** VS Code + Terminal
- **Shell:** bash + zsh
- **Version control:** Git + GitHub
- **Monitoring (from prior role):** Prometheus + Grafana

---

## 📖 Learning resources I'm using

- **Books:** "LPIC-1 Cert Guide" (Ross Brunson)
- **Courses:** Udemy — Shawn Powers LPIC-1 · Stephane Maarek AWS CCP (coming)
- **Communities:** r/linuxadmin · r/devops · r/aws
- **Own structured plan:** 18-month roadmap

---

## 📬 About the author

**Javier Rico Alcántara** — Based in Calonge (Costa Brava, Spain).

- 💼 **Previous role:** Routing Operations Manager @ Six Five Networks (2019–2026)
- 🎯 **Target role:** Linux Support / Cloud Support Junior → DevOps Engineer
- 🌐 **LinkedIn:** [linkedin.com/in/javier-rico-16228b153](https://www.linkedin.com/in/javier-rico-16228b153/)
- 🗓️ **Available for first role:** Month 7–9 of this roadmap (approx. 2027 Q2)

---

## ⚖️ License

MIT — feel free to reuse any script or note.

---

<p align="center">
  <sub>Last updated: October 2026 · In active development · PRs welcome if you spot an improvement</sub>
</p>
