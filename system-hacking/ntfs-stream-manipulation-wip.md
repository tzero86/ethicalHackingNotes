---
description: >-
  En esta práctica veremos en que consiste el ataque llamado NTFS Stream
  Manipulation.
---

# NTFS Stream Manipulation

## NTFS (New Technology File System)

![](<../.gitbook/assets/image (62).png>)

**NTFS** es un sistema de archivos propietario de Microsoft y fue introducido como reemplazo de sistemas de archivos anteriores como **FAT (File Allocation Table)** y **HPFS (High Performance File System)** e incorpora mejoras técnicas con respecto a estos. Entre algunas de las ventajas **NTFS** incorpora un soporte mejorado para metadatos, mejoras en la administración de espacio en disco, mejoras de performance, un mejorado sistema de seguridad y  un sistema de encriptado de archivos llamado **EFS (Encrypting File System)**.&#x20;

Desde **Windows NT 3.1** es el sistema de archivos por default en la familia de **Windows NT** como por ejemplo **Windows Server 2008**, **Windows 7** y **Windows 10** por nombrar algunos. Es soportado en sistemas operativos de escritorio y servidor. El soporte en **Linux** y **BSD** es posible mediante el **NTFS Driver (NTFS-3G)** el cual ofrece soporte para lectura y escritura. En **macOS** se ofrece soporte de lectura.

## NTFS Alternate Data Streams

![](<../.gitbook/assets/image (131).png>)

Para poder entender como funciona el ataque de `NTFS Stream Manipulation`, primero debemos conocer lo básico sobre **`ADS`**` ``(alternate data streams)`. ADS es una característica del sistema de archivos **`NTFS`**` ``(NT File System)` que permite que más de un `stream` de datos pueda estar asociado a mismo archivo. Cada archivo, tiene su contenido principal conocido como **`default stream`** y puede tener uno o más **`ADS`**.

Estos **`streams`** de datos usan un formato en particular: **`fileName:streamName:streamType`**. Por ejemplo un  **`ADS`** cuyo **`stream`** es llamado **`payload`**  y esta alojado dentro de un archivo llamado **`malicioso.txt`** se vería de esta forma: **`malicioso.txt:payload:$DATA`**. Cabe destacar que los ADS pueden existir en cualquier tipo de archivo, incluyendo ejecutables. El contenido de los ADS puede ser de cualquier tipo y por ende no es necesario que sea del mismo tipo del archivo que lo contenga. Por ejemplo un archivo de imagen **`JPG`** puede contener **`streams`** de datos del tipo video, audio, etc.

Otra característica de **`ADS`** es que su peso no es reportado como parte del peso total del archivo que lo contiene y tampoco aparecen listados en aplicaciones muy utilizadas en Windows como lo es **`Windows Explorer`**. Que un archivo contenga uno o más ADS tampoco altera el funcionamiento original del archivo y este seguirá funcionando como siempre. Por estos motivos, los archivos con ADS maliciosos son algo bastante común.

{% hint style="danger" %}
Cuando se **`copian o mueven`** archivos que contienen **ADS** a sistemas de archivos que no soportan **`Alternate Data Streams`**, el usuario recibe una advertencia de que los **`streams`** se perderán. Sin embargo, esta advertencia **`no suele ser emitida`** cuando los archivos se adjuntan por **`correo`** o son subidos a una **`web`**.  En esos casos toda información de **ADS** que estaba contenida en el archivo se **perderá.**
{% endhint %}



## NTFS Stream Manipulation

La idea principal detrás de la manipulación de `NTFS Data Streams` es  la de ocultar información dentro de otro archivo, normalmente con fines maliciosos. Como por ejemplo permite a un atacante ocultar información sensible recolectada de un sistema, dentro de los mismos archivos del usuario sin que este note cambio alguno en sus archivos. El atacante luego puede simplemente extraer esos archivos aparentemente normales del sistema objetivo llevándose consigo toda la información relevante almacenada en los **ADS**.

### Veamos el uso básico de ADS usando la consola de comandos de Windows

En esta parte de la práctica veremos como podemos crear un archivo de texto y posteriormente agregarle **Alternate Data Streams** con información únicamente visible para **NTFS**.

Para comenzar abrimos la consola de **Windows (cmd)** y creamos nuestro primer archivo, en este caso un archivo de texto. Para lo cual usamos el comando **`echo`** , el contenido de nuestro archivo y finalmente el redireccionamos todo a nuestro archivo usando el carácter **`>`** seguido por el **`nombre de archivo y extensión`** que queremos crear. Veamos esto en la consola:\


![](<../.gitbook/assets/image (57).png>)

{% hint style="success" %}
**`echo`** _{contenido}_ **`>`** _{nombre de archivo}_**.**_{extensión}_
{% endhint %}

Como podemos observar en la imagen, el archivo es creado correctamente y tiene un peso de **`36 bytes`**. Podemos usar el comando **`type`** para ver el contenido del mismo:

<div align="center"><img src="../.gitbook/assets/image (221).png" alt=""></div>

Ahora, usando nuevamente el comando **`echo`** como hicimos en el paso 1, agregaremos el primer **`Alternate Data Stream`** a nuestro archivo. La única diferencia es que en este caso debemos indicar el nombre del **`stream`**. Veamos esto en la consola:

![](<../.gitbook/assets/image (151).png>)

Como podemos observar el **`stream`** parece haberse agregado sin error y nuestro archivo reporta el mismo tamaño de **`36 bytes`**. También vemos que en ningún lado es reportado que existe un **`ADS`** en el archivo. Esto es precisamente lo que convierte a los ADS en una buena opción para esconder información sin que el usuario lo note.

Agreguemos un segundo ADS y veamos de que manera podemos obtener información de los mismos en la consola:

