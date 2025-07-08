
# Máquina UserSearch

```bash
┌──(root㉿kali)-[~]
└─# nmap -sS -p- -Pn -n --min-rate=5000 172.19.0.2 -oN fScan     
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-08 18:40 EDT
Nmap scan report for 172.19.0.2
Host is up (0.000013s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 02:42:AC:13:00:02 (Unknown)
