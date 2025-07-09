
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
Ya tenemos la versión del servicio que está corriendo en el puerto 80, que es Apache 2.4.59

```bash
http://172.18.0.2 [200 OK] Apache[2.4.59], Country[RESERVED][ZZ], HTML5, HTTPServer[Debian Linux][Apache/2.4.59 (Debian)], IP[172.18.0.2], Title[User Search]
```

Antes de buscar vulnerabilidades sobre Apache, voy a visitar la página web. Por lo que parece, tengo permitido hacer un input en el cual se busca un usuario.
Supongo que ese usuario estará guardado en una base de datos por lo que por detras estará realizando una consulta.

![Captura](https://github.com/user-attachments/assets/57a2037f-d52b-41c1-892f-615f29d5f3fc)

Para empezar introduciré una palabra cualquiera y ver cual es la respuesta. 

![fsd](https://github.com/user-attachments/assets/59abe1c5-b6a3-4c00-bae1-d9e6751e9b66)

En la barra de búsqueda aparece un parámetro /?user= y en la página web nos avisan de un "Unauthorized query". Hice algunas pruebas con el repeater 
de Burp enviando ?user= con valores como ../../../etc/passwd o ....//....//....//....//etc/passwd pero no era vulnerable en ese aspecto, por lo que volveré 
al input de la página principal. 

Probando nombres aleatorios las respuestas cambian entre "Unauthorized query" o "No results found" aunque probando con "admin" se obtuvo una respuesta:

![admin](https://github.com/user-attachments/assets/445aa63c-6650-4232-82b1-d70d81880513)

Por la información que dan pienso que la query será algo como:
SELECT username, password FROM tabla_usuarios WHERE username=admin  

Probando distintos inputs SQLi obtuve una respuesta con:
admin' OR 1=1-- - 

![usuarios](https://github.com/user-attachments/assets/19785ba7-7747-4f40-ba91-56754db5d1cc)

Como ahora tenemos usuarios y contraseñas, se probará a realizar una conexión por ssh, pues tiene el puerto open. (administrador y user1 no funcionan)

```bash
ssh kvzlx@172.18.0.2 
kvzlx@172.18.0.2's password: 
Last login: Wed Jul  9 09:53:58 2025 from 172.18.0.1
kvzlx@85e9bcd37a39:~$ 
```

Ahora que estamos dentro, tendremos que intentar escalar privilegios. 

```bash
kvzlx@85e9bcd37a39:~$ sudo -l
Matching Defaults entries for kvzlx on 85e9bcd37a39:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty

User kvzlx may run the following commands on 85e9bcd37a39:
    (ALL) NOPASSWD: /usr/bin/python3 /home/kvzlx/system_info.py
```

Hay un archivo .py que el usuario puede usar con permisos, por lo que trataremos de escalar privilegios con el. Ese archivo está en la carpeta personal del usuario
pero tiene los siguientes permisos > -rwxr-xr-x 1 root  root   191 May 29  2024 system_info.py 
Probaremos a borrar el archivo a ver si podemos (al ser nuestra carpeta personal, deberíamos tener total control sobre los archivos). Fue posible borrar el archivo 
por lo que crearemos otro archivo .py con el mismo nombre para intentar escalar privilegios con Python. 

![escalada](https://github.com/user-attachments/assets/0c40a8f9-cfaf-4972-9777-b8a79b62fa7f)







