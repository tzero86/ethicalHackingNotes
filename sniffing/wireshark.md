---
description: >-
  En esta práctica veremos el uso básico de Wireshark para el análisis de
  paquetes de red.
---

# Sniffing con Wireshark

## Uso Básico de Wireshark

![](../.gitbook/assets/1280px-Wireshark_Logo.svg\[1].png)

&#x20;**Wireshark‎**‎ es un analizador de protocolos de red más utilizado del mundo. Permite ver lo que está sucediendo en la red en detalle y es el estándar en muchas empresas comerciales y sin fines de lucro, agencias gubernamentales e instituciones educativas. Wireshark es la continuación de un proyecto iniciado por Gerald Combs en 1998 llamado **Ethereal**.‎

Al abrir Wireshark nos aparecerá una pantalla similar a la siguiente, veremos a continuación los controles más importantes que usaremos de aquí en adelante.

![](<../.gitbook/assets/image (58).png>)

1. **`Filtros:`** Una de los controles que más usaremos en Wireshark es la barra de filtros. Mediante el uso de filtros podemos identificar los paquetes que cumplan uno o más criterios de filtrado. Veremos lo básico de filtros más adelante.
2. **`Archivos Recientes`**: Esta sección (Vacía por default al instalar Wireshark de cero) recopila los últimos archivos PCAP que hayamos abierto. También nos permite abrir archivos directamente haciendo click en el texto **`Open`**.
3. **`Interfaces para Captura:`** En esta sección podemos ver el listado de las interfaces presentes en el equipo donde se ejecuta Wireshark. Esta sección varía de equipo a equipo. Se puede seleccionar una interfaz en particular y comenzar a escuchar su trafico de red haciendo **doble click sobre el nombre de la interfaz**. El gráfico representa la actividad de red en cada interfaz.

### Coloring Rules en Wireshark&#x20;

En Wireshark los paquetes se clasifican por color según el tipo de protocolo usado y errores encontrados en estos. Para poder ver las reglas de colores para los paquetes de red, vamos a: **`View -> Coloring Rules`**.

![](<../.gitbook/assets/image (85).png>)

Como podemos observar Wireshark nos presenta todas las reglas de coloreado que están actualmente configuradas.

![](<../.gitbook/assets/image (22).png>)

Es importante tener presente esta configuración de colores y comenzar a familiarizarnos con ella, dado que es una forma visualmente sencilla de identificar paquetes y protocolos de forma rápida.

### Usando Filtros

Existe una gran cantidad de filtros disponibles en Wireshark que nos permitirán refinar nuestras búsquedas hasta encontrar el tráfico de red específico a una dirección IP, MAC Address, Tipo de Protocolo, etc. No cubriremos todos los posibles filtros en esta sección, nos limitaremos a los necesarios para entender el funcionamiento general de los mismos y los usaremos a lo largo de esta práctica.

Los filtros en Wireshark utilizan **`operadores lógicos`** y  **`operadores de comparación`** como en los lenguajes de programación. Haciendo uso de estos operadores podemos combinar nuestros filtros para realizar filtrados refinados para encontrar exactamente el tráfico de red que queremos capturar y ver.

#### Operadores de Comparación

Los operadores de comparación nos permiten comparar valores en nuestras expresiones de filtrado. Estos operadores son los siguientes:

| Operador                    | Uso                                             |
| --------------------------- | ----------------------------------------------- |
| **`eq`** o **`==`**         | Igual a x valor.                                |
| **`ne`** o **`!=`**         | No igual (not equal) a x valor.                 |
| **`gt`** o **`>`**          | Mayor que x valor.                              |
| **`lt`** o **`<`**          | Menos que x valor.                              |
| **`ge`** o **`>=`**         | Mayor o igual que x valor.                      |
| **`le`** o **`<=`**         | Menor o igual que x valor.                      |
| **`contains`**              | Contiene x valor.                               |
| **`matches`** o **`~`**     | Cumple una expresión regular (Perl-Compatible). |
| **`bitwise_and`** o **`&`** | Bitwise AND is non-zero.                        |

#### Operadores Lógicos

Los **`operadores Lógicos`** nos permiten comparar expresiones de filtrado. Estos operadores son los siguientes:

| Operador              | Uso                                                                                  |
| --------------------- | ------------------------------------------------------------------------------------ |
| **`and`** o **`&&`**  | Nos permite enlazar condiciones, todas deben cumplirse.                              |
| **`or`** o **`\|\|`** | Nos permite enlazar condiciones, al menos una debe cumplirse.                        |
| **`xor`** o **`^^`**  | Únicamente una de las condiciones debe cumplirse.                                    |
| **`not`** o **`!`**   | Negación, la condición no debe cumplirse.                                            |
| **`[…​]`**            | Permite seleccionar una sub-secuencia.                                               |
| **`in`**              | Nos permite ver si el valor es parte de un set. Por ej. **`tcp.port in {80, 8080}`** |

