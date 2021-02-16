---
description: >-
  En este artículo veremos en que consiste el secuestro de sesión (Session
  Hijacking) y un simple ejemplo de reflected XSS.
---

# Session Hijacking

![](../.gitbook/assets/image%20%2848%29.png)

## ¿En que consiste el Session Hijacking?

Session Hijacking o secuestro de sesión consiste en interceptar la comunicación entre dos hosts con la intención de obtener un rol de usuario autenticado en un determinado objetivo. Normalmente esta práctica se suele realizar de modo `pasivo` o `activo` y con ataques como ser`MITM(Man-in-the-middle)`, `STP(Session token prediction)`, `TP(token tampering)` y `SP(Session Replay)`, `XSS(Cross-site-scripting)`, `SF(Session-Fixation)` entre otros. 

**`Los ataques pasivos`** involucran el monitoreo del tráfico de red entre los hosts, haciendo sniffing del tráfico interesante pero sin influir en absoluto en la comunicación. En cambio **`los ataques de tipo activos`** involucra el envío de paquetes de red por parte del atacante, lo que se conoce como `packet-injection`.

A su vez, estos tipos de ataques se pueden clasificar entre las siguientes categorías:

* **Stealing:** Ataques donde en efecto se `"roba"` la sesión. Por ejemplo utilizando `Troyanos`, `Network-Sniffing`, etc. 
* **Guessing:** Ataques donde se intenta `"adivinar"` el ID de sesión. Por ejemplo se identifica el patrón usado en la generación de `Session IDs` y se emula hasta generar uno válido. 
* **Brute-Forcing:** Ataques donde se utiliza el enfoque de fuerza bruta con el uso de scripts. Por ejemplo ataques que prueban diversas combinaciones de `usuario:password` hasta dar con el correcto.

A grandes rasgos, session-hijacking consta de las siguientes etapas:

| Etapa | Descripción |
| :--- | :--- |
| **Sniffing** | El atacante intenta interceptar el trafico entre dos hosts, para hacer sniffing del tráfico de red entre hosts. |
| **Monitoring** | Monitoreo del tráfico de red en busca de información de sesión. |
| **Session Desync** | En este punto el atacante esta listo para quebrar o de-sincronizar la conexión entre los hosts. |
| **Session ID** | El atacante toma control de la sesión, via diversas técnicas como `Session Guessing`. Para lograr generar un **`session-ID`** válido. |
| **Command Injection** | Una vez el atacante controla la sesión comienza a inyectar los comandos maliciosos o puede tomar control completo de la sesión autenticada y operar en vez del usuario legítimo. |

El secuestro de sesión se diferencia del **`Spoofing`** en un rasgo fundamental: al hacer spoofing el atacante trata de impersonar a la victima y no cuenta con una sesión de ese usuario activa. Generalmente trata de obtener sesión una mediante la ayuda de la ingeniería social y el robo de datos. En ~~**`session-hijacking`**~~ en cambio, se intenta **`tomar el control`** de una sesión autenticada existente entre la victima y un determinado host.

También hay que tener presente que el **session-hijacking se da en dos niveles**: **`Network-Level Session Hijacking`** y **`Application-Level Session Hijacking`**. Veremos a continuación sus diferencias y distintos tipos de ataques.

![](../.gitbook/assets/image%20%28124%29.png)

### Application-Level Session Hijacking

