# Aprende a Encontrar Directorios Ocultos en la Web

## ¿Qúe es el Descubrimiento de Contenido?
En primer lugar, debemos preguntarnos, en el contexto de la seguridad de las aplicaciones web, ¿qué es el contenido? El contenido puede ser muchas cosas, 
un archivo, un video, una imagen, una copia de seguridad, una característica del sitio web. Cuando hablamos de descubrimiento de contenido, no nos referimos a las cosas obvias que podemos ver en un sitio web; son las cosas que no se nos presentan de inmediato y que no siempre estaban destinadas al acceso público.
Este contenido podría ser, por ejemplo, páginas o portales destinados al uso del personal, versiones anteriores del sitio web, archivos de respaldo, archivos de configuración, paneles de administración, etc.
Hay tres formas principales de descubrir contenido en un sitio web que cubriremos. Manualmente, Automatizado y OSINT (Open-Source Intelligence).

## Descubrimiento de Forma Manual - Robots.txt
Hay varios lugares que podemos verificar manualmente en un sitio web para comenzar a descubrir más contenido, como por ejemplo el archivo Robots.txt.

El archivo robots.txt es un documento que le dice a los motores de búsqueda qué páginas pueden y qué páginas no pueden mostrar en los resultados de su 
motor de búsqueda o prohíben que motores de búsqueda específicos rastreen el sitio web por completo. Puede ser una práctica común restringir ciertas 
áreas del sitio web para que no se muestren en los resultados del motor de búsqueda. Estas páginas pueden ser áreas como portales de administración o 
archivos destinados a los clientes del sitio web. Este archivo nos brinda una gran lista de ubicaciones en el sitio web que los propietarios no quieren 
que descubramos como probadores de penetración.

## Descubrimiento de Forma Manual - Favicon
El favicon es un pequeño icono que se muestra en la barra de direcciones del navegador o en la pestaña que se usa para marcar un sitio web.
A veces, cuando se utilizan marcos para crear un sitio web, sobra un favicon que es parte de la instalación, y si el desarrollador del sitio web no lo 
reemplaza con uno personalizado, esto puede darnos una pista sobre qué marco está en uso. OWASP aloja una base de datos de íconos de marco comunes que 
puede usar para compararlos con el favicon de destino https://wiki.owasp.org/index.php/OWASP_favicon_database. Una vez que conocemos la pila del framework, 
podemos usar recursos externos para descubrir más sobre él (ver la siguiente sección).

## Descubrimiento de Forma Manual - Sitemap.xml
A diferencia del archivo robots.txt, que restringe lo que pueden ver los rastreadores de los motores de búsqueda, el archivo sitemap.xml proporciona una 
lista de todos los archivos que el propietario del sitio web desea incluir en un motor de búsqueda. A veces, estos pueden contener áreas del sitio web en 
las que es un poco más difícil navegar o incluso enumerar algunas páginas web antiguas que el sitio actual ya no usa pero que todavía funcionan detrás de 
escena.

## Descubrimiento de Forma Manual - Headers HTTP
Cuando hacemos solicitudes al servidor web, el servidor devuelve varios encabezados HTTP. Estos encabezados a veces pueden contener información útil, 
como el software del servidor web y posiblemente el lenguaje de programación/script en uso. En el siguiente ejemplo, podemos ver que el servidor web es 
NGINX versión 1.18.0 y ejecuta PHP versión 7.4.3. Usando esta información, podríamos encontrar versiones vulnerables del software que se está utilizando. Intente ejecutar el siguiente comando curl en el servidor web, donde el interruptor -v habilita el modo detallado, que generará los encabezados (¡podría haber algo interesante!).
