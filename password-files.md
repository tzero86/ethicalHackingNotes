---
description: >-
  En esta práctica veremos como están formados los archivos passwd y shadow en
  Linux y SAM en Windows y como se maneja la autenticación en estos SO.
---

# Password Files & Authentication

![imagen por pipedrive.com](.gitbook/assets/image%20%2813%29.png)

## Entendiendo los archivos `passwd` y `shadow`

{% hint style="info" %}
**Objetivo de la Practica del curso de CEH de la UTN:**

Dentro de los entornos de los distintos SO, encontraremos archivos que tienen una gran importancia respecto a la autenticación, son el archivo `passwd` y el `shadow`, de las distribuciones de LINUX o el archivo `SAM` de Windows. El ejercicio de aquí es analizar y detallar esos archivos y describirlos lo mejor posible.
{% endhint %}

Anteriormente en Linux las credenciales de usuario solían ser almacenadas directamente en el archivo`passwd`. No obstante por limitaciones y cuestiones de seguridad actualmente solo almacena información de las cuentas de usuarios, mientras que los hashes de los passwords se almacenan en el archivo `shadow`.

El archivo se encuentra comúnmente en la siguiente ubicación `/etc/passwd` y su contenido es de texto plano. Generalmente el acceso de solo lectura es permitido dado que muchas utilidades utilizan su contenido para relacionar `IDs` a las correspondientes cuentas de usuario. Dentro del mismo podemos encontrar una lista de `system accounts`. De cada cuenta se exponen las siguientes piezas de información: `Username` , `Password`, `UID`, `GID`, `UserID Info`, `Home Dir`, `Command/Shell`. Veamos que significa cada uno y como es la estructura general de este archivo y sus datos.

### Estructura del archivo `/etc/passwd`

Ya mencionamos que el archivo `passwd` almacena información en texto plano. Dicha información es almacenada de a una entrada por línea. Cada línea delimita sus campos usando los dos puntos `:`. 

Veamos un diagrama de la estructura de cada línea.

![](.gitbook/assets/image%20%2860%29.png)

Veamos una descripción sobre que función cumple cada una de las partes:

1. **Username:** Comúnmente usado para el proceso de login, de longitud entre 1 y 32 caracteres. 
2. **Password:** Un carácter `x`  que indica que el password hash esta almacenado en el archivo `/etc/shadow`. 
3. **UID \(User ID\):** A cada user se le asigna un `ID`, siendo `0` reservado para el `root`, los de `1 a 99` reservados para cuentas predefinidas, los de `100 a 999` reservados para cuentas administrativas y de sistema. Finalmente los IDs superiores a `1000` son asignados para los usuarios. 
4. **GID \(Group ID\):** El grupo primario al que pertenece el user, almacenado en `/etc/groups`. 
5. **UserID Info:** Permite almacenar información adicional del usuario, como ser el `nombre del servicio` \(service accounts\) o detalles del tipo `Nombre completo del user`. 
6. **User Home Directory:** La ruta absoluta la carpeta home del user en cuestión. 
7. **Comando o Shell:** Indica el `shell` o `comando`. Comúnmente es un `shell` pero no es siempre el caso.

{% hint style="warning" %}
Permisos: Este archivo suele ser de `solo lectura` para los usuarios y propiedad de `root`.
{% endhint %}

### Leyendo el Archivo `/etc/passwd`

Veamos que contenido obtenemos al ejecutar el comando en alguna de nuestras máquinas virtuales.

{% hint style="success" %}
cat /etc/passwd
{% endhint %}

![](.gitbook/assets/image%20%2816%29.png)

vemos que los resultados incluyen una larga lista de cuentas de administración, servicios y finalmente usuarios.

### Entendiendo el archivo `/etc/shadow`

Ya mencionamos que actualmente las credenciales en Linux residen en gran parte en el archivo `/etc/passwd` pero también mencionamos que el  `password hash`  de cada usuario se almacena en el archivo llamado `/etc/shadow`. Veamos en detalle como es la estructura de este otro archivo.

