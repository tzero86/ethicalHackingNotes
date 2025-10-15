---
description: >-
  En esta práctica veremos como usar Shodan para localizar servers con puertos
  22 y 23 abiertos y usaremos nmap para obtener información básica de los
  objetivos.
---

# Basic Scanning (Shodan.io & Nmap)

{% hint style="info" %}
**CEH:** "_En el escaneo, por lo general, encontramos puertos abiertos, cerrados y filtrados. Cada uno son servicios totalmente diferentes, excepto unos puertos utilizados en cuanto a la presentación de un sitio web (80 o 8080). Una de las mayores vulnerabilidades es encontrar puertos de sencillo acceso, tanto el puerto 22 (SSH) como el puerto 23 (Telnet)."_
{% endhint %}

En esta **`mini-práctica`** veremos como usar **`Shodan`** para localizar servers que tengan determinados puertos abiertos. Para el caso de este ejemplo estamos interesados en encontrar los puertos **`22`** y **`23`** abiertos. Luego usaremos **`nmap`** para confirmar que dichos servidores en efecto tienen ambos puertos abiertos.

{% hint style="danger" %}
**Wikipedia:** **Telnet** (**Tel**etype **Net**work​) es el nombre de un [protocolo de red](https://es.wikipedia.org/wiki/Protocolo_de_red) que nos permite acceder a otra máquina para [manejarla remotamente](https://es.wikipedia.org/wiki/Administraci%C3%B3n_remota). También es el nombre del [programa informático](https://es.wikipedia.org/wiki/Programa_inform%C3%A1tico) que implementa el [cliente](https://es.wikipedia.org/wiki/Cliente_\(inform%C3%A1tica\)). Su mayor problema es de seguridad, ya que todos los **nombres de usuario y contraseñas necesarias para entrar en las máquinas viajan por la** [**red**](https://es.wikipedia.org/wiki/Red_de_telecomunicaci%C3%B3n) **como** [**texto plano**](https://es.wikipedia.org/wiki/Texto_plano) (cadenas de texto sin [cifrar](https://es.wikipedia.org/wiki/Cifrado_\(criptograf%C3%ADa\))). Esto facilita que cualquiera que espíe el tráfico de la red pueda obtener los nombres de usuario y contraseñas. Por esta razón dejó de usarse, ante la llegada de **SSH**.
{% endhint %}

{% hint style="success" %}
**Wikipedia:** **SSH** (o **S**ecure **SH**ell) es el nombre de un [protocolo](https://es.wikipedia.org/wiki/Protocolo_\(inform%C3%A1tica\)) y del [programa](https://es.wikipedia.org/wiki/Programa_\(computaci%C3%B3n\)) que lo implementa cuya principal función es el [acceso remoto](https://es.wikipedia.org/wiki/Administraci%C3%B3n_remota) a un servidor por medio de un canal seguro en el que toda la información está cifrada.  SSH permite copiar datos de forma segura (tanto archivos sueltos como simular sesiones [FTP](https://es.wikipedia.org/wiki/File_Transfer_Protocol) cifradas), gestionar [claves RSA](https://es.wikipedia.org/wiki/Claves_RSA) para no escribir contraseñas al conectar a los dispositivos y pasar los datos de cualquier otra aplicación por un canal seguro [tunelizado](https://es.wikipedia.org/wiki/Protocolo_tunelizado) mediante SSH y también puede redirigir el tráfico del ([Sistema de Ventanas X](https://es.wikipedia.org/wiki/Sistema_de_ventanas_X)) para poder ejecutar programas gráficos remotamente.
{% endhint %}

## Localizando servers con puerto 22, 23 abiertos.

**Shodan** nos permite realizar búsquedas tanto desde su sitio web como desde la terminal utilizando una llave de acceso a la API. En este caso usaremos rápidamente la versión web, [`Shodan.io`](https://www.shodan.io/) utilizando la búsqueda. La cual refinaremos con el uso de **`filtros`**, en este caso el filtro por puertos llamado **`port:`**.

Este filtro lo podemos utilizar de la siguiente manera:

{% hint style="success" %}
En la barra de búsqueda ingresamos el texto **`port:`** seguido del número de puerto que por el que queremos filtrar. Por ejemplo: **`port:80`**.
{% endhint %}

![](https://i.imgur.com/A0cICh6.png)

Está búsqueda con **`shodan`** la realizaremos para encontrar los objetivos para esta práctica, basta con seleccionar de la lista de resultados ofrecidos por **`shodan`** las **`direcciones IP`**. Esas direcciones IP las usaremos a continuación para confirmar que los puertos abiertos para cada objetivo reportados por **`shodan`** están en efecto abiertos. Para esa confirmación usaremos la herramienta **`nmap`**.

## Escaneando objetivos con nmap

Para el scanning voy a utilizar **`nmap`** como herramienta, la misma viene preinstalada en distribuciones como [**`Kali Linux`**](https://www.kali.org/).&#x20;

### Puerto 22

Como primer ejemplo usare el objetivo **`35.199.79.95`** al escanearlo con **`nmap`** vemos que dicho server tiene el puerto **`22`** abierto. Para esto usaremos nmap con una serie de switches o flags que nos permiten refinar el tipo de scan, velocidad y puerto.

{% hint style="info" %}
En esta mini-práctica no vamos a ahondar en **`nmap`**. Para conocer más sobre esta herramienta y como realizar distintos tipos de escaneos, sigue este :point\_right: [**link**](running-scans-with-nmap.md).&#x20;
{% endhint %}

![](https://i.imgur.com/47SVpSo.png)

Una vez completado el scan vemos que obtenemos confirmación de que el objetivo cuenta con el puerto **`22`** abierto como había sido reportado por la búsqueda que realizamos en **`shodan`**. Si observamos con atención el resultado del scan, vemos que nmap nos devuelve bastante información adicional.

### Puerto 23

Busquemos ahora algún objetivo que tenga el puerto **`23`** abierto, para lo cual refinamos nuestra búsqueda en **`shodan`** con el filtro **`port:23`**, alternativamente podemos buscar directamente por el nombre del servicio, `telnet`.&#x20;

{% hint style="info" %}
Para encontrar **`servidores`** con el servicio de **`telnet`** activo, basta con ingresar el **`nombre del servicio`** en la barra de búsqueda. Esto funciona con otros servicios como por ejemplo **`SSH`**.
{% endhint %}

Una vez elegido el objetivo procedemos a escanearlo con nmap para confirmar que en efecto el puerto **`23`** esta abierto. En mi caso elegí **`67.201.141.136`** como objetivo:

![](https://i.imgur.com/AxlaXZ3.png)

Como vemos en los resultados el scan confirman que el puerto **`23`** se encuentra abierto. En este caso en particular también el puerto **`21`** se encuentra abierto. De esta manera vimos como ubicar servidores con determinados puertos abiertos y como usando nmap podemos comprobar que en efecto se encuentran abiertos.

