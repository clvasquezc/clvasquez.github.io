|[Inicio](https://clvasquezc.github.io/index.html)|[Contenido](https://clvasquezc.github.io/es/nav/page1.html)|[Autor](https://clvasquezc.github.io/es/nav/about.html)|[Linkedin](https://www.linkedin.com/in/clvasquezc/)

# Bienvenido a mi primer articulo (Esto es una plantilla)

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

El codigo php nos muestra la seccion **file=statement**, el cual al modificarlo nos revela una **vulnerabilidad LFI (local file inclusion)**. Como estamos ante un LFI podemos ir navegando por el sistema de archivos del servidor mediante un directory path traversal:

![img](/img/tabby/Untitled (14).png)
![img](/img/tabby/Untitled (15).png)

Si analizamos el texto en modo "Source View" podemos ver los datos de manera más clara:

![img](/img/tabby/Untitled (17).png)

Al leer el contenido podemos notar un usuario de nombre **“ash” y “root”.**

Si intentamos hacer un **path traversal** con el username de ash o root no damos con exito hacia el directorio, esto pude deberse a que estan protegidos por passwords.

Navegando en el sistema de archivos, no encontramos mucho contenido.

Una busqueda en google nos revela diferentes paths para encontrar los archivos que buscamos para las credenciales y damos con exito hacia credenciales correctas:

![img](/img/tabby/Untitled (18).png)

Al realizar un “source View Page” a la página web podemros notar que el código fuente escondido (comentado) credenciales de acceso de usuario:

![img](/img/tabby/Untitled (19).png)
![img](/img/tabby/Untitled (20).png)

Nuevamente volvemos al panel de inicio de sesion anterior para introducir las credenciales encontradas **(tomcat:$secureP4s5w0rd123!)**, pero nos arroja un codigo de error 403 - acceso denegado. 

Error 403 Forbidden es un código de estado HTTP que indica que el servidor deniega la acción solicitada, página web o servicio. En otras palabras, el servidor ha podido ser contactado, y ha recibido una petición válida, pero ha denegado el acceso a la acción que se solicita.

![img](/img/tabby/Untitled (21).png)
![img](/img/tabby/Untitled (22).png)
![img](/img/tabby/Untitled (23).png)

Como las credenciales que acabamos de introducir **SON CORRECTAS** pero el servidor no es capaz de conectarnos, realizamos una busqueda externa en google para comprobar vias alternas.

Una busqueda en google nos arroja informacion de bastante utilidad en donde encontramos un foro con el mismo problema y su solucion:

![img](/img/tabby/Untitled (24).png)

[https://www.certilience.fr/2019/03/tomcat-exploit-variant-host-manager/](https://www.certilience.fr/2019/03/tomcat-exploit-variant-host-manager/)

![img](/img/tabby/Untitled (25).png)

El texto anterior, se resume en que debemos modificar la URL de inicio de sesion y con las mismas credenciales deberiamos ganar acceso hacia el panel de tomcat:

![img](/img/tabby/Untitled (26).png)
![img](/img/tabby/Untitled (27).png)

Llegados hasta este punto, nuestro objetivo en este momento es encontrar alguna forma de introducir archivos para poder introducir una **aplicación (.war) maliciosa** con el fin de lograr una reverse shell.

Como el mismo portal web no nos deja introducír ningún tipo de archivo externo, mediante la consola podríamos realizarlo. Para ello primero listamos las aplicaciones ya existentes:

![img](/img/tabby/Untitled (28).png)

Entonces nuestro objetivo ahora es poder subir nuestra aplicación maliciosa para conseguir una reverse shell, para ello, utilizamos en la cosnola el comando msfvenom y pedimos que nos otorgue una lista de payloads en donde nos intersa conseguir una reverse shell. Ten en cuenta que este comando nos ayuda a listar los payloads ya existentes que podrían servirnos para nuestros ataques:

![img](/img/tabby/Untitled (29).png)

Una vez encontrado nuestro payload de interés, en este caso es java/jsp_shell_reverse_tcp, generamos nuestro archivo .war, selecionando nuestra dirección IP y puerto local de destino:

![img](/img/tabby/Untitled (30).png)
![img](/img/tabby/Untitled (31).png)

Como no podemos subir los archivos directamente hacia la página web, realizamos una búsqueda externa en google para poder averiguar como introducir achivos externos mediante consola.

![img](/img/tabby/Untitled (32).png)

Nuevamente, si listamos nuestras aplicaciones existentes, podemos notar que nuestra aplicación de nombre **“nuestropayload.war”** malicioso ahora existe dentro de los archivos internos:

![img](/img/tabby/Untitled (33).png)

Si ahora en la URL abrimos nuestro achrivo “revershell” y entablamos comunicación con un netcat, obtendremos una comunicación exitosa:

![img](/img/tabby/Untitled (34).png)
![img](/img/tabby/Untitled (35).png)

Nuestro siguente paso es hacer un tratamiento de la TTY. Esto es útil a la hora de ganar una reverse shell en cualquier máquina ya que nos ayuda a poder manipular la terminal remota de una manera mucho más eficiente y cómoda. Para ello vamos a utilizar los siguientes comandos:

> Nota: Más información para manipular una TTY [Aqui](https://github.com/emersontech/tratamiento-de-tty/blob/main/tty.md)

### 1) Comprobar que tenemos la conexion establecida. Al lanzar el comando *whoami* debería devolvernos el nombre de la máquina.
```bash
whoami
```
### 2) Lanzar una bash interactíva (una pseudo consola)

```bash
script /dev/null -c bash
```

### 3) Enviar el proceso que se está ejecutando a segundo plano

```bash
[CTRL + Z]
```

### 4) Retomar el proceso anteriormente dejado en segundo plano (3)

```bash
stty raw -echo; fg
```

### 5) Reiniciar la configuración actual de la terminal (luego de este paso ya deberíamos poder operar la terminal externa de forma más cómoda.)

```bash
reset xterm
```

### 6) Exportamos una terminal Xterm

```bash
export TERM=xterm
```

### 7) Exportamos una bash

```bash
export SHELL=bash
```

### 8) Cambiar la resolución de nuestra bash interactiva. Nota: Para revisar el numero de rows y columns actuales ejecutar -> stty size.
```bash
stty rows 51 columns 189
```

### 9) Reiniciar nuevamente la configuración actual de la terminal.
```bash
reset xterm
```

Una vez dentro de la máquina remota y navegando por el sistema de archivos podemos notar que no tenemos acceso directamente hacia el contenido del usuario ash:

![img](/img/tabby/Untitled (36).png)

Como encontramos un usuario ash y no podemos acceder a él, debaríamos por lo tanto, realizar un **user pivoting**.

Como el servidor web estaba montado en el directorio **/var/www/html**, lo lógico es buscar pistas para poder encontrar alguna forma de cambiarnos hacia el usuario ash.

Realizamos un cambio de directorio y listamos los archivos que se encuentran dentro:

![img](/img/tabby/Untitled (37).png)

Navegando por el directorio files logramos encontrar un archivo .zip el cual tiene una contraseña:

![img](/img/tabby/Untitled (38).png)
![img](/img/tabby/Untitled (39).png)

Intentamos realizar diferentes combinaciones de contraseñas, tales como:

- $3cureP4s5w0rd123!
- admin
- admin123
- tomcat
- tabby

Pero ninguna de estas dió con éxito la contraseña correcta, es por ello que usé la herramienta **zip2john**.

Antes de operar la herramienta es necesario poder descargar el fichero hacia nuestra máquina de trabajo y poder operar de forma más comoda. Como el comando **get** no es reconocído por la máquina víctima, empelamos un comando que nos genere un código **base64** para de alguna forma importar los archivos hacia nuestra máquina de ataque.

Para ello empleamos el comando:
### Redes Sociales

![img](/img/linkedin.png)|[Linkedin](https://www.linkedin.com/in/clvasquezc/)|![img](/img/youtube.png)|[Youtube](https://www.youtube.com/channel/UCKXMoG8CoHrbEC8UvpZVSCw)|![img](/img/github.png)|[Github](https://github.com/clvasquezc)