Cabe destacar que en Wireshark los filtros se clasifican en dos grandes grupos, **`Capture Filters`** (Filtros de Captura) y **`Display Filters`** (Filtros de visualización). Veamos a continuación un breve detalle de cada uno.

#### Capture Filters

Los filtros de captura son aplicados **antes de iniciar la captura** de paquetes y no pueden ser alterados durante la captura. Si prestamos atención en la pantalla inicial de Wireshark, este control también se presenta como una barra de búsqueda en la sección **`Capture`**:

![](<../.gitbook/assets/image (133).png>)

Este tipo de filtros nos permite por ejemplo capturar el tráfico de un rango de IP en particular, el tráfico de un tipo de protocolo en particular, el tráfico únicamente del protocolo IPV4, etc.&#x20;

Cabe mencionar que estos filtros también son accesibles desde el menu de Wireshark: **`Capture -> Options (CTRL + K)`**.

![](<../.gitbook/assets/image (14).png>)

{% hint style="info" %}
En esta ventana también podemos activar/desactivar el modo promiscuo de Wireshark.

**Promiscuous mode:** En modo activo Wireshark tratará de detallar todo paquete de red que vea en la interfaz, este o no dirigido a nuestro equipo.\
\
**Non-Promiscuous Mode:** En este modo Wireshark únicamente interceptará el tráfico de red de la interfaz seleccionada si su origen o destino es dirigido a nuestro equipo.
{% endhint %}

| Uso                                                                                 |   Capture Filter   |
| ----------------------------------------------------------------------------------- | :----------------: |
| Capturar tráfico IPV4, ignorando otros protocolos como ARP,etc.                     |       **ip**       |
| Capturar tráfico por puerto, por ejemplo **`port 53`** capturará el tráfico de DNS. |     **port #**     |
| Capturar el tráfico desde/ hacia el IP especificado.                                |   **host \<IP>**   |
| Captura el tráfico por rango de puertos, por ejemplo **`tcp portrange 1200-2112`**. | **tcp portrange**  |

