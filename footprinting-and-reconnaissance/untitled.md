---
description: En esta práctica veremos como usar Maltego para realizar un footprinting.
---

# Footprinting with Maltego

![](../.gitbook/assets/image%20%2875%29.png)

En esta práctica usaremos **`Maltego`** en su versión gratuita \(`Community Edition`\) para entender como podemos, utilizando esta herramienta, llevar a cabo un **`footprinting`** de una web objetivo.

## Instalando Transforms

En Maltego los "Módulos" se llaman `Transforms` cada uno de ellos aporta funcionalidades y diversos tipos de `scans` que podemos utilizar.

Para instalar **`transforms`**, Maltego dispone de una sección llamada **`transform hub`**:

![](https://i.imgur.com/2jLKfOH.png)

El `hub` es una suerte de `store o market` donde podemos encontrar`transforms` pagos, gratuitos y algunos que ofrecen free trials \(pruebas gratuitas\). Disponemos de `filtros` para refinar nuestra búsqueda. En este caso en particular usaremos todos `transforms gratuitos` para hacer nuestra práctica de reconocimiento web.

En mi caso voy a utilizar los siguientes transforms gratuitos:

![](https://i.imgur.com/2WCbem9.png)

La instalación de los `transforms` es sencilla, basta con `clickear` cada una y elegir la opción `install`. Luego una ventana se abrirá para comenzar la instalación.

![](https://i.imgur.com/Zl42thC.png)

Algunos `transforms` como el de `Shodan`, requieren una `API Key` para funcionar y pedirán la misma durante la instalación:

![](https://i.imgur.com/tz71trn.png)

## Reconocimiento web con Maltego

El objetivo de esta práctica es generar un `reconocimiento web usando Maltego y los transforms` que instalamos previamente.

### Creando un nuevo scan

Para iniciar generamos un nuevo `graph` desde el menú de Maltego:

![](https://i.imgur.com/LQyXoPm.png)

Una vez creado vemos que disponemos de una suerte de `canvas` vacío donde vamos a poder organizar los elementos de nuestro scan. Estos elementes en Maltego se llaman `Entities`. Podemos ver una lista de cada una en el `panel a la izquierda` de nuestro `canvas`, diferenciados por categorías.

### Definiendo el Dominio \(Entities\)

Las `Entities` nos permiten ubicar en el `canvas` los distintos tipos de `dispositivos`, `eventos`, `infraestructuras`, `locations`, `Personal`, etc.

Para comenzar nuestro scan, buscamos en la lista de entidades la entidad llamada `Domain`:

![](https://i.imgur.com/ewAAXik.png)

Para agregar nuestra entidad al `canvas`, basta con arrastrarla y soltarla sobre el mismo. Por default esta entidad apunta a `paterva.com`. Necesitamos ajustar ese valor y apuntarlo a nuestro objetivo. Para ello disponemos de 2 formas:

* **Opción 1:** Hacer `doble click` en el `texto` de la `entidad` y cambiar el `valor` al `dominio objetivo`:
  * ![](https://i.imgur.com/d6OIkRN.png)
* **Opción 2:** Editar el `dominio` usando el panel de propiedades de la `entidad` \(este panel es genérico a cualquier `entidad` que tengamos seleccionada\):
  * ![](https://i.imgur.com/K4Nx6PL.png)

En mi caso utilizaré como `objetivo` una web de noticias online:

* `https://semanarionuestragente.com/`

### Realizando el primer scan Manual

En Maltego cada `entity` nos ofrece diversos tipos de scan \(en realidad también son llamados `transforms`\). Los mismos son habilitados por los `transforms` que tengamos instalados. Cada entidad puede contener distintos tipos de `scans` disponibles según su tipo. Para ver los `scans` disponibles, podemos hacer `click derecho` sobre la entidad:

![](https://i.imgur.com/mUCw5A4.png)

Vemos que el menú contextual que se despliega es llamado `Run Transforms`. El mismo nos presenta cada `transform` instalado, podemos hacer `click` sobre alguno en particular o bien hacer `click` en `All Transforms` para ver la lista completa de opciones disponibles:

![](https://i.imgur.com/s0EL8nJ.png)

Comenzaremos por hacer un scan del tipo `whois`. Podemos usar la barra de búsqueda del menú contextual para refinar la lista y por ejemplo ver los `scans` de tipo `whois` que están disponibles:

![](https://i.imgur.com/vSU6ymG.png)

En este caso probaremos el `transform` \(scan\) llamado `to DNS - NS (Name Server)`. Al hacerle `click` el `scan/transform` seleccionado es ejecutado. Vemos que luego de un momento `2` nuevas entidades aparecen en nuestro `canvas`. También podemos ver que cada `transform/scan` genera un `log` al ejecutarse que se muestra en la ventada de `output` debajo del `canvas`:

![](https://i.imgur.com/GMryuvF.png)

De esta manera vemos que obtenemos ambos `Name Servers` que están vinculados a nuestro objetivo. Estas nuevas entidades nos permiten correr `scans` adicionales. Veamos cuales están disponibles para `ns69.domaincontrol.com`:

![](https://i.imgur.com/6f5w3wZ.png)

Probemos correr el `transform` llamado `To Domains [Sharing this NS]` y al correr vemos que nos actualiza el `canvas` con todos los dominios que también utilizan ese mismo `Name Server`:

![](https://i.imgur.com/1cMuUgZ.png)

Ya nos podemos ir dando una idea del potencial de Maltego para hacer reconocimiento de nuestros objetivos.

Tomemos para nuestro próximo scan a `jamibgoode.com` y corramos el `transform` llamado `To Email Address [from whois info]`:

![](https://i.imgur.com/4tMGV8j.png)

Vemos que de esta manera logramos listar el correo que esta especificado en los registros de `whois` para ese dominio. De esta forma podemos comenzar a obtener información sobre nuestro objetivo, pero Maltego también nos ofrece otra forma automatizada de realizar escaneos de `footprinting` usando lo que se llama `machines`.

## Usando Machines para Footprinting Automático

Maltego pone a nuestra disposición diferentes `machines` que son una suerte de `scans pre-seteados` que podemos ejecutar automáticamente para el dominio objetivo que hemos definido. Veamos como podemos usar `machines` para hacer `footprinting`, esta vez para el dominio `kimballoon.com`:

![](https://i.imgur.com/QauHK22.png)

Primero ubicamos la `machine` que deseamos ejecutar, para esta práctica usaremos la llamada `Footprint L1`. Basta con hacer `click` en la machine deseada para ejecutarla:

{% hint style="warning" %}
En la versión **`community`** de **`Maltego`** esta limitada la funcionalidad y potencia de estas **`machines.`**
{% endhint %}

![](https://i.imgur.com/xb1uLqi.png)

Luego de ejecutar la **`machine`**, en la parte superior derecha de la pantalla de Maltego vemos el resultado de los `scans/transforms` ejecutados por dicha `machine`. Y al ver el `canvas` vemos que tenemos múltiples nuevas `entidades`, cada una de las cuales nos sigue proveyendo de información adicional para nuestro esfuerzo de `footprinting`:

![](https://i.imgur.com/etwMmj1.png)

Vemos que en este caso el `footprinting` realizado nos genera en nuestro `canvas` una considerable cantidad de nuevas `entidades` de variados tipos. Cada una de las cuales nos permite seguir utilizando `transforms` adicionales para intentar conseguir información y detalles adicionales que luego podemos recopilar para tener un panorama lo más completo posible de nuestro objetivo.

Como podemos observar el poder de Maltego es considerable y la facilidad de uso que nos ofrece la convierte en una herramienta formidable para nuestros esfuerzos de **`footprinting`** `y` **`reconocimiento.`**

Durante esta simple práctica vimos algunas de las funcionalidades que Maltego ofrece, ciertamente hay muchas más por descubrir, aprender y utilizar. Espero que este texto sea útil y sirva para comenzar a explorar esta poderosa herramienta.