![](.gitbook/assets/image%20%2863%29.png)

De igual forma que en el archivo `/etc/passwd` la data dentro de `/etc/shadow` se almacena también línea por línea. Incluso cada una de las líneas de este archivo se corresponde 1 a 1 con las del archivo `password`.

{% hint style="info" %}
Incluso existen herramientas como `unshadow` \(parte de JohnTheRipper\) que nos permite recombinar  los archivos`passwd` y `shadow Para luego crackearlos rápidamente con` John`.`
{% endhint %}

1. **Username:**  El Nombre del usuario. 
2. **Password Encriptado:** El password encriptado, usualmente sigue un patrón de este estilo `$tipo$Sal$Hash`. 
   1. Donde el `tipo` es el algoritmo de hash que fue usado para encriptar el password.
      1. **$6$**: Indica el uso de SHA-512.
      2. **$5$**: Indica el uso de SHA-256.
      3. **$2a$**: Indica el uso de Blowfish.
      4. **$1$:** Indica el uso de MD5 Hash.
      5. **$2y$:** Indica el uso de Eksblowfish.
   2. **La `Sal` \(`salt` en ingles\):** es un valor que se utiliza para garantizar la aleatoriedad del hashing, de manera que el hash resultante siempre sea distinto.
   3. **Finalmente el `Hash value`:** El resultado de hash generado para el encriptado de nuestro password. 
3. **Ultimo cambio de password:** Representa una fecha en días, contando desde el primero de enero de 1970 hasta el día de hoy.  
4. **Edad mínima del password:** Normalmente se setea en cero indicando que el no hay un mínimo establecido para la caducidad del password. Si contiene otro valor, el mismo representa la cantidad de días que deben transcurrir para cambiar el password. 
5. **Edad máxima del password:**  Número de días antes de que el password expire. 
6. **Período de Advertencia:** La cantidad de días antes de que el password expire. Por ejemplo un valor de 6 le recordará al usuario cambiar el password 6 días antes de que el password caduque. 
7. **Período de Inactividad:** El número de días luego de que el password haya vencido donde la cuenta de usuario quedará desactivada. Normalmente esta en cero. 
8. **Fecha de expiración**: La fecha donde la cuenta de usuario fue efectivamente deshabilitada. 
9. **Sin Uso \(Reservados para futuro uso\)**: Actualmente es ignorado, se guarda para futuros posibles usos.

Hasta acá vimos como esta compuestos los archivos `/etc/passwd` y `/etc/shadow` y como se organiza y encripta la información dentro de ellos.

## Entendiendo SAM \(Security Account Manager\)

En Windows las contraseñas son almacenadas en el archivo SAM. Este archivo es en realidad una base de datos, donde Windows almacena las contraseñas en formato de hashes. Para generar estos hashes Windows ha utilizado distintos mecanismos a lo largo de las versiones. En particular nos centraremos en las siguientes: `LM, NT-Hash(NTLM, NTLMv1 y NTLMv2)`.

### LM Hash \(LanMan Hash\)

LM Hash es la forma antigua en la que Windows almacenaba las contraseñas y se remonta a los años '80. Las principal desventaja de este hash es que trabajaba con un set de caracteres limitados \(14 caracteres\), lo cual lo convertía en uno muy fácil de crackear. Internamente los hashes se generan mediante un mecanismo muy débil en general y la longitud del set de caracteres no es su única desventaja.

![Imagen por Cornell University](.gitbook/assets/image%20%28130%29.png)

