---
description: >-
  En esta práctica veremos como usar recon-ng para realizar un footprinting de
  un objetivo.
---

# Footprinting with Recon-NG

![](<../.gitbook/assets/image (125).png>)

Al iniciar `recon-ng` nos encontramos con un framework vacío. Lo primero que debemos hacer es instalar `modulos` que habilitan diferentes tipos de funcionalidades en `recon-ng` (podemos pensarlos como extensiones o plugins).

Para ver los comandos disponibles usamos el comando `help`:

![](https://i.imgur.com/CEy5WaL.png)

## Usando Módulos

Con cualquier framework donde se utilicen módulos o extensiones es debemos conocer cuales comandos tenemos disponibles para poder buscar, instalar y eliminar los módulos cuando ya no los necesitemos.

### Buscando Módulos en el Marketplace

Para buscar que módulos disponibles existen para recon-ng, contamos con el comando `marketplace search`, con el cual recon-ng mostrara una lista de los módulos disponibles para ser instalados:

&#x20;

![](https://i.imgur.com/po2xQvD.png)

La lista de módulos disponibles es bastante extensa, y vemos que entre los detalles tenemos `path`, `version`, `status`, `updated.`&#x20;

![](https://i.imgur.com/ek70rM8.png)

Adicionalmente tenemos dos columnas llamadas `D` y `K` que nos indican si el modulo tiene Dependencias(D) o si necesita de una key(K), como es por ejemplo el caso de `shodan_ip`.

### Agregando API keys

Si el modulo que intentamos usar requiere una api key, podemos agregarla a recon-ng de la siguiente manera: `keys add shodan_api {API_KEY}`:

![](https://i.imgur.com/mXiwAuk.png)

> Las keys añadidas son almacenadas en el archivo `keys.db` en la carpeta donde se encuentra instalado recon-ng.

### Instalando Módulos

Para poder instalar los módulos, contamos con el comando `marketplace`. Para instalar un modulo, por ejemplo `shodan_ip`. Usamos el siguiente comando: `marketplace install shodan_ip`:

![](https://i.imgur.com/OVZdn4k.png)

De esta manera dejamos instalado el modulo seleccionado.

### Cargando y configurando Módulos

Es necesario cargar el modulo que se desea utilizar, en este caso: `modules load shodan_ip`. Similar a otros frameworks como `metasploit`. En recon-ng los módulos tienen diferentes opciones que debemos setear para poder ejecutarlos. Para ver las opciones requeridas (y opcionales) del modulo seleccionado usamos el siguiente comando: `options list`:

![](https://i.imgur.com/rTwi9v7.png)

Si ejecutamos el comando `info`, podemos ver los distintos tipos de opciones que se pueden setear y sus valores actuales. Adicionalmente obtenemos un detalle de que valores podemos setear para la opción `SOURCE`.

![](https://i.imgur.com/RO4Q1lN.png)

### Seteando Opciones

Para setear las opciones usamos el comando `options set {NOMBRE_OPCION}`, en este caso el modulo necesita que `SOURCE` este seteado. El source en nuestro caso es el IP del objetivo (obtenido en shodan.io):

![](https://i.imgur.com/viuIMVx.png)

### Ejecutando el Módulo

En este punto ya estamos listo para ejecutar el modulo, para eso usamos el comando `run`:

![](https://i.imgur.com/KGshirb.png)

Como podemos ver el modulo realiza un escaneo del objetivo y nos devuelve ciertos detalles sobre el mismo. Si ingresamos el comando `show ports` podemos ver la lista de puertos descubiertos durante el scan:

![](https://i.imgur.com/WYdsnOx.png)

También es posible usar otros módulos que devuelven otro tipo de información. Por ejemplo podemos instalar el modulo `whois_pocs`, configurarlo y ejecutarlo:

![](https://i.imgur.com/4j4YsH3.png)

De esta manera realizamos un scan simple utilizando `shodan` para obtener puertos abiertos y luego usando el modulo `whois_opcs` obtuvimos información de `whois` en un scan adicional.

## Workspaces en Recon-ng

Es importante tener en cuenta que recon-ng nos permite organizar nuestra información en diferentes `workspaces` o espacios de trabajo. La ventaja de esto es que podemos tener nuestra información separada por ejemplo, por objetivos o clientes para los cuales estemos haciendo reconocimiento. De esta manera es muy simple tener un espacio por ejemplo para todo lo relacionados a nuestras tareas de reconocimiento de `microsoft.com` y en otro workspace tener todo lo relacionado a `udemy.com`.

El uso de workspaces es muy simple como vemos a continuación:&#x20;

![](https://i.imgur.com/qaG8pnp.png)

* `workspaces list` nos deja ver todos los workspaces existentes.
* `workspaces create {NOMBRE_WORKSPACE}` nos permite crear uno nuevo.
* `workspaces load {NOMBRE_WORKSPACE}` nos permite cargar y marcar como activo un determinado workspace.
* `workspaces remove {NOMBRE_WORKSPACE}` nos permite borrar un workspace.
