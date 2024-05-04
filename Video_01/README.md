# Primeros pasos 🏃‍con Træfik 🚏

![Traefik_Video_01.jpg](images%2FTraefik_Video_01.jpg)

Hoy vamos a hablar de Traefik, un proxy inverso y balanceador de carga que se integra perfectamente con Kubernetes y que nos permite exponer nuestros servicios de una forma sencilla y segura.

0:00 Inicio

0:11 INTRO

0:30 Ejemplo 01

4:31 Ejemplo 02

11:19 Dashboard

13:11 Conclusión

13:20 Despedida

13:28 OUTRO

GitHub:
https://github.com/TheAutomationRules/traefik/blob/main/Video_01/README.md

Twitter: @TheAutomaRules
Instagram: TheAutomationRules

Visita y suscríbete en mi otro canal!
https://www.youtube.com/@TheRavenManCave

#traefik #proxy #kubernetes #loadbalancer #devops #automation #cloud #cloudnative

---

## Ejemplo 01

En este ejemplo usaremos Docker Compose para levantar un contenedor de Traefik y otro con un servicio web de prueba.

````
docker-compose up -d
````

Una vez levantados los contenedores, podremos acceder a la URL http://localhost:8080 para ver el dashboard de Traefik.

Para acceder al servicio web de prueba, podremos hacerlo a través de la CLI.

````
curl -H Host:whoami.docker.localhost http://127.0.0.1
````

En la definicion de nuestro docker-compose tenemos solo una instancia del servicio web, por lo que siempre obtendremos la misma respuesta.

Ahora escalamos este servicio a 3 instancias.

````
docker-compose up -d --scale whoami=3
````

Y ahora, al hacer la misma petición, veremos que obtenemos respuestas de las 3 instancias, cada vez que hagamos la peticion recibiremos respuesta de un contenedor distinto.

````
curl -H Host:whoami.docker.localhost http://127.0.0.1
````

Ahora destruimos todo.

````
docker-compose down
````

## Ejemplo 02

En este ejemplo vamos a levantar Traefik en local, basado en un fichero de configuracion en el que definiremos los siguientes parametros:

- Deshabilitamos la comprobacion de nuevas versiones.
- Deshabilitamos el envio de datos anonimos.
- Habilitamos el almacenamiento del log de Traefik en un fichero.
- Habilitamos el almacenamiento del log de acceso en un fichero.
- Habilitar el dashboard de Traefik
- Deshabilitamos el SSL/TLS.
- Definimos un entrypoint para el trafico Web HTTP y HTTPS.
- Definimos un entrypoint para el acceso a los puertos de los servicios de HashiCorp (Consul y Vault).
- Habilitamos el provider file para la lectura de los ficheros de configuracion de los servicios de manera dinamica.

Ahora veamos la definicion del fichero con la conffiguracion dinamica.

En este fichero definimos el index http y un par de routers, uno para el acceso a Consul y otro para el acceso a Vault con los siguientes parametros:

- El nombre del router.
- La regla de acceso en este caso el nombre del host, en este caso rasp.lan.
- El servicio al que se redirige el trafico.
- La prioridad del router.
- El entrypoint por el que se accede al servicio.

Como veis con esta configuracion accederemos a 3 servidores distintos, que se encuentran en 3 Raspberry Pi distintas, a traves de un solo nombre de host (rasp.lan).

Una vez definidos los routers, definimos los servicios a los que se accede a traves de los routers, con los siguientes parametros:

- El nombre del servicio.
- El tipo de servicio en este caso LoadBalancer.
- Y la definicion de los servidores que componen el servicio, en este caso el acceso a los 3 servidores de Consul y Vault, cada uno con su hostname y puerto.

Para levantar Traefik con esta configuracion, ejecutamos el siguiente comando parados en la raiz del video (Video_01).

````
traefik --configFile=./ejemplo_02/config/traefik.yaml
````
Ahora para ver los logs de Traefik, ejecutamos el siguiente comando.

````
tail -f ejemplo_02/var/log/traefik/traefik.log
````

Podemos acceder al dashboard de Traefik en la URL http://localhost:8080.

Si queremos ver los logs de acceso, ejecutamos el siguiente comando.

````
tail -f ejemplo_02/var/log/traefik/access.log
````

Con esto podemos ir comprobando los accessos a los servicios de Consul y Vault a traves de los routers definidos en el fichero de configuracion. Como vereis, el acceso a los servicios se hace a traves de los nombres de host definidos en el fichero de configuracion, cada vez que accedemos a los servicios veremos que el balanceador nos deriva constantemente a otro servidor balanceando la carga.

En conclusión, Traefik es una herramienta muy potente y versatil que nos permite exponer nuestros servicios de una forma sencilla y segura, con una configuracion muy flexible y facil de mantener.