{% hint style="info" %}
**Wikipedia:** **Data Encryption Standard** \(**DES**\) es un algoritmo de cifrado, es decir, un método para [cifrar](https://es.wikipedia.org/wiki/Criptograf%C3%ADa) información. DES fue sometido a un intenso análisis académico y motivó el concepto moderno del [cifrado por bloques](https://es.wikipedia.org/wiki/Cifrado_por_bloques) y su [criptoanálisis](https://es.wikipedia.org/wiki/Criptoan%C3%A1lisis). Hoy en día, DES se considera inseguro para muchas aplicaciones. Esto se debe principalmente a que el tamaño de clave de 56 bits es corto; las claves de DES se han roto en menos de 24 horas. . Se cree que el algoritmo es seguro en la práctica en su variante de [Triple DES](https://es.wikipedia.org/wiki/Triple_DES), aunque existan ataques teóricos. Desde hace algunos años, el algoritmo ha sido sustituido por el nuevo [AES](https://es.wikipedia.org/wiki/Advanced_Encryption_Standard) \(Advanced Encryption Standard\).
{% endhint %}

**Veamos rápidamente como opera el algoritmo LM:**

1. **Conversión:** Se convierten todas las letras minúsculas de la contraseña a letras mayúsculas. 
2. **Modificación:** Se agregan al password caracteres nulos \(NULL\) hasta completar los 14 caracteres. 
3. **División:** Se separa el password en dos bloques, cada uno de 7 caracteres. 
4. **Generación de KEYs:** Se generan 2 claves DES para cada bloque. 
5. **Cifrado**: Se cifra cada bloque con DES y un texto fijo con el valor `KGS!@#$%`. 
6. **Hash Generado:** El hash se genera en base a la concatenación de ambos bloques encriptados con DES.

Considerando la facilidad con la que se puede crackear, LM Hash fue desactivado por defecto con la salida de Windows Vista y Windows Server 2008. No obstante aún se mantiene una retro compatibilidad para sistemas legacy y puede ser activado manualmente mediante una Group Policy \(GPO\).

Podemos ver este hash en acción usando una herramienta como la que se encuentra online en [este link](https://tobtu.com/lmntlm.php).

![](.gitbook/assets/image%20%28142%29.png)

Como podemos observar los Hashes generados para cada contraseña en formato LM resultan **iguales luego de la conversión**, esto se debe al primer paso que lleva adelante el algoritmo, la conversión de minúsculas a mayúsculas. Si observamos los hashes resultantes en formato NTLM, vemos que son diferentes para cada contraseña.

Veamos que tan sencillo es crackear ese hash usando una herramienta como `hashcat`. 

{% hint style="success" %}
hashcat -m 3000 -a 3 hash
{% endhint %}

![](.gitbook/assets/image%20%282%29.png)

Dentro de la VM demoró 10min aproximadamente en crackear el password:

![](.gitbook/assets/image%20%28103%29.png)

Como vemos es relativamente simple y rápido el proceso de crackeo de los hashes LM.

### NT Hash \(NTLM, NTLMv1, NTLMv2\)

Actualmente en Windows se utiliza un mecanismo de hash conocido como NT Hash o NTLM Hash. Existen varias versiones de este hash que han ido mejorando la seguridad del mismo. Veamos rápidamente las características principales de cada versión.

#### NTLM Protocol

El protocolo NTLM es un sistema del tipo `Challenge-Response` el cual utiliza el intercambio de 3 mensajes para autenticar al cliente y un cuarto mensaje opcional para la integridad del intercambio.  El hash tiene una longitud de 128 bits y funciona tanto para cuentas locales como para cuentas de dominio de Active Directory. 

![Imagen por IONOS](.gitbook/assets/image%20%28101%29.png)

#### NT Hash - NTLMv1

El algoritmo para este protocolo es bastante simple para la generación del hash, y hace uso de ambos tipos de hash: `LM Hash y NT Hash`. Esta versión 1 de NTLM ya esta deprecada siendo actualmente mantenida para retro compatibilidad con sistemas viejos.



![Imagen por asecuritysite.com](.gitbook/assets/image%20%28122%29.png)

El algoritmo para esta versión de NTLM es bastante simple de comprender a alto nivel:

1. **Conversión:** Se convierte la contraseña a Unicode \(UTF-16-LE\). 
2. **Modificación:** Para cada carácter agrega un 0 \(Zero byte\) 
3. **Hashing:** Finalmente aplica el algoritmo MD4 al resultado del proceso anterior.

El algoritmo en concreto se puede consultar en Wikipedia, pero para referencia se ve así:

{% hint style="info" %}
**Wikipedia**: El algoritmo en concreto se ve de la siguiente forma. [\[LINK\]](https://en.wikipedia.org/wiki/NT_LAN_Manager#NTLMv1)

```text
C = 8-byte server challenge, random
K1 | K2 | K3 = NTLM-Hash | 5-bytes-0
response = DES(K1,C) | DES(K2,C) | DES(K3,C)
```
{% endhint %}

Veamos como podemos crackear este nuevo hash usando `hashcat` y un hash de ejemplo.

{% hint style="info" %}
**Hash de ejemplo \(De la página de hashes de hashcat\)**: [\[LINK\]](https://hashcat.net/wiki/doku.php?id=example_hashes)

u4-netntlm::kNS:338d08f8e26de93300000000000000000000000000000000:9526fb8c23a90751cdd619b6cea564742e1e4bf33006ba41:cb8086049ec4736c
{% endhint %}

{% hint style="success" %}
hashcat -m 5500 -a 3 ntlm\_v1\_hash
{% endhint %}

![](.gitbook/assets/image%20%2880%29.png)

Esta vez le tomó a hashcat unos 5 minutos aproximadamente para crackear el hash.

![](.gitbook/assets/image%20%2883%29.png)

Como podemos notar el proceso de crackeo de la versión NTLMv1 es también relativamente simple y rápido.

### NT Hash - NTLMv2

La versión 2 de NTLM continúa usando el protocolo NTLM de formato `challenge-response` que vimos antes, pero incorpora mejoras de seguridad y criptografía para hacerlo más seguro y reemplazar a NTLMv1. También se incorpora el uso de HMAC-MD5 como algoritmo de Hashing y se incorpora el nombre de dominio como variable para el proceso de autenticación.

![Imagen por Guang Ying Yuan, Advisory IT specialist at IBM.](.gitbook/assets/image%20%28105%29.png)

1. **Negociación:** Se genera el `challenge de 8 bytes`  y es emitido por el server. 
2. **Respuesta:** Se generan `2 bloques de 16 bytes con hashes HMAC-MD5` a modo de respuesta al `challenge`. Estos bloques de respuesta incluyen también un `challenge` generado en el lado del cliente, el password hash \(HVAC-MD5\) y puede incluirse otra información de identificación. 
   1. **Respuesta Corta \(shorter-response\)**: En este bloque de respuesta se incluyen los 8 bytes del `challenge` del lado de cliente y los `16 bytes de del bloque`. Esto genera `un bloque de respuesta de 24 bytes` consistente con el formato usado en `NTLMv1`. 
   2. **Segunda Respuesta:** El segundo bloque de respuesta usa un `challenge` de longitud variable, que incluye entre otras cosas: el horario actual en formato `NT Time`, un valor aleatorio de `8 bytes`, el `nombre de dominio` e información estándar adicional. Considerando que esta respuesta debe incluir el `challenge` del lado del cliente, su longitud variara en cada caso.

El hash resultante se puede apreciar en el siguiente ejemplo obtenido en la página de hashes de hashcat:

{% hint style="info" %}
NTLMv2 Hash: [\[LINK\]](https://hashcat.net/wiki/doku.php?id=example_hashes)

admin::N46iSNekpT:08ca45b7d7ea58ee:88dcbe4446168966a153a0064958dac6:5c7830315c7830310000000000000b45c67103d07d7b95acd12ffa11230e0000000052920b85f78d013c31cdb3b92f5d765c783030
{% endhint %}

El algoritmo se puede ver en detalle en el artículo de Wikipedia, el cual proporciona información adicional.

{% hint style="info" %}
**Wikipedia**: El algoritmo en concreto se ve de la siguiente forma. [\[LINK\]](https://en.wikipedia.org/wiki/NT_LAN_Manager#NTLMv2)

```text
SC = 8-byte server challenge, random
CC = 8-byte client challenge, random
CC* = (X, time, CC2, domain name)
v2-Hash = HMAC-MD5(NT-Hash, user name, domain name)
LMv2 = HMAC-MD5(v2-Hash, SC, CC)
NTv2 = HMAC-MD5(v2-Hash, SC, CC*)
response = LMv2 | CC | NTv2 | CC*
```
{% endhint %}

#### Extrayendo hashes de SAM con Mimikatz

Hasta aquí vimos los diferentes tipos de manejos de passwords en Windows. Para no terminar la práctica sin aprender alguna herramienta que nos permita interactuar con estos hashes almacenados en SAM, veamos como podemos extraer estos hashes de un `host Windows`  usando `mimikatz`  y posteriormente crackearlos usando `hashcat`. No obstante no ahondaremos en detalle en como utilizar mimikatz en sí, sino que lo utilizaremos rápidamente para hacer un dump de los hashes. _Quizás cubra el uso de mimikatz en otro escrito a futuro, dado que es una herramienta sumamente interesante._ 

{% hint style="warning" %}
**Descarga mimikatz desde su repositorio:** [\[LINK\]](https://github.com/gentilkiwi/mimikatz/releases)

Deberás **desactivar Windows Defender**, dado que mimikatz es detectado como archivo malicioso.
{% endhint %}

El primer paso es obtener una copia del archivo SAM, para lo cual usaremos la terminal de Powershell \(como Admin\) y copiaremos los archivos `SAM` y `SYSTEM` a nuestra carpeta de trabajo.

![](.gitbook/assets/image%20%2873%29.png)

Deberíamos tener algo similar a esto luego de realizar ese proceso:

![](.gitbook/assets/image%20%2833%29.png)

Ahora necesitamos abrir una consola, idealmente como administrador, y navegar hasta la carpeta donde tenemos nuestro `export y mimikatz`.

Lo siguiente es ejecutar mimikatz desde la terminal de Windows \(PowerShell\):

![](.gitbook/assets/image%20%28144%29.png)

Ahora debemos indicarle a mimikatz que queremos extraer los hashes de archivo SAM y para ello debemos hacer uso del siguiente comando:

```text
lsadump::sam /sam:C:\Users\Administrator\Desktop\mimikatz\x64\sam /system:C:\Users\Administrator\Desktop\mimikatz\x64\system
```

Como vemos mimikatz extrae los hashes presentes en la base de datos de SAM:

![](.gitbook/assets/image%20%2871%29.png)

Por último usaremos nuevamente a nuestro amigo fiel `hashcat` para tratar de crackear el password de la cuenta `administrador`. Como podemos ver el hash esta en formato NTLM.

{% hint style="success" %}
hashcat -m 1000 -a 3 hash
{% endhint %}

Hashcat demora nuevamente unos minutos en crackear el hash.

![](.gitbook/assets/image%20%2841%29.png)

## Fin de la práctica

Hasta aquí hemos visto como funciona el manejo de passwords tanto en Linux como en Windows y como podemos crackear distintos tipos de hashes usando `mimikatz`  y `hashcat`. Cabe mencionar que también existe otro protocolo de autenticación que no hemos visto en esta práctica llamado `Kerberos` que es comúnmente usado para autenticación contra dominios como `Active Directory`. 

{% hint style="warning" %}
**Kerberos** no forma parte del alcance de esta práctica y lo veremos en detalle más adelante.
{% endhint %}

Ha sido una práctica extensa y que me ha permitido estudiar bastante sobre los temas cubiertos, principalmente sobre el funcionamiento de autenticación en Windows que no lo tenía muy presente.

{% hint style="info" %}
Estas prácticas están sujetas a modificaciones y correcciones, la versión más actualizada disponible se encuentra online en [el siguiente link](https://tzero86.gitbook.io/tzero86/).
{% endhint %}

