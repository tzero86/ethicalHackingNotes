---
description: >-
  Para esta práctica veremos como realizar un diagrama de una red, utilizando
  NTM para realizar un scan de topología de red.
---

# Creando un diagrama de red con Network Topology Mapper

## Solarwinds Network Topology Mapper

![](../.gitbook/assets/image%20%2838%29.png)

Para esta práctica veremos como realizar un diagrama de una red, utilizando NTM para realizar un scan de topología de red.

### Conociendo NTP

SolarWinds NTM nos permite realizar un scaneo mediante el cual podemos descubrir los elementos que componen una red objetivo y según los IPs que usemos, automáticamente generar un diagrama de dicha red.

![](https://i.imgur.com/ns8Ut3r.png)

Al iniciar NTM vemos una suerte de wizard donde podemos iniciar nuestro primer scan. El wizard nos ofrece diversas opciones para configurar: Proveer credenciales, rangos de IP, Dominios, direcciones IPV6, etc.

En nuestro caso queremos realizar un scan básico por ende en el paso de `Network Selection` definiré una serie de IPs obtenidas mediante `Shodan.io`. Dichas IPs pertenecen a la `Universidad de Ljubljana en Lituania`.

Si avanzamos por el wizard tendremos la posibilidad de definir un nombre para nuestro scan, debajo de esa opción vemos también una opción para ignorar los nodos de red que no únicamente respondan a ping \(ICMP\) y no a SNMP/WMI:

![](https://i.imgur.com/MO0kZEt.png)

Si presionamos `next` llegamos a la parte de `scheduling` donde podremos configurar nuestro scan para ejecutarse inmediatamente o bajo cierta frecuencia que podemos definir manualmente.

En este caso ejecutaremos el scan inmediatamente: ![](https://i.imgur.com/lEEoTba.png)

Al darle `next` veremos que la ultima etapa del wizard es un resumen de todos los seteos que configuramos en los distintos pasos. Vemos también un botón llamado `discovery` que nos permite iniciar finalmente el proceso de scaneo:

![](https://i.imgur.com/KrBQPnC.png)

Una vez que el scan comienza vemos que abre una nueva ventana con el progreso actual del mismo:

![](https://i.imgur.com/U41SsA0.png)

Una vez que el scan termina, podemos ver listados en el panel de la izquierda los nodos que fueron detectados:

![](https://i.imgur.com/d4xhpk3.png)

Estos nodos podemos usarlos para ir construyendo nuestro diagrama, basta con arrastrarlos al mapa en blanco y serán agregados:

![](https://i.imgur.com/66Whv0O.png)

Como podemos ver el scaneo de las IPs que definimos realmente no sirvieron para generarnos un diagrama automáticamente.

### Definiendo un Objetivo para obtener un diagrama automático de red

Veamos si logramos encontrar algún IP objetivo que nos permita ver como NTM genera automáticamente un diagrama de los nodos de red luego de realizar el scaneo del objetivo. Para esto usaremos como ejemplo nuestra red local:

![](https://i.imgur.com/fvChvaF.png)

Como podemos observar esta vez obtenemos 8 nodos de red y una subnet como resultado de nuestro scan. Si arrastramos los nodos al mapa vemos que esta vez si se generan las líneas de conexión. De cada nodo podemos obtener su dirección IP, lo cual nos permitirá a futuro usar otras herramientas y obtener información adicional de cada nodo.

NTM nos ofrece distintas formas de organizar nuestro mapa de red usando las opciones listadas debajo de la lista de nodos en la sección llamada `Map Layouts`. También podemos realizar distintas acciones sobre cada nodo. Para esto podemos hacer click derecho sobre cualquier nodo y veremos las opciones disponibles:

![](https://i.imgur.com/n2xbcWd.png)

En este caso vemos que podemos realizar conexiones de escritorio remoto, TraceRoute, Ping y Telnet.

## Recolectando los resultados

Por medio de esta práctica obtuvimos los siguientes datos:

* La Subnet 192.168.1.0/24 contiene los siguientes nodos de red de los cuales obtuvimos las siguientes direcciones IP: 
  * 192.168.1.34
  * 192.168.1.34
  * 192.168.1.35
  * 192.168.1.38
  * 192.168.1.41
  * 192.168.1.43
  * 192.168.1.46

Esta recolección de información nos permite tener una idea más detallada de la red objetivo y la obtención de los IPs nos permitirá realizar distintos tipos de scaneos a futuro usando, por ejemplo, nmap para identificar puertos abiertos y servicios en ejecución.