![Im&#xE1;gen por: www.starcertification.org](../.gitbook/assets/image%20%2864%29.png)

En Application-level session hijacking, el atacante intenta el secuestro de sesión como también diversas técnicas para crear nuevas sesiones\(**`session-ID`**\). En este tipo de enfoque necesitamos adaptar nuestros ataques acorde a la tecnología que alimenta por ejemplo la aplicación web que estemos intentando atacar. Para este fin se utilizan diversos tipos de ataques y técnicas, algunas de las cuales veremos a continuación.

<table>
  <thead>
    <tr>
      <th style="text-align:left">Ataque</th>
      <th style="text-align:left">Descripci&#xF3;n</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>Session Sniffing</b>
      </td>
      <td style="text-align:left">Consiste en hacer sniffing del la comunicaci&#xF3;n para obtener un Session
        Token o ID y lograr autenticarse en la aplicaci&#xF3;n web objetivo. Normalmente
        se utiliza alg&#xFA;n Network Sniffer(<b><code>Wireshark o Ettercap</code></b>)
        o mediante el uso de un web proxy como (<b><code>Burp Proxy</code></b> , <b><code>ZAP</code></b> o <b><code>Fiddler</code></b>).</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Session ID Prediction</b>
      </td>
      <td style="text-align:left">En este ataque lo que se busca en identificar el patr&#xF3;n usado en
        la generaci&#xF3;n de tokens de sesi&#xF3;n o <b><code>session-IDs</code></b>.
        De encontrarse dicho <b><code>patr&#xF3;n</code></b>, el atacante puede
        entonces comenzar a generar <b><code>session-IDs</code></b> que sean considerados
        como leg&#xED;timos por la aplicaci&#xF3;n.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>MITB(Man-in-the-browser)</b>
      </td>
      <td style="text-align:left">Similar a al ataque <b><code>Man-in-the-middle</code></b> generalmente se
        utilizan <b><code>troyanos</code></b>, y <b><code>extensiones maliciosas</code></b> para
        interceptar la comunicaci&#xF3;n entre el browser y los mecanismos de seguridad
        implementados en la aplicaci&#xF3;n. De esta manera el c&#xF3;digo malicioso
        puede directamente manipular el DOM (Document Object Model) y extraer o
        modificar la informaci&#xF3;n sin que el usuario se de cuenta.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">Dado que el servidor no tiene forma de detectar cuales valores fueron
        modificados por el atacante y cuales por el usuario, simplemente realiza
        la transacci&#xF3;n solicitada. Imaginemos este escenario en una aplicaci&#xF3;n
        de home banking, la extensi&#xF3;n maliciosa puede modificar el monto y
        la cuenta de destino de una transferencia sin que el usuario se percate
        y el atacante recibir&#xE1; esos fondos en una cuenta bajo su control.
        Incluso el c&#xF3;digo malicioso puede re-setear los valores originales
        por ejemplo cuando el servidor emite el comprobante de transferencia. De
        esta forma el usuario no detecta actividad sospechosa alguna.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>XSS(Cross site scripting)</b>
      </td>
      <td style="text-align:left">Es un ataque en el cual se inyectan scripts en sitios normalmente benignos
        con el fin de que estos scripts sean ejecutados sin problemas dado que
        el sitio web no tiene forma de saber que ese c&#xF3;digo no es parte de
        la misma web que esta sirviendo.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">Usualmente estos scripts que se inyectan son piezas de c&#xF3;digo en
        JavaScript que pueden incluir HTML. De esta forma se pueden obtener las
        cookies, tokens de sesi&#xF3;n y cualquier otra informaci&#xF3;n que sea
        almacenada por el browser o directamente hacer que se redirija al usuario
        a un servidor controlado por el atacante. Existen varios tipos de XSS:</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <ul>
          <li><b>Stored XSS:</b> Cuando el input del usuario es almacenado en el servidor,
            por ej la DB o en un comentario en un foro. Al ser visitado por otro usuario
            este c&#xF3;digo no sanitizado es ejecutado directamente dado que se almacena
            directamente en la p&#xE1;gina.</li>
          <li><b>DOM Based XSS:</b> En este caso el c&#xF3;digo malicioso no es interpretado
            directamente y en cambio el c&#xF3;digo real de la p&#xE1;gina lo incluye
            al modificar el DOM para presentar por ej los resultados de una b&#xFA;squeda.</li>
          <li><b>Reflected XSS:</b> En este caso el payload es le&#xED;do por el servidor
            y renderizado directamente. Ya sea en un mensaje de error o en resultados
            de una b&#xFA;squeda. Veremos un simple ejemplo de esta variante de XSS
            al final de este escrito.</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>CSRF(Cross site request forgery)</b>
      </td>
      <td style="text-align:left">En este tipo de ataque se intenta forzar a la victima autenticada a realizar
        ciertas acciones en una aplicaci&#xF3;n web con peticiones maliciosas.
        El posible impacto de este ataque depende de que acciones est&#xE1;n disponibles
        para la victima en la app. De ser un usuario con privilegios, la totalidad
        de la aplicaci&#xF3;n web puede verse comprometida.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"><b><code>CSRF</code></b> se ejecuta en los <b><code>cambios de estado</code></b> de
        la aplicaci&#xF3;n, por ejemplo cuando se actualizan datos del perfil como
        ser el correo, password, direcciones, transferencias de fondos, etc.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Session Fixation</b>
      </td>
      <td style="text-align:left">En este tipo de ataque se fuerza a la victima a utilizar un <b><code>session-ID</code></b> controlado
        por el atacante. Com&#xFA;nmente se enga&#xF1;a a la victima para que por
        ejemplo, haga click en un enlace de inicio de sesi&#xF3;n que ya contiene
        un <b><code>session-ID</code></b> activo.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">Cuando esto sucede el servidor habilita la sesi&#xF3;n de usuario y dado
        que el atacante posee el mismo session-ID, puede continuar operando en
        la webApp como si fuera el usuario leg&#xED;timo.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Session Replay</b>
      </td>
      <td style="text-align:left">En este tipo de ataque se hace uso de la captura de requests y el reenv&#xED;o
        de las mismas con el fin de controlar el session-ID existente y poder realizar
        operaciones maliciosas en nombre de la victima.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">En aplicaciones poco seguras, la informaci&#xF3;n de <b><code>session-ID</code></b> suele
        exponerse en forma de cookies o <b><code>par&#xE1;metros de URL</code></b> y
        en apps donde los <b><code>session-IDs</code></b> no tienen configurado una
        fecha de caducidad. Por ende la misma request, una vez es capturada de
        la victima puede reenviarse para obtener control de la sesi&#xF3;n.</td>
    </tr>
  </tbody>
</table>



### Network-Level Session Hijacking

![ Im&#xE1;gen por: www.starcertification.org](../.gitbook/assets/image%20%2882%29.png)

El Network-level session hijacking tiene la ventaja de que los ataques no deben adaptarse según la tecnología usada por ejemplo en una determinada aplicación web, dado que este ataque se realiza directamente sobre el flujo de información de red el cual es independiente de la tecnología en cual este desarrollada la aplicación.  En esta modalidad, **`el objetivo es interceptar y manipular el tráfico de red`** entre los hosts y se utilizan variadas técnicas, a continuación veremos alguna de ellas.

<table>
  <thead>
    <tr>
      <th style="text-align:left">Ataque</th>
      <th style="text-align:left">Descripci&#xF3;n</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>IP Spoofing</b>
      </td>
      <td style="text-align:left">Usando el <b><code>spoofing de IP</code></b>, el atacante puede modificar
        paquetes de red y mandarlos al servidor indicando que la IP de origen es
        la de la victima cuando en realidad esos paquetes son emitidos por el atacante.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">Estos paquetes ser&#xE1;n tomados por tr&#xE1;fico v&#xE1;lido originado
        en la <b><code>sesi&#xF3;n TCP</code></b> creada por la victima.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>TCP Hijacking</b>
      </td>
      <td style="text-align:left">En <b><code>TCP Hijacking</code></b> la idea es generar un estado en el
        que la victima y el host no puedan comunicarse y poder emitir paquetes
        de red alterados que simulen venir de ambas partes y de esta manera obtener
        el control de la sesi&#xF3;n.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">Para esto es necesario que el atacante este en la misma red que la victima.
        Luego para evitar que la victima y el server puedan comunicarse puede hacerse
        uso de ataques como <b><code>DoS(Denial-of-Service)</code></b>.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>UDP Hijacking</b>
      </td>
      <td style="text-align:left">
        <p></p>
        <p>Dado que en <b><code>UDP</code></b> los paquetes de red no precisan ser
          secuenciales, ni usa paquetes ACK este protocolo es bastante m&#xE1;s d&#xE9;bil
          que <b><code>TCP</code></b> y por ende <b><code>UDP-Hijacking</code></b> es
          bastante mas simple que el <b><code>TCP-Hijacking</code></b>.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">En <code>UDP Hijacking</code> el objetivo es crear y enviar un <code>paquete de respuesta</code> antes
        de que el servidor tenga tiempo de responder. UDP es susceptible a ataques
        como <code>MITM</code> donde el atacante puede incluso evitar que la respuesta
        del server llegue a la victima del todo.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>MITM (Man-in-the-middle)</b>
      </td>
      <td style="text-align:left">Este ataque consiste es <b><code>sniffear</code></b> los paquetes de red
        entre la victima y el servidor. En este caso el trafico entre hosts esta
        pasando por el sniffer y el atacante puede modificarlos. Una t&#xE9;cnica
        utilizada es <b><code>ARP Poisoning</code></b>, que consiste en el env&#xED;o
        de paquetes alterados para lograr que el host actualice sus <b><code>ARP Table</code></b> y
        que la <b><code>IP</code></b> de la victima sea<code> </code><b><code>mapeada</code></b> a
        la direcci&#xF3;n de hardware (<b><code>MAC Address</code></b>) del atacante.
        De esta manera el tr&#xE1;fico del server hac&#xED;a la victima sera redirigido
        hacia el atacante.</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">Otra forma es con paquetes <b><code>ICMP</code></b> alterados intentando
        hacerle creer a la victima que rutear el tr&#xE1;fico de red mediante el
        atacante es mejor que hacerlo mediante el host. En este caso <b>mejor</b> puede
        entenderse como <b>m&#xE1;s r&#xE1;pido o menos propenso a errores</b>.</td>
    </tr>
  </tbody>
</table>

### Herramientas para Session Hijacking

Algunas de las herramientas usadas para Session Hijacking a nivel de App y Network se listan a continuación:

| Tool | Descripción |
| :--- | :--- |
| **OWASP ZAP** \(Zed Attack Proxy\) | OWASP ZAP es un escáner de seguridad de aplicaciones web de código abierto. Y es utilizado para realizar auditorias de seguridad y analizar distintas vulnerabilidades en webapps. |
| **Burp Suite** | Es una herramienta integrada para realizar auditorias de seguridad en aplicaciones web. Incluye diversas herramientas para hacer reenvio de requests, modificaciones de requests, fuzzing, web crawling e incluso análisis de tokens de sesión, entre otras. |
| **JHijack** | Basado en Java es una herramienta multiplataforma para el análisis y detección de vulnerabilidades web. Algunos ataques que pueden ser simulados incluyen DOM Body Hijacking, URL Attack y session Hijacking. Require conocimientos de programación y protocolo HTTP para su uso, esta orientada a programadores. |
| **DroidSheep** | Herramienta para sniffing Mobile \(Android\) y análisis de red. Permite la captura de cookies y profiles para llevar a cabo session hijacking. |
| **DroidSniff** | Otra app Android que permite realizar análisis de seguridad en redes wireless y capturar cuentas de usuario de Facebook, Twitter entre otras. |
| **Wireshark & Ethercap** | Utilizadas entre otras cosas para realizar Network Sniffing y capturar tráfico de red sensible como tokens de sessión. |

## Simple Ejemplo de XSS

Veamos ahora un simple ejemplo de como funciona un ataque de **`Cross Site Scripting (XSS)`**. Para este ejemplo usaremos una web de práctica llamada **`XSSGame`**, en la cual podemos poner en práctica algunas diversas técnicas de **`XSS`**. En particular en este caso veremos un ataque de tipo **`Reflected XSS`**.

Anteriormente mencionamos la posibilidad de hacer robo de cookies de sesión mediante ataques **`XSS`**. Veamos a continuación este ejemplo:

* [**XSSGAME Foogle Challenge**](http://www.xssgame.com/f/m4KKGHi2rVUN/?) **website**.

Lo primero que realizaremos es la creación de una cookie, dado que este sitio de práctica filtra automáticamente la única cookie válida que utiliza \(llamada ACSID\). 

{% hint style="danger" %}
**NOTA:** La creación de esta cookie es puramente para ejemplificar esta técnica de XSS con esta web de práctica. En web reales esto no es parte del proceso de ataque XSS.
{% endhint %}

Para crear una cookie de prueba basta con abrir la URL del sitio y luego presionando **`F12`** abrimos las **`Devs tools`** del navegador:

Una vez en las dev tools, vamos a la tab **`Application -> Storage -> Cookies -> http://xssgame.com`** y veremos lo siguiente:

![](../.gitbook/assets/image%20%2822%29.png)

Para crear nuestra cookie basta con hacer doble click en la fila vacía \(marcada en rojo en la imagen\), en la columna Name ingresamos el nombre de la cookie y en value el valor. A continuación de los valores que puse.

* **Name:** `CEH_XSS`
* **Value:** `Robo de Cookies con XSS`

Una vez creada la cookie, debemos ver algo similar a esto:

![](../.gitbook/assets/image%20%28128%29.png)

Ahora veamos como explotando la vulnerabilidad de esta simple web, podemos robar directamente esa cookie sin mucho esfuerzo. Cabe aclarar que esto es posible dado que la web no procesa el input del user en el cuadro de búsqueda y directamente lo incluye en la página de la misma forma en que viene. Dado que esta app es intencionalmente vulnerable, aparte de un ataque XSS mediante el Input disponible en UI, veremos que también podemos hacer uso de un ataque XSS mediante la URL.

### XSS mediante Inputs sin sanitizar y URLs

Veamos que simple resulta este ataque de XSS mediante el cuadro de búsqueda de la web de práctica. Basta con que prepararemos un pequeño script que se encargará de robar la cookie que hemos creado y presentar su valor en un alert box de Javascript:

El script es super simple y dado que la web procesa el contenido directamente sin hacer ningún chequeo sobre lo que se esta ingresando basta con hacer uso de una tag **`<script>`** y un **`alert`** que levante el valor de las cookies al llamar a **`document.cookie`**.

```text
<script>alert(document.cookie)</script>
```

* **&lt;script&gt;&lt;/script&gt;**: Simple etiqueta HTML que nos permite incluir código JavaScript.
* **alert:** Es un methodo que muestra una ventana de alerta con el contenido indicado. 
* **document.cookie**: **`cookie`** es una **`propiedad`** de **`document`**, que nos permite leer y asignar cookies asociadas al documento \(página web\) que estamos accediendo. [Leer más](https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie).

Veamos que sucede cuando ingresamos este código en el input de búsqueda de la web:

![](../.gitbook/assets/image%20%2881%29.png)

Y al hacer click en el botón search, nuestro ataque XSS se procesa exitosamente.

![](../.gitbook/assets/image%20%2826%29.png)

Obviamente este es un ejemplo super básico de un ataque de XSS, pero sirve para demostrar la importancia que tiene la sanitización de los valores ingresados por el usuario.

De la misma forma, podemos directamente atacar la URL de la web. Lo único a tener en cuenta en este caso es que debemos hacer URL encoding the los caracteres especiales como **`/`** , **`()`** y los **`espacios`** para luego anexar el resultado a la URL:

Si realizamos una simple búsqueda en esta web, podemos ver de que manera se construye la consulta que luego regresará o no los resultados:

![](../.gitbook/assets/image%20%2885%29.png)

Como vemos luego de una búsqueda la URL queda de la siguiente forma:

```text
/?query=USER_INPUT
```

Teniendo esto en cuenta podemos convertir nuestro script de ataque para que pueda ser ejecutado directamente via URL. Para hacer el encoding a URL podemos hacer uso de una herramienta online como **URL Decoder/Encoder**\([Link](https://meyerweb.com/eric/tools/dencoder/)\).

Ingresamos nuestro simple Script tal cual como lo usamos antes, y le damos click en el botón **`Encode`**:

![](../.gitbook/assets/image%20%28116%29.png)

 Una vez que tenemos nuestro código encodeado, podemos directamente usarlo en la URL respetando como el sistema normalmente forma la URL, para este ejemplo quedaría de la siguiente forma:

```text
?query=%3Cscript%3Ealert(document.cookie)%3C%2Fscript%3E
```

Si agregamos eso a la URL de nuestra web vulnerable y lo ejecutamos debemos obtener el mismo alert con el contenido de la cookie:

![](../.gitbook/assets/image%20%2837%29.png)

Como podemos ver el ataque XSS por URL es exitoso y si observamos bien la URL final, vemos que luego de ser procesada algunos caracteres que habíamos encodeado antes son ahora mostrados directamente. Este mismo ejemplo se puede ajustar para que en vez de mostrar la cookie en un alert, la misma sea enviada a nuestro equipo o a un sitio que controlemos y de esta manera hacer uso de esa cookie para poder impersonar una sesión de usuario válida.

La web presenta otros desafíos sobre ataques XSS que recomiendo explorar y practicar para comprender este tipo de ataques y sus variantes. 

Cabe aclarar que fuera de XSS existen numerosas formas de ataques mediante las cuales se pueden realizar ataques de Session Hijacking, no es el objetivo de esta práctica demostrar estas otras técnicas. Pero hasta acá hemos visto los conceptos y variados ataques que pueden realizarse para llevar a cabo un robo de sesión.

{% hint style="info" %}
Estas prácticas están sujetas a modificaciones y correcciones, la versión más actualizada disponible se encuentra online en [el siguiente link](https://tzero86.gitbook.io/tzero86/).
{% endhint %}

