# Server-side request forgery (SSRF) attacks

Permite que un atacante haga consultas a información interna utilizando un servidor vulnerable como intermediario y de esta manera, saltarse un firewall por ejemplo. 
Supongamos que en las capas internas de esa organización, tienen un firewall que solo permite conexión con unas cierts IPs, pues utilizando al servidor 
y modificando los paquetes, podemos lograr que el servidor haga una consulta de la información interna, pues el servidor 
tendría permitido las conexiones. Sería una manera de bypasear la seguridad.

Un ejemplo de ello seria utilizar la API del servidor(que es el intermediario entre el servidor y un servicio):
```bash
stockApi=http://localhost/admin/delete?username=carlos
```

Burp con su herramienta Intruder nos permite atacar un rango de IPs para analizar las respuestas 200-500 (por ejemplo si envias solucitudes a  todas las IP 
del rango 192.168.0.0/24, para la mayoría recibiras un estado de 500 que es un error, pero con una IP en concreto como es el siguiente caso recibes un estado 
de 200, por lo que la conexión se realizó y podemos intentar el ataque)
```bash
stockApi=http://192.168.0.171:8080/admin/delete?username=carlos
```

También existen métodos para ofuscar datos y esconder lo que queremos buscar, para cuando llegue al servidor no filtre esa información:
```bash
stockApi=http://127.1/admin
stockApi=http://127.1/%2561dmin/delete?username=carlos
```

O tambien para redireccionar:
```bash
/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos
```
