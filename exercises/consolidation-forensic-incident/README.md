# 🕵️ Forensic Incident Investigation — Case Study

> Hands-on simulated compromise scenario where I played the role of a Linux systems analyst responding to a security incident. End-to-end: investigation, containment, documentation, and remediation.

<p align="left">
  <img src="https://img.shields.io/badge/Type-Hands--on%20exercise-blueviolet?style=flat" />
  <img src="https://img.shields.io/badge/Environment-Ubuntu%2022.04-E95420?style=flat&logo=ubuntu&logoColor=white" />
  <img src="https://img.shields.io/badge/Status-Completed-brightgreen?style=flat" />
</p>

---

## 📖 Context

This was the **integrator exercise of the Linux fundamentals block** (weeks 1–4 of my DevOps learning plan). Instead of a scripted lab, I set up the compromise scenario myself, then investigated it from scratch as if I were being paged by my manager.

**Mindset:** play the game straight. No shortcuts. Document everything.

---

## 🎭 The scenario

My imaginary manager Marta pings me:

> *"A teammate did something on the server last night. There's a log growing weird, some permissions look off, there's a process running we don't recognize, and he doesn't remember what he did. Investigate and send me a report."*

Open-ended. No obvious attacker. No clear scope. Just: *something's wrong, figure it out*.

---

## 🔍 Phase 1 — Investigation

My job: figure out **what is anomalous** on the server, **who** did it, and **how bad** it is.

### Users

```bash
cat /etc/passwd | awk -F: '$3 >= 1000 && $3 < 65534 {print $1, $3, $6, $7}'
```

Found: `misterio` (UID 1000, home `/home/misterio`, shell `/bin/bash`). **Only human user in a server that should only have root.** Red flag #1.

### Processes

```bash
pgrep -af "while true"
ps aux | grep -v grep | grep "while true"
```

Found three PIDs: two `sudo` wrappers and one `bash -c 'while true; do echo "$(date) - actividad" >> /var/data-app/app.log; sleep 2; done'`. **Running as root. Launched with nohup for persistence.** Red flag #2.

### Growing file

```bash
du -h /var/data-app/*
sleep 5
du -h /var/data-app/*
```

`/var/data-app/app.log` was growing continuously. `/var/data-app/dump.bin` was a static 50 MB binary (`file dump.bin` confirmed it was random data from `/dev/urandom`).

### Permissions

```bash
ls -ld /var/data-app/
ls -la /var/data-app/
```

- `/var/data-app/` → **777** (world-writable). Any user could create/delete files inside, including root-owned ones.
- `/var/data-app/app.log` → **666** (world-writable). Anyone could tamper with the log.

Red flag #3.

### Ownership

The suspicious process was owned by **root**. A persistent background loop running as root is as bad as it gets — no privilege separation, maximum blast radius.

---

## 🛡️ Phase 2 — Containment (without destroying evidence)

Critical principle: **pause the problem, don't nuke it yet**. Evidence first, cleanup second.

### Pause the process (not kill)

```bash
sudo kill -SIGSTOP 1046
```

Why **SIGSTOP** instead of SIGTERM/SIGKILL:
- SIGSTOP cannot be caught, blocked, or ignored by the process. Instant pause.
- The process stays alive in state `T` (stopped). Memory, file descriptors, PID — all preserved.
- If forensic analysis later wants to inspect it (gcore, lsof, /proc/PID/), it's still there.

### Verify stopped state

```bash
ps -o pid,stat,user,cmd -p 1046
```

Column `STAT = T` → confirmed stopped.

### Harden permissions (block further writes without destroying files)

```bash
sudo chmod 750 /var/data-app/
sudo chmod 640 /var/data-app/app.log
```

Now the directory and log are properly owned by root with no world access — but the files themselves (evidence) remain intact.

### Verify log is no longer growing

```bash
du -h /var/data-app/app.log
sleep 5
du -h /var/data-app/app.log
sleep 5
du -h /var/data-app/app.log
```

Three measurements, same size → process truly paused.

---

## 📝 Phase 3 — Documentation

Marta's second request:

> *"Send me a Markdown report of what happened and what you did. Professional style. Save it in `~/incidentes/`."*

Delivered: `~/incidentes/incidente-2026-10-02.md` with the following structure:

```markdown
# Incident report — 2026-10-02

## Executive summary
3–4 lines summarizing what happened

## Findings
### Unexpected users
### Suspicious processes
### Affected files
### Incorrect permissions

## Actions taken
(numbered list)

## Pending
(what's left to decide)
```

Writing the report immediately after the investigation was the most useful part of the exercise. It forced me to:
- Separate **facts** (what I observed) from **assumptions** (what I inferred).
- State **evidence**, not opinion.
- Keep **future-decision items** separate from already-executed actions.

---

## 🧹 Phase 4 — Remediation

Marta signs off:

> *"Report looks good. Clean everything up — user, processes, files — and close the incident."*

### Kill the stopped process

```bash
sudo kill -SIGKILL 1046
```

Why **SIGKILL** here (vs the usual SIGTERM):
- The process is in state `T`. **It doesn't process signals in that state** — SIGTERM would queue up but never execute.
- Only SIGKILL is applied by the kernel directly, regardless of process state.

### Remove the user and their home

```bash
sudo deluser --remove-home misterio
```

Learned the hard way: the first time I ran `deluser misterio` **without `--remove-home`**, the user was deleted from `/etc/passwd` but `/home/misterio` stayed on disk as an **orphan directory** (owned by a UID that no longer exists). This is a classic security risk — if later a new user gets UID 1000, they inherit the leftover files silently.

Fix:

```bash
sudo rm -rf /home/misterio
```

### Delete the suspicious directory and temp files

```bash
sudo rm -r /var/data-app/
sudo rm /tmp/misterio.pid.tmp
```

### Verification

```bash
id misterio               # expected: "no such user"
ls /var/data-app/         # expected: error
pgrep -f "while true"     # expected: empty
```

All three pass → incident closed.

---

## 🎓 Key learnings

### Technical

1. **Signals have semantics.** SIGSTOP preserves state, SIGKILL destroys it, SIGTERM asks politely. Picking the right one matters.
2. **`T` state processes don't react to signals.** Only SIGKILL bypasses the pause.
3. **`deluser` != `deluser --remove-home`.** Flags matter. Orphan directories are a real risk.
4. **`rmdir` vs `rm -r` is not a style preference.** `rmdir` only works on empty directories, has no `-f` option, no recursion.
5. **The classic grep gotcha** → `grep -v grep` or `pgrep -af` to avoid matching your own grep.
6. **`du` measured twice with `sleep` between** is the standard way to confirm a file is actually growing (or stopped growing).

### Operational

1. **Evidence first, cleanup second.** The urge to "just delete the suspicious thing" destroys forensic value.
2. **Documentation is part of the job, not an afterthought.** The report forces clarity.
3. **State what you know vs what you assume.** "User misterio exists" is a fact. "Root created it last night" is an assumption until auth.log proves it.
4. **Verification is a step, not a feeling.** After cleanup, three concrete commands that must fail → not "feels clean".

### Meta

A full forensic workflow is: **Investigate → Contain → Document → Remediate → Verify**. In that order. Skipping steps or doing them out of sequence creates risk, destroys evidence, or produces unreliable results.

---

## 🔗 Related

- Linux `signal(7)` man page — the full signal catalog
- `[Linux Journey](https://linuxjourney.com/)` — fundamentals I used to prepare
- Blog post forthcoming on my LinkedIn

---

<p align="center">
  <em>Part of my <a href="../../">devops-learning-journey</a>.</em>
</p>

---