![](<../.gitbook/assets/image (144).png>)

Para ver el contenido de **`data streams`** adicionales que pueda contener un archivo, hacemos uso del comando **`dir /r`** el cual lista los archivos incluyendo sus **`ADS`**. Como podemos ver en la imagen anterior el tamaño reportado del archivo sigue siendo el mismo **`36 bytes`** a pesar de que contiene dos **`ADS`** de **`23 bytes`** cada uno. Si prestamos atención al espacio en disco disponible luego de crear el archivo y luego de cada creación de los **`ADS`**, podemos observar que **NTFS** si lleva el registro correcto de cuanto espacio esta siendo utilizado en realidad.

Si abrimos el archivo con el bloc de notas vemos que únicamente se presentan los datos del **`default stream`**:

![](<../.gitbook/assets/image (190).png>)

Si queremos editar o ver el contenido de cada ADS, debemos invocarlo directamente de la siguiente forma:

![](<../.gitbook/assets/image (109).png>)

Como dijimos antes el contenido del ADS puede ser de cualquier formato al igual que el archivo que los contenga. Cabe destacar que **Alternate Data Streams** se pueden agregar a **`directorios`** de la misma forma que co&#x6E;**`archivos`**. Veamos esto rápidamente en la consola:

![](<../.gitbook/assets/image (39).png>)

Como podemos observar en la imagen la carpeta **`DIRECTORIO`** contiene un **`ADS`** llamado **`ADS1`**.

### ADS con PowerShell

PowerShell incorpora ciertos comandos (**cmdlets**) que facilitan trabajar con ADS. Veamos como podemos listar los ADS presentes en el archivo que hemos creado en esta práctica:

&#x20;

![](<../.gitbook/assets/image (191).png>)

En el caso de **PowerShell**, el **`default stream`** se conoce como **`unnamed stream`**, o **`stream sin nombre`** ya que aparece listado simplemente como **`:$DATA`**.&#x20;

Para ver el contenido por ejemplo del **`ADS2`**, podemos hacer uso del comando **`Get-Content`** de la siguiente manera:

![](<../.gitbook/assets/image (28).png>)

Si queremos agregar contenido al ADS podemos hacerlo con el comando **`Set-Content`**. Veamos como podemos agregar dentro de un nuevo archivo de texto un **ADS** llamado **`payload`** que contenga un **`archivo ejecutable`** como por ejemplo el clásico **`Microsoft Paint`**. Para eso hacemos uso del siguiente comando:

{% hint style="info" %}
También podemos hacer uso del **`cmdlet`** **`Add-Content`**.
{% endhint %}

```
Set-Content -path .\NTFS_ADS_DEMO.txt -value $(Get-Content $(Get-Command mspaint.exe).Path -readcount 0 -encoding byte) -encoding byte -stream payload.exe
```

En este comando explícitamente indicamos el **`encoding para los bytes`**, el **`readcount en 0`** (para leer el archivo en una sola operación y el comando **`Get-Command`** para obtener rápidamente la ruta de **`mspaint.exe`** sin tener que especificarla manualmente. Luego simplemente indicamos el nombre del **`stream`** que queremos agregar usando el switch **`-stream {nombreDelStream}`**.

![](<../.gitbook/assets/image (21).png>)

Como podemos ver en la imagen, nuestro archivo de texto contiene un nuevo **ADS**. Veamos como podemos **`ejecutar`** ese **ADS** que sabemos contiene un archivo ejecutable.  Para lograr esto podemos usar **`Windows Management Instrumentation`** para crear un proceso que ejecute nuestro **ADS**.&#x20;

{% hint style="danger" %}
Este método de ejecución de archivos usando **`wmic`** parece haber sido reparado en Windows en la versión que tengo en las VMs por ende al intentar ejecutarlo tanto en **`CMD`** como en **`Powershell`** obtengo un error y el programa no se ejecuta. En versiones vulnerables de Windows, el programa sería ejecutado directamente y veríamos en nuestro caso a **`mspaint.exe`** en ejecución. Voy a investigar un poco más sobre otras formas de ejecutar ADS en versiones actualizadas de Windows, de encontrar alguna actualizaré este artículo.&#x20;
{% endhint %}

**Actualización:** Luego de varios intentos logré agregar el contenido de la calculadora de Windows **`calc.exe`** a nuestro archivo de práctica y ejecutarlo correctamente usando **`wmic`**:

![](<../.gitbook/assets/image (178).png>)

Como vemos al ejecutarse el comando correctamente genera un nuevo proceso el cual retorna un **`ProcessId = 4344`** y un **`ReturnValue = 0`** indicando que el proceso se completó exitosamente. No comprendo porque no funcionó el intento inicial, quizás sea el ejecutable de **`mspaint.exe`** o algún error que estoy cometiendo y no logro darme cuenta. Si te das cuenta del problema, házmelo saber con un tweet a mi cuenta [@tzero86](https://twitter.com/Tzero86).

Si queremos **borrar** un **ADS** en particular podemos hacer uso del **cmdlet** **`Remove-Item`** de la siguiente manera. Por ejemplo para eliminar nuestro **ADS** llamado **`payload`**:

```
Remove-Item -Path .\NTFS_ADS_DEMO.txt -stream payload
```

![](<../.gitbook/assets/image (201).png>)

Como podemos ver nuestro archivo ahora contiene solo dos **ADS** adicionales. Hasta aquí vimos y practicamos un poco **NTFS Stream Manipulation**.

{% hint style="info" %}
Estas prácticas están sujetas a modificaciones y correcciones, la versión más actualizada disponible se encuentra online en [el siguiente link](https://tzero86.gitbook.io/tzero86/).
{% endhint %}
