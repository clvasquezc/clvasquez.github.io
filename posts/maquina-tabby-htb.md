[Inicio](https://emersontech.github.io)|[Contenido](https://emersontech.github.io/nav/page1.html)|[Autor](https://emersontech.github.io/nav/about.html)|[Youtube](https://www.youtube.com/channel/UChNTj2xNpEQiliMv-IJbWvQ)|[Linkedin](https://www.linkedin.com/in/emersontech/)

# Resolviendo la Máquina Tabby de HackTheBox

![portada](/img/tabby/0.png)

## Resumen
Tabby es una máquina Linux de dificultad fácil. La enumeración del sitio web revela un segundo sitio web que está alojado en el mismo servidor bajo un vhost diferente. Este sitio web es vulnerable a archivos locales Inclusión. El conocimiento de la versión del sistema operativo se utiliza para identificar la ubicación del archivo tomcat-users.xml. Este archivo produce credenciales para un usuario de Tomcat que está autorizado para usar el /manager/text interfaz. Esto se aprovecha para implementar un archivo war y cargar un webshell, que a su vez se usa para obtener una capa inversa. La enumeración del sistema de archivos revela un archivo zip protegido por contraseña, que puede ser descargado y descifrado localmente. La contraseña descifrada se puede usar para iniciar sesión en el control remoto máquina como un usuario con pocos privilegios. Sin embargo, este usuario es miembro del grupo LXD, lo que permite escalada de privilegios mediante la creación de un contenedor privilegiado, en el que se encuentra el sistema de archivos del host. montado. Eventualmente, el acceso a la máquina remota se obtiene como root usando SSH.

### Fase de Reconocimiento
Comenzamos la fase de reconocimiento lanzando un ping hacia la direccion IP objetivo (10.10.10.190) para comprobar si existe conexion estable por medio de una traza ICMP.

![img](/img/tabby/Untitled.png)

La maquina arroja un time to live = 63 (64) lo que indica que estamos ante una maquina linux. Si realizamos un ping junto con un traceroute verificamos que por medio de un nodo intermediario se nos resta -1 unidad en el TTL haciendo que este sea de 63.

![img](/img/tabby/Untitled (1).png)

Una vez comprobada la comunicación contra nuestra victima realizamos una enumeración con la herramienta de nmap y le pasamos los siguientes parametros:

![img](/img/tabby/Untitled (2).png)

Continuando con la fase de enumeración con la herramienta de nmap, lanzamos scripts de reconocimiento para tener mayor profundidad de recopilacion de informacion y los lanzamos directamente hacia los puertos abiertos (80,22,8080):

![img](/img/tabby/Untitled (3).png)

Lanzamos un whatweb, que es como un wappalyzer, que funciona por consola para poder analizar ante que tecnologias se encuentra corriendo el servidor web:

![img](/img/tabby/Untitled (4).png)

si analizamos la captura anterior podemos observar un nombre de dominio “megahosting.com” “megahosting.htb” lo cual nos interesa investigar con mayor profundidad. Si realizamos un ping podemos observar que no entablece una conexion:

![img](/img/tabby/Untitled (5).png)

Nos dice que el nombre o el servicio se desconoce. Esto puede suceder porque se esta aplicando virtual hosting, el cual podemos resolver modificando la direccion /etc/hosts:

![img](/img/tabby/Untitled (6).png)

> Nota: Esto es importante realizarlo, ya que gracias a esto podemos lograr un path traversal exitoso, ya que la URL tendrá un request content valido para lograr el path traversal.

![img](/img/tabby/Untitled (7).png)

Esto es ideal realizarlo, ya que no es lo mismo acceder por el dominio que por la direccion ip.

Ya que el puerto  80 se encuentra abierto, esto nos indica que existe la comunicacion HTTP, lo cual, al ingresar en el navegador la direccion ip logramos entrar en la web de **MEGA-HOSTING.**

![img](/img/tabby/Untitled (8).png)

Nuevamente lanzamos un whatweb directamente especificando el puerto 8080 para comprobar los headers y podemos ver que corre un servidor apache tomcat:

![img](/img/tabby/Untitled (9).png)

Nuevamente volvemos a la web para comprobar a que lugares dentro de el podemos ir navegando y descubrir vectores de ataques como entradas de usuario, configuraciones o codigo desanitizado.

Si nos dirigimos a la misma direccion IP y especificamos el puerto 8080 podemos ver mas contenido de la web de tomcat:

![img](/img/tabby/Untitled (10).png)
![img](/img/tabby/Untitled (11).png)

Paralelamente al directorio anteriormente encontrado, sabemos que por lo general existe un directorio de inicio de sesion ubicado en manager/html el cual introducimos en la URL y damos con exito a una entrada de usuario:

![img](/img/tabby/Untitled (12).png)
![img](/img/tabby/Untitled (13).png)

Intentamos probar diferentes credenciales de inicio de sesión para comprobar credenciales débiles, tales como:

- admin:admin
- admin:admin123
- admin:password
- admin:passwrd

Pero ninguno revelea un inicio de sesión exitoso.

Navegando en la seccion “news” logramos ver un mensaje que habla sobre una fuga de datos, si analizamos la URL (Uniform Resource Locator) podemos notar lo siguiente:

![img](/img/tabby/Untitled (14).png)

El codigo php nos muestra la seccion file=statement, el cual al modificarlo nos revela una vulnerabilidad LFI (local file inclusion). Como estamos ante un LFI podemos ir navegando por el sistema de archivos del servidor mediante un directory path traversal:

![img](/img/tabby/Untitled (14).png)
![img](/img/tabby/Untitled (15).png)

Si analizamos el texto en modo "Source View" podemos ver los datos de manera más clara:

![img](/img/tabby/Untitled (17).png)

Al leer el contenido podemos notar un usuario de nombre “ash” y “root”.

Si intentamos hacer un **path traversal** con el username de ash o root no damos con exito hacia el directorio, esto pude deberse a que estan protegidos por passwords.

Navegando en el sistema de archivos, no encontramos mucho contenido.

Una busqueda en google nos revela diferentes paths para encontrar los archivos que buscamos para las credenciales y damos con exito hacia credenciales correctas:

![img](/img/tabby/Untitled (16).png)


### Redes Sociales

![img](/img/linkedin.png)|[Linkedin](https://www.linkedin.com/in/emersontech/)|![img](/img/youtube.png)|[Youtube](https://www.youtube.com/channel/UChNTj2xNpEQiliMv-IJbWvQ)|![img](/img/github.png)|[Github](https://github.com/emersontech)
