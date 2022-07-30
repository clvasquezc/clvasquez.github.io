# Resolviendo la Máquina Goodgames de HackTheBox

![GoodGames.png](emersontech.github.io/img/goodgames.png)

# Resumen
GoodGames es una máquina Easy Linux que muestra la importancia de desinfectar las entradas del usuario en
aplicaciones web para prevenir ataques de inyección SQL, utilizando fuertes algoritmos hash en la base de datos
estructuras para evitar la extracción y el descifrado de contraseñas de una base de datos comprometida,
junto con los peligros de la reutilización de contraseñas. También destaca los peligros de usar
render_template_string en una aplicación web de Python donde se refleja la entrada del usuario, lo que permite
Ataques de inyección de plantilla del lado del servidor (SSTI). La escalada de privilegios involucra hosts docker
enumeración y muestra cómo tener privilegios de administrador en un contenedor y un usuario con privilegios bajos en
la máquina host puede ser peligrosa, lo que permite a los atacantes aumentar los privilegios para comprometer el
sistema.

# Enumeración
