[English](https://emersontech.github.io/en/index.html)|[Contenido](https://emersontech.github.io/es/nav/page1.html)|[Autor](https://emersontech.github.io/es/nav/about.html)|[Linkedin](https://www.linkedin.com/in/emersontech/)

# Encuentra Vulnerabilidades en la Red // Tutorial Nmap para Hackers // EP 1
Nmap es la herramienta numero uno utilizada por los hackers, se utiliza para identificar y escanear sistemas en la red. Es una parte importante del diagnóstico de red y la evaluación de sistemas conectados a la red. En este video, aprendereás los conceptos básicos de esta herramienta y cómo se puede usar de manera eficiente para mapear la red interna mediante la identificación de hosts activos y la exploración de puertos, la enumeración de servicios y la detección del sistema operativo.

<iframe width="560" height="315" src="https://www.youtube.com/embed/zoOAnbVplSI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Nmap Cheat Sheed
Abajo encontrás una hoja de trucos (cheat sheed) sobre algunas técnicas de auditorías y recomendaciones de uso de la herramienta en entornos C.T.F.

### Escaneo Básico.
```bash
nmap -oA <file-name> <target-ip>
```

### Escaneo a Todos los Puertos.
```bash
nmap -p- <target-ip>
```

### Escaneo a Puertos Específicos.
```bash
nmap -p<ports> -oA <file-name> <target-ip>
```

### Escaneo a Rango de Puertos.
```bash
nmap -p<from_number>,<to_number> <target-ip>
```

### Escaneo a los Puertos Top 100
```bash
nmap -F <target-ip>
```

### Escaneo a Todos los Puertos.
```bash
nmap -p- -oA <file-name> <target-ip>
```

### Guardar los escaneos en todos los formatos (-oN, -oG, -oX).
```bash
nmap -oA <file-name> <target-ip>
```

### Técnicas de Optimización de Escaneos.
```bash
nmap -n -T5 --open <target-ip>
```

### Escaneos Para C.T.F - Primer Escaneo // Básico.
```bash
nmap --open -T5 -v -n <ip-target> -oA/oN/oG/oX <file-name>
```

### Escaneos Para C.T.F - Segundo Escaneo // Escaneo A Todos los Puertos.
```bash
nmap -p- --open -T5 -v -n <ip-target> -oA/oN/oG/oX <file-name>
```

### Escaneos Para C.T.F - Tercer Escaneo // Escaneo Avanzado Con Más Detalles.
```bash
nmap -p- -sS --min-rate 5000 --open -vvv -n <ip-target> -oA/oN/oG/oX <file-name>
```

### Escaneos Para C.T.F - Cuarto Escaneo // Explotando Las Versiones De Servicios y Sus Vulnerabilidades
```bash
nmap -sCV -p<port-number,range> -oA/oN/oG/oX <file-name>
```

### Redes Sociales

![img](/img/linkedin.png)|[Linkedin](https://www.linkedin.com/in/emersontech/)|![img](/img/youtube.png)|[Youtube](https://www.youtube.com/channel/UChNTj2xNpEQiliMv-IJbWvQ)|![img](/img/github.png)|[Github](https://github.com/emersontech)
