# 🔐 AAA + SSH Authentication Lab (TACACS+ & RADIUS) — Cisco Packet Tracer

A CCNA / network-security lab that secures remote administration of Cisco routers by combining **SSH** (encrypted remote access) with **AAA** (Authentication, Authorization, Accounting) using two different protocols: **TACACS+** and **RADIUS**.

---

## 📌 Overview

Instead of relying on a shared local password, the router forwards login requests to a central AAA server. If the server is unreachable, the router falls back to a local user account so administrators are never locked out.

The lab contains **two independent topologies** in a single Packet Tracer file:

| Scenario | Protocol | Client | Router | AAA Server |
|----------|----------|--------|--------|------------|
| 1 | **TACACS+** | PC7 | Router2 (Cisco 2911) | Server3 |
| 2 | **RADIUS** | PC8 | Router3 (Cisco 2911) | Server4 |

---

## 🗺️ Topology

```
[ PC7 ]--Fa0------Gig0/0--[ Router2 ]--Gig0/1------Fa0--[ Server3 ]   ← TACACS+

[ PC8 ]--Fa0------Gig0/0--[ Router3 ]--Gig0/1------Fa0--[ Server4 ]   ← RADIUS
```

> Add a screenshot of the topology here: `![Topology](images/topology.png)`

---

## 🎯 Objectives

- Configure SSH v2 on a Cisco router and disable Telnet.
- Enable AAA with `aaa new-model`.
- Authenticate VTY logins against a **TACACS+** server.
- Authenticate VTY logins against a **RADIUS** server.
- Configure a **local fallback** account.
- Verify access from the client PC using SSH.

---

## ⚙️ Sample Configuration

> The IP addresses, usernames and keys below are **examples** — adjust them to match your own `.pkt` file.

### 1) SSH (both routers)

```
hostname Router2
ip domain-name lab.local
username admin privilege 15 secret LocalP@ss
crypto key generate rsa modulus 1024
ip ssh version 2
```

### 2) Interfaces (example addressing)

```
interface GigabitEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 no shutdown
interface GigabitEthernet0/1
 ip address 192.168.2.1 255.255.255.0
 no shutdown
```

### 3) AAA with TACACS+ (Router2 ↔ Server3)

```
aaa new-model
tacacs-server host 192.168.2.10 key TacacsKey123
aaa authentication login default group tacacs+ local
line vty 0 4
 transport input ssh
 login authentication default
```

### 4) AAA with RADIUS (Router3 ↔ Server4)

```
aaa new-model
radius-server host 192.168.2.10 key RadiusKey123
aaa authentication login default group radius local
line vty 0 4
 transport input ssh
 login authentication default
```

### 5) AAA Server settings (Packet Tracer → Server → Services → AAA)

- Enable the AAA service.
- Add the router as a **client** (name, IP, secret key, and server type: TACACS+ / RADIUS).
- Create a **user** (username + password) for the administrator.

---

## ✅ Verification

From the client PC (Desktop → Command Prompt):

```
ssh -l <username> 192.168.1.1
```

Useful commands on the router:

```
show ip ssh
show running-config | section aaa
debug aaa authentication
```

**Expected result:** login succeeds using the credentials stored on the AAA server; if the server is down, the local `admin` account still works.

---

## 📂 Repository Structure

```
.
├── AAA+SSH.pkt        # Packet Tracer project file
├── README.md
└── images/
    └── topology.png
```

---

## 🛠️ Tools

- Cisco Packet Tracer
- Cisco 2911 routers
- TACACS+ and RADIUS (Packet Tracer Server-PT AAA service)

---

## 📚 What I Learned

- The difference between **TACACS+** (TCP/49, encrypts the whole payload, separates authentication/authorization) and **RADIUS** (UDP, encrypts only the password, combines authentication and authorization).
- How AAA method lists work and why a `local` fallback matters.
- Why SSH should always replace Telnet for device management.

---

## 👤 Author

**Ahmed Rady**
SOC Analyst Intern | CCNA | Ethical Hacking
🔗 [LinkedIn](https://linkedin.com/in/ahmed-rady-b35252244) · 💻 [GitHub](https://github.com/AhmedRady01)
