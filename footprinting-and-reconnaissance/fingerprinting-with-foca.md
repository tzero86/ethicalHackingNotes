---
description: En esta práctica veremos como hacer fingerprinting de archivos usando FOCA.
---

# Fingerprinting with FOCA

![](<../.gitbook/assets/image (87).png>)

En esta oportunidad vamos a hacer un scan con la herramienta llamada **FOCA** **(Fingerprinting Organizations with Collected Archives).**

Para esta práctica voy a usar `www.globant.com` como objetivo inicial para conocer un poco sobre `FOCA` y su uso. Luego usaremos algún otro objetivo para ver casos donde haya `metadata` expuesta.

## Uso básico de FOCA

Como primer paso configuramos el proyecto y el `dominio` objetivo:

![](https://i.imgur.com/7WDBhIv.png)

Una vez listo, seleccionamos las `extensiones de archivo` que queremos que sean tomadas en cuenta por `FOCA` al realizar el scan:

![](https://i.imgur.com/ApPFnjP.png)

Una vez listo, hacemos `click` en `Search All` para iniciar el `scan`. Una vez que `FOCA` comienza a detectar archivos los vemos listados debajo de la selección de extensiones de archivos:

![](https://i.imgur.com/c5KFCUh.png)

Una vez tenemos resultados disponibles, podemos hacer `click derecho` sobre cualquiera de los archivos listados y darle `click` en `Download` para descargarlo y ver que información podemos obtener del mismo:

![](https://i.imgur.com/vrwKjsg.png)

Una vez descargado el documento aparece listado en el `tree view` y podemos observar algunos detalles del mismo.

![](https://i.imgur.com/IMPrHDU.png)

Para ver los detalles de la `metadata` del archivo necesitamos volver a la lista de archivos y luego darle `click derecho` al archivo deseado y `click` en `Extract All metadata` y luego `Analyze all metadata`.

![](https://i.imgur.com/0aBI0tA.png)

En este caso no obtenemos información importante dado que estos documentos ya fueron sanitizados antes de ser publicados. Pero en caso de obtener algún detalle importante de la `metadata` de los mismos, `FOCA` listara los diferentes tipos de información en el `tree view` para que podamos revisarlos:

![](https://i.imgur.com/HbxOuPA.png)

## Análisis de Metadata Expuesta con FOCA

Si vemos un ejemplo de un archivo que si expone ciertos datos en su **`metadata`** veremos como esta información es presentada en **`FOCA`**:

![](https://i.imgur.com/07H0uXR.png)

Vemos que entre los resultados obtenemos:

* La dirección IP del servidor
* El Software usado para crear el Archivo
* El User usado al crear el archivo

Cada archivo escaneado puede exponer distintas piezas de información que nos dejan obtener una imagen más completa del objetivo durante la etapa de reconocimiento.

En este otro ejemplo vemos que un **PDF** del campus de la UTN revela diferentes detalles al analizarlo:

![](https://i.imgur.com/gQILptw.png)

En este caso obtenemos algunas `carpetas`(Folders), algunos `emails` y la `versión del software` usado en la creación del documento.

Algunos documentos revelan mucha más información:

![](https://i.imgur.com/EIj4Wi3.png)

En este caso podemos ver que la **`metadata`** extraída de ciertos documentos, exponen cuentas de usuarios, correos, impresoras, carpetas, incluso otros servidores. Claramente sanitizar los documentos antes de compartirlos es clave para prevenir que agentes externos puedan obtener detalles potencialmente sensibles con herramientas como **`FOCA`**.
