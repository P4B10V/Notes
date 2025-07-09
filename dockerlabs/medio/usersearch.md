
# Máquina UserSearch

Una vez desplegado el contenedor, haremos un primer escaneo para ver que información podemos obtener. 

```bash 
nmap -sS -p- -Pn -n --min-rate=5000 172.18.0.2 -oN fScan 
```
Obtenemos lo siguiente. Los puertos 22 y 80 están con el estado open. 

```bash
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-09 05:13 EDT
Nmap scan report for 172.18.0.2
Host is up (0.0000080s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 02:42:AC:12:00:02 (Unknown)
```

Se podría realizar otro escaneo con nmap pero voy a investigar el puerto 80 para ver que información puedo obtener con whatweb.

```bash
whatweb http://172.18.0.2                                
```

#### http://172.18.0.2 [200 OK] Apache[2.4.59], Country[RESERVED][ZZ], HTML5, HTTPServer[Debian Linux][Apache/2.4.59 (Debian)], IP[172.18.0.2], Title[User Search]
   