{% hint style="info" %}
Más sobre **`Capture filters`** en la documentación oficial de Wireshark. \
Click en el siguiente :point\_right: [**link**](https://gitlab.com/wireshark/wireshark/-/wikis/CaptureFilters).
{% endhint %}

#### Display Filters

Los **`Display Filters`**, nos permiten realizar filtrado de datos directamente sobre la lista de resultados y son los que usaremos regularmente para ubicar el tráfico de red que nos interese inspeccionar. Wireshark utiliza este tipo de filtros para las [**`Coloring Rules`**](wireshark.md#coloring-rules-en-wireshark) que vimos antes y es una de sus principales funcionalidades.  Cabe destacar que la cantidad de filtros que se pueden utilizar es enorme y esta fuera del alcance de esta práctica verlos en detalle. Mencionaremos sin embargo, los que usaremos en esta práctica.

![](<../.gitbook/assets/image (74).png>)

{% hint style="info" %}
Según la misma :point\_right: [Wiki de Wireshark](https://www.wireshark.org/docs/dfref/), actualmente más de **261000** campos de **3000** protocolos son soportados para su uso con filtros.
{% endhint %}

| Uso                      | Display Filter                            |
| ------------------------ | ----------------------------------------- |
| Filtrar por IP (Destino) | **ip.dest == 192.168.1.1**                |
| Filtrar por IP (Origen)  | **ip.src == 192.168.1.2**                 |
| Filtrar por IP           | **ip.addr == 192.168.1.3**                |
| Filtrar por Subnet       | **ip.addr = 192.168.1.1/24**              |
| Filtrar por puerto(TCP)  | **tcp.port == 21**                        |
| Filtrar por URL          | **http.host == "tzero86bits.tk"**         |
| Filtrar por MAC Address  | **eth.addr == 00:65:C7:16:25:F2**         |
| Filtrar por TimeStamp    | **frame.time >= "Mayo16, 1986 11:16:00"** |

{% hint style="info" %}
Más sobre **`Display Filters`** en la documentación de Wireshark. \
Click en el siguiente :point\_right: [**link**](https://gitlab.com/wireshark/wireshark/-/wikis/DisplayFilters)**.**
{% endhint %}

## Analizando paquetes de red (FTP Sniffing)

Veamos ahora como podemos capturar las credenciales de logueo a un servicio de FTP analizando el trafico de red de una interfaz. Cabe destacar que este mismo proceso de análisis puede llevar a cabo de igual manera luego de abrir archivo **`PCAP/PCAPNG`**.&#x20;

Para esta práctica voy a utilizar lo siguiente:

* **Interfaz**: **`eth0`**
* **Puerto: `21`**

{% hint style="success" %}
Si quieres realizar esta práctica locamente, al menos la parte del análisis. Puedes descargar el archivo **`PCAPNG`** desde el link debajo :point\_down:&#x20;
{% endhint %}

{% file src="../.gitbook/assets/UTN_CEH_FTP_LOGIN_TZERO86_1.pcapng.gz" %}
ARHIVO PCAPNG PARA ESTA PRACTICA
{% endfile %}

Para comenzar en la pantalla principal de Wireshark aplicaremos un filtro de captura, seleccionando únicamente la interfaz eth0. Para lo cual podemos directamente hacer doble click sobre el nombre de la interfaz:

![](<../.gitbook/assets/image (92).png>)

Luego de esto Wireshark comenzará a capturar el tráfico de red de esa interfaz automáticamente. Dejamos la captura activa y nos dirigimos a la terminal para conectarnos por **`FTP`** a nuestra **`VM`** e intentamos loguearnos. Luego de esto volvemos a **`Wireshark`** y hacemos click en el botón rojo de stop para detener la captura.

Comenzamos conectándonos al servidor FTP, en este caso usé un usuario recientemente configurado para el acceso a FTP.

{% hint style="info" %}
Para esta práctica también usare el Server de mi Lab local de Active Directory. Mas detalle en el siguiente :point\_right: [**link**](../scanning/running-scans-with-nmap.md#intro-distintos-tipos-de-scans-con-nmap).
{% endhint %}

![](<../.gitbook/assets/image (219).png>)

Una vez detenido el proceso de captura, debemos ver algo similar a esto en Wireshark:

![](<../.gitbook/assets/image (11).png>)

Ahora hagamos usos de distintos filtros que nos permitan ubicar específicamente el tráfico FTP. Por ejemplo podemos filtrar por el trafico en el puerto 21:

```
tcp.port == 21
```

Y como podemos ver Wireshark aplica el Display Filter correspondiente sobre los resultados:

![](<../.gitbook/assets/image (189).png>)

Alternativamente podemos directamente filtrar por el protocolo FTP simplemente escribiendo:

```
ftp
```

![](<../.gitbook/assets/image (106).png>)

También nos puede interesar ver el tráfico del puerto **`20 (FTP-DATA)`** junto con el del puerto **`21 (FTP)`**:

```
tcp.port == 20 || tcp.port == 21
```

![](<../.gitbook/assets/image (156).png>)

Filtremos directamente por ftp y hagamos click derecho en el paquete número 58 y elegimos la opción **`Follow -> TCP Stream.`** De esta manera podemos seguir el paso a paso del trafico de red generado por nuestro intento de login al la maquina virtual Windows Server.

{% hint style="success" %}
Combinación de teclas para TCP Stream: **CTRL + ALT + SHIFT + T**&#x20;
{% endhint %}

![](<../.gitbook/assets/image (96).png>)

Al abrirse el TCP Stream, veremos la secuencia de interacción entre el usuario y el servicio FTP. Vemos que el usuario listó los archivos del directorio y descargo el mismo.

![](<../.gitbook/assets/image (220).png>)

Como podemos ver las credenciales de login fueron **`ftpuser:Test123`** y el usuario descargó el archivo **`Executive_Secrets.txt`** desde el servidor FTP a su equipo. Veamos como podemos ubicar esta acción en el tráfico de red y extraer los contenidos de ese archivo.

Primero filtremos por **`ftp-data`**:

![](<../.gitbook/assets/image (147).png>)

Luego seleccionamos el paquete **`386`** donde se hace la transferencia del archivo **`(RETR)`**, y seguimos el **`TCP Stream`** como vimos antes.

![](<../.gitbook/assets/image (10).png>)

Finalmente podemos ver el contenido del archivo descargado por el usuario durante la sesión FTP. Y podemos guardarlo en nuestro equipo haciendo uso de las opciones que nos provee Wireshark.

![](<../.gitbook/assets/image (20).png>)

Wireshark nos permite cambiar el formato en el que se muestra la data, lo que es útil si en lugar de una archivo de texto plano estuviéramos tratando de recuperar un ejecutable por ejemplo. En ese caso podemos ver el contenido en modo **`raw`** y guardarlo como **`.exe`**.

Hasta acá vimos como usar lo básico de **`Wireshark`** y repasamos algunos de los filtros que nos serán de utilidad a la hora de analizar tráfico de red. **Wireshark** dispone de muchísimas más opciones y no está en el alcance de esta práctica cubrir esta herramienta a fondo.

{% hint style="info" %}
Estas prácticas están sujetas a modificaciones y correcciones, la versión más actualizada disponible se encuentra online en [el siguiente link](https://tzero86.gitbook.io/tzero86/).
{% endhint %}
