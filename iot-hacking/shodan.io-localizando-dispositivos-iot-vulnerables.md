---
description: >-
  En esta práctica veremos como podemos localizar diversos dispositivos IoT
  vulnerables, usando shodan.io y Python.
---

# Shodan.io: Localizando Dispositivos IoT Vulnerables

![](<../.gitbook/assets/image (150).png>)

**IoT** o Internet de las cosas (Internet of Things) es básicamente la interconexión de distintos tipos de dispositivos en una red que compartan. Esta red puede ser incluso internet mismo, en otros casos redes privadas. Estos dispositivos entablan comunicación con otros dispositivos de la misma red y a su vez permiten monitoreo e control de sus funcionalidades.

Hoy en día este tipo de dispositivos es muy variado, desde cámaras de seguridad, sensores ambientales, luces, sistemas de sonido, etc. Incluso controladores industriales que mantienen operaciones críticas como ser controles para turbinas de parques de energía eólica, centrales de telecomunicaciones, controles críticos de vuelo. O sistemas mucho más mundanos como ser controles de sistemas de semáforos o heladeras "smart".

Todo este rango de dispositivos funciona por lo general, con cierto nivel de autonomía. Pero comúnmente disponen de algún panel de administración desde el cual se puede monitorear su funcionamiento e incluso modificarlo completamente.&#x20;

Considerando que estos dispositivos en muchos casos están expuestos de cara a internet, normalmente pueden ser escaneados en busca de software vulnerable que permita el acceso con privilegios elevados, servicios expuestos que usen contraseñas comunes o de fábrica.  Una de las herramientas que podemos usar para localizar este tipo de dispositivos es **`Shodan.io`**, una suerte de buscador como DuckDuckGo, Google, etc. Pero a diferencia de esos, **`Shodan`** nos permite ubicar dispositivos en internet.

**Shodan** tiene multiples servicios que se ejecutan 24/7 y que van escaneando internet en busca de dispositivos para escanearlos, detectar puertos abiertos y distintos tipos de información y posibles vulnerabilidades. Al consumir la data generada en este proceso por los crawlers de **Shodan**, podemos obtener información muy valiosa sobre los objetivos que tengamos intención de investigar.

A continuación veremos como podemos utilizar **Shodan** para localizar diversos dispositivos IoT vulnerables expuestos en internet y el tipo de información que podemos obtener.

## Usando Shodan.io y su Search Query Syntax

![](<../.gitbook/assets/image (132).png>)

Para comenzar con **Shodan** podemos hacer uso de su interfaz para navegar los resultados por categorías, tipos de dispositivos, países, etc. Esta interfaz web proporciona incluso una vista de mapa donde podemos ubicar dispositivos geográficamente. También disponemos de un programa de línea de comandos Shodan CLI para distintos sistemas operativos, y  paquetes de desarrollo para Python, NodeJS, etc.

### Shodan Search Query Syntax <a href="#shodan-filters" id="shodan-filters"></a>

En **Shodan** también disponemos de un lenguaje de consultas, llamado **Search Query Syntax**. Esta sintaxis nos permite hacer uso de variados filtros para obtener resultados más específicos a nuestras necesidades.  La información devuelta contiene diversas propiedades sobre el dispositivo y sus servicios expuestos, como puede ser el país donde opera, puertos abiertos a servicios, detalle sobre la organización a la que pertenece, etc.

Algunos de los filtros útiles que podemos usar con Shodan:

* **geo**: Nos permite especificar coordenadas geográficas.
* **country**: Nos permite buscar dispositivos en un determinado país.
* **city**: Nos permite buscar dispositivos por ciudad.
* **hostname**: Para ubicar hosts por nombre (google.com)
* **os**: Buscar por Sistema operativo.
* **port**: buscar por puertos.
* **net**: Para buscar por IP o CIDR

Y algunos ejemplos de queries usando alguno de estos:\
\
Buscar servidores apache en un determinado país:

```python
apache country:"AR"
```

Buscar servidores Nginx en una ciudad en particular:

```python
nginx city:"Buenos Aires"
```

Una combinación de varios filtros:

```python
apache city:"Los Angeles" port:"80" product:"Apache/2.4.7"
```

De esta manera podemos generar nuestras propias consultas para obtener resultados más focalizados sobre nuestros objetivos. Cabe destacar que ese lenguaje de consultas soporta muchos otros filtros. Los mismos podemos aprenderlos de las mismas búsquedas en la web de Shodan y existen diversos repositorios con queries útiles que podemos probar. En lo que respecta a esta práctica haremos uso de algunas queries en particular para ubicar determinados dispositivos de nuestro interés.

{% hint style="danger" %}
Cabe aclarar que para obtener resultados de Shodan mediante queries, **debes obtener tu API key en Shodan.io**. Esta clave API será requerida para poder hacer consultas en Shodan y ver distintas páginas de resultados. Shodan ofrece una clave gratuita, pero obviamente provee funcionalidades limitadas, como el número de consultas que podemos realizar. Para este práctico usaré mi clave personal.
{% endhint %}

## Localizando dispositivos con Shodan.io via Python3

![imagen por Nullbyte](<../.gitbook/assets/image (99).png>)

Para este práctico voy a utilizar la **librería oficial de Python para Shodan**, que puedes descargar del siguiente repositorio. Veamos como luce este banner al realizar una consulta con un básico programa en Python, una suerte de Hola mundo con Shodan.&#x20;

{% hint style="success" %}
**Repositorio:** [https://github.com/achillean/shodan-python](https://github.com/achillean/shodan-python)
{% endhint %}

En realidad contiene un poco más que lo necesario para un Hola mundo, pero es importante poder visualizar la información devuelta por Shodan en un formato más ameno al formato crudo en el que es inicialmente devuelta la respuesta:

{% hint style="warning" %}
Nota: Para usar este script basta con copiarlo y asegurarse de ejecutar:**`pip3 install IPy, shodan, json`**
{% endhint %}

```python
"""
    UTN-CEH Práctica de banner grabbing con Shodan API by Tzero86
"""

__author__ = "Tzero86"
__contact__ = "twitter: @tzero86"
__date__ = "2021/03/15"


import socket
import json
from shodan import Shodan
from IPy import IP

api_connection = Shodan("TU_API_KEY_VA_AQUI")
host_target = 'turismo.buenosaires.gob.ar'


# Si se ingresa un dominio lo convierte en una IP
def check_ip(target):
    try:
        IP(target)
        return target
    except ValueError:
        return socket.gethostbyname(target)


# formateamos la respuesta de shodan, para que se aprecie mejor la estructura y los datos devueltos
def format_response(target):
    formatted_data = json.dumps(target, indent=1)
    print(formatted_data)


target_info = api_connection.host(check_ip(host_target))
format_response(target_info)

```

Si observamos la respuesta, vemos el nivel de detalle que podemos obtener. Para este ejemplo puse como objetivo la web de Turismo Buenos Aires e intencionalmente removí algunos datos a fin de que sea mas simple visualizar la información base incluida en el banner:

```javascript
{
  "region_code": null,
  "tags": [],
  "ip": 3356514664,
  "area_code": null,
  "domains": [
    "buenosaires.gov.ar"
  ],
  "hostnames": [
    "buenosaires.gov.ar"
  ],
  "postal_code": null,
  "dma_code": null,
  "country_code": "AR",
  "org": "Agencia de Sistemas de Informacion, Gobierno de la Ciudad Autónoma de Buenos Aires",
  "data": [],
  "asn": "AS52318",
  "city": "Buenos Aires",
  "latitude": -34.61315,
  "isp": "Agencia de Sistemas de Informacion, Gobierno de la Ciudad Autónoma de Buenos Aires",
  "longitude": -58.37723,
  "last_update": "2021-03-15T11:44:54.025248",
  "country_code3": null,
  "country_name": "Argentina",
  "ip_str": "200.16.89.104",
  "os": null,
  "ports": [
    80,
    443,
    179
  ]
}
```

Como podemos observar, la información devuelta sobre el objetivo incluye información valiosa, como ser puertos abiertos, el código de país y nombre del mismo entre algunos otros detalles. La respuesta completa incluye mucha más información que por cuestión de espacio no voy a mostrar en esta práctica.

### Localizando Webcams vulnerables

Hasta acá vimos como obtener información básica con Shodan usando Python, veamos ahora de que manera podemos localizar webcams vulnerables.

Para esto usaremos la siguiente query:

```python
query = 'IPCamera_Logo country:AR'
```

Y consumiremos esa query en nuestro script de Python no con el método **`host()`** sino haciendo uso del método **`search():`**

```python
"""
    UTN-CEH Práctica Localizando Dispositivos IoT Tzero86
    Script básico para localizar dispositivos mediante queries
"""

__author__ = "Tzero86"
__contact__ = "twitter: @tzero86"
__date__ = "2021/03/15"


import socket
import json
from shodan import Shodan

api_connection = Shodan("TU_API_KEY_VA_AQUI")
query = 'title:"Android Webcam Server"'

# formateamos la respuesta de shodan, para que se 
# aprecie mejor la estructura y los datos devueltos
def format_response(target):
    formatted_data = json.dumps(target, indent=1)
    print(formatted_data)


devices = api_connection.search(query, limit=3)
format_response(devices)
```

Y de esta manera obtenemos los primeros 3 dispositivos expuestos a internet de este tipo.

```javascript
{
 "matches": [
  {
   "hash": -783407210,
   "ip": 3007196261,
   "org": "PUNTA INDIO DIGITAL S.A.",
   "isp": "Red Intercable Digital S.A.",
   "transport": "tcp",
   "data": "HTTP/1.1 200 OK\r\nServer: WebServer(IPCamera_Logo)\r\nContent-Length: 2032\r\nContent-Type: text/html\r\nConnection: close\r\nLast-Modified: Thu, 11 Aug 2011 06:50:00 GMT\r\nCache-Control: max-age=60\r\n\r\n",
   "asn": "AS27983",
   "port": 80,
   "hostnames": [
    "101.44.62.179.unassigned.ridsa.com.ar"
   ],
   "location": {
    "city": "Ver\u00f3nica",
    "region_code": null,
    "area_code": null,
    "longitude": -57.33691,
    "country_code3": null,
    "latitude": -35.38796,
    "postal_code": null,
    "dma_code": null,
    "country_code": "AR",
    "country_name": "Argentina"
   },
   "timestamp": "2021-03-16T02:36:35.691676",
   "domains": [
    "ridsa.com.ar"
   ],
   "http": {
    "robots_hash": null,
    "redirects": [],
    "securitytxt": null,
    "title": null,
    "sitemap_hash": 1473104497,
    "robots": null,
    "server": "WebServer(IPCamera_Logo)",
    "host": "179.62.44.101",
    "html": ""},
   "os": null,
   "_shodan": {
    "crawler": "bf213bc419cc8491376c12af31e32623c1b6f467",
    "ptr": true,
    "id": "7728b63d-9637-4da0-bef6-a5d044634117",
    "module": "http",
    "options": {}
   },
   "ip_str": "179.62.44.101"
  }
```

Al igual que antes vemos solo una parte de los resultados devueltos por nuestra query. En este caso podemos ver que este dispositivo es en efecto vulnerable y no cuenta con ningún control de acceso. Basta con dirigirnos a la IP y Puerto devuelto en los resultados y podemos ver la webcam en vivo e incluso controlar el movimiento de la misma. Todo esto sin haber siquiera ingresado un usuario y contraseña.

![](<../.gitbook/assets/image (117).png>)

### Localizando Servicios RDP infectados con Ransomware

Veamos ahora como podemos localizar dispositivos con servicios de Remote Desktop Protocol, los cuales hayan caído victimas de ransomware:

Usaremos la siguiente query:

```python
query = '"attention"+"encrypted"+port:3389'
```

En este caso no hace falta revisar el script, solo necesitamos actualizar la variable query en nuestro script y ejecutarlo. Al hacerlo obtenemos la siguiente response desde Shodan:

```python
{
 "matches": [
  {
   "hash": 1447427490,
   "tags": [
    "self-signed"
   ],
   "vulns": {
    "CVE-2019-0708": {
     "verified": true,
     "references": [
      "http://packetstormsecurity.com/files/153133/Microsoft-Windows-Remote-Desktop-BlueKeep-Denial-Of-Service.htmlhttp://www.huawei.com/en/psirt/security-advisories/huawei-sa-20190529-01-windows-en",
      "http://www.huawei.com/en/psirt/security-notices/huawei-sn-20190515-01-windows-en",
      "https://cert-portal.siemens.com/productcert/pdf/ssa-166360.pdf",
      "https://cert-portal.siemens.com/productcert/pdf/ssa-406175.pdf",
      "https://cert-portal.siemens.com/productcert/pdf/ssa-433987.pdf",
      "https://cert-portal.siemens.com/productcert/pdf/ssa-616199.pdf",
      "https://cert-portal.siemens.com/productcert/pdf/ssa-832947.pdf",
      "https://cert-portal.siemens.com/productcert/pdf/ssa-932041.pdf",
      "https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-0708"
     ],
     "cvss": 10.0,
     "summary": "A remote code execution vulnerability exists in Remote Desktop Services formerly known as Terminal Services when an unauthenticated attacker connects to the target system using RDP and sends specially crafted requests, aka 'Remote Desktop Services Remote Code Execution Vulnerability'."
    }
   },
   "timestamp": "2021-02-25T01:35:44.959900",
   "isp": "Free SAS",
   "transport": "tcp",
   "data": "Remote Desktop Protocol\n\\x03\\x00\\x00\\x13\\x0e\\xd0\\x00\\x00\\x124\\x00\\x02\\x01\\x08\\x00\\x02\\x00\\x00\\x00\n\nAttention!\nYour PC ran into a critical problem and all files have been encrypted with .missing extension.\nIncluding all partitions from all drives. Its impossible to decrypt your files by yourself or with\nthierd parties softwares and doing such a thing could damage all files forever.\nThe only safe method to recover your data is contacting the email below and purchasing for the right\ndecrypter software.\nEmaik  pcsolutionsmail.ru\nID code: .MISSING B1F2DOO3FR\nContact the email with your ID code and 1-2 files for free decryption to make sure the data is still safe and\nundamaged.\nIf you dont receive an answer within 12 h, email again from another email service.\nThe faster you purchase the software the sooner you get back on track. 1\nom.\n4 Windows Server-2008rz\nStandard",
   "asn": "AS12322",
   "port": 3389,
   "ssl": {
    "chain_sha256": [
     "0297e31a785b7e6b74595ef1cd1a61a5fb871631c4a449f273b6cf23cd62064c"
    ],
    "jarm": "06b06b00006b06b06b06b06b06b06b2bb1101b28b790bf5d9d4dcad463fdc2",
    "chain": [
     "-----BEGIN CERTIFICATE-----\nMIIC/DCCAeSgAwIBAgIQGEz28WkG+5VD2Hk+wxlBvDANBgkqhkiG9w0BAQUFADAn\nMSUwIwYDVQQDExxTRVJWRVUyMDA4LUJJUy5waXNzb25uaWVyLmZyMB4XDTIxMDEy\nMzEwMjIzM1oXDTIxMDcyNTEwMjIzM1owJzElMCMGA1UEAxMcU0VSVkVVMjAwOC1C\nSVMucGlzc29ubmllci5mcjCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEB\nAMT+xDunxgCaEBi0A3T/lF3XtBvHOvBv0vVOwD2lvzKIHr+qqGrvqXcX+wOLfBTq\nK/rMHr9akQP4DKXPYKfb8CjK9/LGw35j4YFteMOH4JqdkfAtid94JTpgn4nPMUlN\nTAHYVi38LbwCOMbfxixTiuBs9/OH7D3SLWQfHrrqBVDTANUWtJJlkiWOPX2f0vMl\ncSwpwtrVfllj8YEw2IaMaQnubMtiFnbOx3jbpqjIR2mO43xzOXnSqPfrGBfQmWES\n/PZEo/m2vAD0ycPucEjYA+//7T7XzoaQWcTKXR5X5PeXZypGVj2sukJAS1qmyn4a\nXgFYu4QV7uC8aYtNn3c1zoUCAwEAAaMkMCIwEwYDVR0lBAwwCgYIKwYBBQUHAwEw\nCwYDVR0PBAQDAgQwMA0GCSqGSIb3DQEBBQUAA4IBAQCfTP5PPnpD/5qasaUj7PrB\nfeQwawbSy4W8OCcvmphWyIADQMMdlgGOxNehtPQ060xhssJHMxTiDLortanMd79T\nQ2xXHU5UJm+H+E3PiM4k+jzv4viLDeJuFojgM8TuU1vHTNo7GTRn289YAlqkHbog\nkInfXDg1O8e2HXFVKEIYnkucz2KRYGt9u0Ay5AsQeOY9M6utPyMOwnd6Xtx0CwSE\nsUL4QI587BJ/+X/mpn/WNEg4Z6PD3Nb6zyPDB5hno8FQwovNwUhuhd0nmbJ1ougN\nB/O0eUqSdh6VpE+RHWyfny9JlUO4ejkove4rGS7Juw+b3+h5U+gsYFesmwJbBgKX\n-----END CERTIFICATE-----\n"
    ],
    "dhparams": null,
    "versions": [
     "TLSv1",
     "-SSLv2",
     "-SSLv3",
     "-TLSv1.1",
     "-TLSv1.2"
    ],
    "acceptable_cas": [],
    "tlsext": [
     {
      "id": 65281,
      "name": "renegotiation_info"
     }
    ],
    "alpn": [],
    "cert": {
     "sig_alg": "sha1WithRSAEncryption",
     "issued": "20210123102233Z",
     "expires": "20210725102233Z",
     "pubkey": {
      "bits": 2048,
      "type": "rsa"
     },
     "version": 2,
     "extensions": [
      {
       "data": "0\\n\\x06\\x08+\\x06\\x01\\x05\\x05\\x07\\x03\\x01",
       "name": "extendedKeyUsage"
      },
      {
       "data": "\\x03\\x02\\x040",
       "name": "keyUsage"
      }
     ],
     "fingerprint": {
      "sha256": "0297e31a785b7e6b74595ef1cd1a61a5fb871631c4a449f273b6cf23cd62064c",
      "sha1": "8bcd285685e971d004ffb766700d00d2640a8608"
     },
     "serial": 32301095059340659751689116196458283452,
     "issuer": {
      "CN": "SERVEU2008-BIS.pissonnier.fr"
     },
     "expired": false,
     "subject": {
      "CN": "SERVEU2008-BIS.pissonnier.fr"
     }
    },
    "cipher": {
     "version": "TLSv1/SSLv3",
     "bits": 128,
     "name": "AES128-SHA"
    },
    "trust": {
     "revoked": false,
     "browser": null
    },
    "ja3s": "4192c0a946c5bd9b544b4656d9f624a4",
    "ocsp": {}
   },
   "hostnames": [
    "lns-bzn-23-82-248-108-145.adsl.proxad.net"
   ],
   "location": {
    "city": "Cr\u00e9teil",
    "region_code": "IDF",
    "area_code": null,
    "longitude": 2.4716,
    "country_code3": null,
    "latitude": 48.7926,
    "postal_code": null,
    "dma_code": null,
    "country_code": "FR",
    "country_name": "France"
   },
   "ip": 1392012433,
   "domains": [
    "proxad.net"
   ],
   "org": "Free SAS",
   "os": null,
   "_shodan": {
    "crawler": "a3cc14ebb782071aec2032690d4fd1979446a9ab",
    "ptr": true,
    "id": "1758c294-2b39-4a14-8262-0baec1181cce",
    "module": "rdp",
    "options": {}
   },
   "opts": {
    "screenshot": {
     "data": "/9j/4AAQSkZJRgABAQIAJwAnAAD/2wBDAAYEBQYFBAYGBQYHBwYIChAKCgkJChQODwwQFxQYGBcU\nFhYaHSUfGhsjHBYWICwgIyYnKSopGR8tMC0oMCUoKSj/2wBDAQcHBwoIChMKChMoGhYaKCgoKCgo\nKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCgoKCj/wAARCAMgBAADASIA\nAhEBAxEB/8QAHAABAAMBAQEBAQAAAAAAAAAAAAQFBgcDAgEI/8QAUhAAAgICAQMCAwQIAgcEBQkJ\nAAECAwQRBQYSIRMxFCJBFVFhlAcjMlNUcdLTQoEWM1JVkZXRFyRioSVDcpOxREZWc4KDoqPB8DU2\nRXSSpLK0/8QAGwEBAQEBAQEBAQAAAAAAAAAAAAECAwQGBQf/xAAyEQEAAQIEBAUCBgIDAQAAAAAA\nAQIRAyEx8BITQVFhocHR4QRxFCKBkbHxBSMGMlJC/9oADAMBAAIRAxEAPwDnMpKEXKT1FLbZp6Og\nuqr6oWV8LZ2yW135FEHr8Yymmv8ANGR5F64/Jf3VS/8Agz+lbuVSyLF3f4mfsY2JVRMRS+f+nwac\nSJmpx1fo66uf/wDJJfnMb+4P+zrq7/ckvzmN/cO3zuuroVtkqFFxU1F5Fffpra+Xe/r9x85GRfRd\nfVdW4WUrusTa8LaW/wAfdf8AE8/4mvwer8Hh+LiEv0e9WR9+Fl+bx/7hmLa503WVWwcLapyrnCXv\nGUXpp/immj+iJcsnJfMcB5yfqdRc1P7+Ryn/APnTO+Fi1VTaXm+owaMOm9KGAD0PIAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAicr\n/wDuvM/+pn//AKs2Wb1Xyvx+RFU4/ibXmMvv/wDaMdyacuNy0ltuma//AAs6HLp7LzM++/FxLraL\nbJThZXByjKLe0019Dz4tr5vVgX4cl5zXNcnZxtF+HjdPWV/A0OVzzl66kqo9y9P109pprXZv/wCJ\nL5XqqWRHqp/EYfr4/dDE87WTXK+DWmn5cdN+PdS+6JV0dH8i4r/0flf+5l/0Pq3o7kVF/wDo/K/9\nzL/oebhpezir7Mm+q+VjNbpxvf8A2Zf1GdssndmZ9tiSnPMyJSS+jd0zbZvS3IVvcsDJST93U0v/\nAIGLyO15/IOtpwebkOLXs07p6Z6cK3Fk8mNfgz7+75AB6HkAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAACM8DEb28XHb/+rX/Qkgkx\nE6kTMaIv2fh/wmP/AO7j/wBB9n4f8Jj/APu4/wDQlAnDHZeKrui/AYf8Jj/+7X/QkxSikopJLwkv\nofoLERGhMzOoACoAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAA2XP8DgYvTNfwsJLl+PVM+Qk5NqUb490fG9LsfbF6+sixw+h+Ju5fprFl\nztDhyVEbbYRVvfJuU1utunSXypfN52n9NHOcWmIvvu6xg1Tl4X87OeA0FXTSnG2+XL8bTx0bfQrz\n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\ntKqVm4uSklHaretRa+j14byXGZ+RxnIUZuFKEMmiSnXKdcZqMl7PUk14/kTZdRcpLJpv+IirKYW1\n19tMIqMbXJzSSWvPfL+W/GtI5101TnTvcuuHVRETFUX0XfD8HgYXUPTuPnZE787IyMWyzE+GjKhV\n2SjJRlNy232tbXZrzrZ4vpdZGfhRjlKuOdTl5KSq8Vql2/L7+d+n7+Nb+uisr6k5OurBgraXLClC\nWPbLGqlbX2PcV6jj3uKf+FvX01o9MPqrmMPFdFGTUoasSlLGqlOKsTU1Gbi5RT2/CaXlkqpr1ie/\nw1TVh2tMdvnqvOO4PAwuK5j18mORyceKhkumWMuynvnVKLjNt7koySfyr9ppN+T3yOjMazlra8/m\nPRvv5SfG1Kjj4qMrEoNScYyioR3PTST140n51mn1Ry74yXHvJh8NKlY0v1FffKtNOMHPt7mk0tbf\nj2R83dS8tdlV5NmXu6vLedGXpw8Xvt3PWv8AwR8e3j2JNNd73OPD4Yi27ffutON6Tx8ivHqy+Tlj\n8hkUX5NVMcb1IdlSn4lPuWpN1y0kmted/QjdGdNx6iyp1XZNmJWpQrjd2Vuvvk9KMnOyHn3aUe6T\n09J6LLgurYcdw10b8vKyMt0311Y/wVKhW7U0367k7FH5nLsUUm/+Jn+E6g5LhVNcddXBSnC3VlFd\nvbOO+2ce+L7ZLb8rT8l/PN99/SxVy8piN/u1kOMxaul92YuNLIhw2RN2emm3ZHNUFLet77fG/fXg\npeA6bw+R43EyszlLMWWXmvBprhi+q+/tg1JvuWo/Pp+7XjSfnVZLn+SljSx3k/qpUzx3H04/6uVn\nqSXt9Z+d+/09vBY8b1TfxXTNGBx7UMyGZZkuydFdiinCEYuDkm4zTi/K01vwycNcRPefdZqw6p0y\n+Pd7V9ISfT2dn25UqcrFUp+hONajbGNig5Qfqeo1tvz6fbtNd2yx5Ppng+K4nqGEs/LyeS4/Jppj\nZ8GowUpep8q/W+U+1bbXjXhPZm49ScrHinxyyIfCut0vdFbm4OXf2ep29/b3edb1s+bOoeTtlyUr\nb67HyLUsnvorkpyW9SScfla29OOmtl4a75zu8fKcWFEZQ2md0zx9N+JUst/bNnOywXauPrVD81/+\nr7+ztSl3a7fLbi/C2UVfStFkKIX8lKvkcyF12NTHGTqlGuU188+5djbrlpKMkvG2t+K7/SvmXbOy\n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\nIOVFc5wjNNTjGbi5KL2/CetvfuJprmiYvn8e6U1YcVxNsvn79l1PoV/B8eq+UxXyeZ6DjiysqWld\nrt1qx2bSlFvdaWt6b0twsTgeIy8rPjRy+Z8Lg40si62WBFOTVkYdsI+r533bTbj+KX0rrOouTsws\nfGsuqksdRjTc8ev161F7io3dvqJL6Lu8Lx7H7mdR8ll2ZM7J40J5NTpudOJTV6kXJSfd2RW33RT3\n7/iW1eeff43/ACcWHll2+eq1xukI3chXU8+UcTItxasXI9DfrO/T9u7x2ru35fmOvrss+B6T4W3P\nrnfyGRmYP/eqLO3E7Grq6XNOP6xbjrbT2m3FJxSe1WV9R4+Nn9M1U2Zd3GcPfG9uyEY2Tk7FOeoq\nTS0lpLu+9+N6UPL6s5S3kqsuq+qv0J2SpjHGqjH51qTlFR7ZuS8NyTb+uzMxXOUT33uzVM4VOcx2\n+UPA4uPIV8k8K6yy3GiraqnVqV0O9Rb0pPUkmnpb8b8+PN5HpHCrtjXmcxOt3ZjwKJV4nqKV0Yx7\n+7512wUppJrub8vtRWdNcxXxXK3ctOdsc6uM5Y9dNMVXKck182mu2K3vSi96149zw4zqLlOMpnVi\nZEVCVnrL1KYWuFmtd8HJNwl/4o6fhefCNzxdN79WImiIvMb/AH9VjV0lKWTx1FmWoTy8fKvlqvar\ndLtTj7+d+l7+Nb+uiJzPD4fF28XXZnX2zyaKcm9RxklTCyMZJR3P55JN+PlXhefL184HVHLYGEsX\nGyK41KNkYynj1znGM01OMZyi5JPb2k9eWV2dnZGdZVZlWepOqqFMH2pahCKjFePuSQpiuKrzOX9/\nH7FU4dvyxvL5dNzeKwrutbqcSOL9m4PK4OG8aXHVQ7lKc1rvW3Jai9t+Z78+yM5kdIU/Y9nJX8pi\n4ttvq20Y851RThGyUdPdint9stKNcl7eV51SR6l5aOZflrL/AO8X5NeXZL04fNbW24S1rS05Px7f\nej8XUXJfZ7wrJ492Pubir8Wq2Vfc9y7JSi5Q2/OotefJimiuLZ7tHq3OJhze8bzevVPC1cJm1U0X\n5OTVNNxyLKI11WpPXdVKM5qcfx8fdrZqepOlOMy+puUjg8jHGjVnxx7alidlVPqdyh2y7ltKSSl4\njrba7kjE8ry+Xyjp+LdCjSmq66MeuiEdvbajXGK2/q9b9vuLHL6kzebujXz2bKOLO1XXWYuHUrJy\n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\n7ZyjLtjDs29b13d/un4Mtjc3yuLhzxMXk86nFm3KVNeROMJNrTbinp7Xg/Kua5WrDpxKuTzoYlM1\nbVTG+ahXNPalGO9J787X1Lw1Xznee/0Sa6Okby3+q46p47jlx9fJ8X8XHvzb8W2OROMu6UFCSnHt\nS7U+/wDZe9a92SOV6aw8TpD47vsr5SmWP69Dudi7bYylFtelFRbSi0lOfh+dMys8m+yn0Z3WypU3\nZ2Obce96Tlr73pefwJGRy/JZODVhZPIZluHUkq6LLpSrgl7ai3paEUVRTEXJxKaqpqmNbug9PVcV\nB9JyqxL6L7uOzpZF8Zwl3pQvjJ9vbFt+PG5eFpefcqPsXC+y8jO4u/kcbFyOJnlOid8ZNyjkxrcJ\nyjGKlB67taXnX3GVq5bkacWvGq5DLhjVuUoVRukoRck1Jpb0tptP702ecc/MjSqY5eQqVW6uxWPt\n7HLucdb9nLzr7/I5c8V77z94/ZebTaImN5e3m3Od0dxFvJ5XH8Zbn1W4nKUYFluROFkbI2uS7oxj\nGOmnHXu9/h7FJ1DxPG0cBjcnxtPJY/q5l2K68ycZ+IRg9pqEfO5Pf3a1+JSR5HJnfOWVk5dsLrY2\n5CVzUrWm9Nt7+by9Np62W/VXU8+cxcPFjHN9LHlOx2Z2Y8q6yctLbn2xWkoxSSXjz95Ipri2e7R8\nnFRMTlbc/CU+n+P+0eE4feWs/kFjTlmOyPoxjbp6jX27lpS1vvW2n4RLxum+I5GyieJHksSqVmXR\nKrIthOcpVUuyMoyUI/XScdPW/fz4yj5bkXxsOPeflvAhLujjetL0ove9qG9J7bfsfeTznLZWbRmZ\nXKZ12XRr0b7Micp16e12yb2vP3FmmqdJ7pTXRGsdt+vk1nDcVXh9N5WTB2KzP4S22z1Ndq1mQgte\nPbUV9568zxuFxXSnUuFhVZ8Z4ufi0XW5Li42yirfmilFdqfv2ty8NeTG5fNcpmznPM5LNyJzg6pS\ntvnNyg33OL2/K2k9fehl8zymZiwxszks3IxoKMY1W3ynCKj+ykm9LW3r7iTRVPXd7/CxiUxFojdr\nfPk2fRXGYWLPj8iVedbnZnH5uRG2DiqK4xrth2uPbtv5W3LuWtpafufOF0bx1+Bxnr23059mXh05\nVUb/AFNV37af+qjGD7dNJTn7+dGOxeZ5TEwpYeLyWbRiSbk6K75Rg21pvtT15Xg+nznLOjHofKZ7\npxnF0VvIn21OP7Lit6jr6a9izRVNV4nt/M+9vNIxKOG0x39Pa/k12F0903kz4/tXMdmXyMuLW76k\n4zXZq79j2+dfq/8A8ZEo6Uw30zmZGVbOnk6qZZNSVzlGypXKvucPS1Fb7lt2b2v2dMylefmV+n6e\nVkR9O314dtjXbZ4+defEvC8+/hHt9s8n8A8F8jm/BNtvH9eXptt7fy715fkcFUdV5lF/+u/6920l\n0Zxl+Rk4WNLksfJxuTxuNnfkOLrsdjkpTUVFNfspqPc/DXnyZ3q3jeIws2ijgsueRNuVd1UpTm65\nJ6XmVVXv93a9a93s9+a6qebwFXFY0eR9KNkJyszs95MkoRahCv5IqEV3SetP6efBS8ny/Jcq63yn\nIZma6k1B5N8rOxP313N69kSimu96p3aPW5XVRw2pjP5n0tm6Hx/TmDxHI4WRh2WLJ7c/EyqZXO5V\n2QxZNpS9KtbTk00u5e2pMrXDHs/SD0lXmUu6iePx0ZVppb3XBLe001vW1rytrx7mSyee5jKnXLJ5\nXkLpVxcYOzJnJxTXa0tvwmvH8iJ8blfEU5HxN/r0qKqs9R91aj+z2v3WtLWvbQiiri4pnefuVYlM\n08NMby9nS+NnPP5KXKTy+Rt9XK5KpVZWT6yio4f7Xsvm863r2UV9CnzOnOm+Pw+LjyPL2VZl9eNk\nXdvqS3XZpy7Y+jpdsX+0rJb7X8u3pY+rkc2qKjVmZMEpTmlG2S+ace2T9/drw/vXgk4/UHM42JVi\n4/LchVi1SU66YZM4whJPuTUU9J787+8kYUxa09vK7VWNTVrHfzX/ABnG4eF+kTg6LMOx4F2RRKEJ\nZld6tjKSSl3qCTi37xcU9Jp6ftOt4/A5rPfIcn9sZNvJ8pPAqccmM509qh805OH6z9taiuzxH3+7\nFZHJ5+TyCz8jNyrc5SjJZE7ZSsTj7Pub3taWv5H1h8vyWFXkwwuQzMeGStXxqulBWrz+0k/m937/\nAHs1NE5T1YjEpi8dJ9p92v47pPism7hMWy/K+IzIZN910Zr0/TplatQioSluSrXnzrz8svY8JdNc\nNyV0odP51lyqnVPIk5uUaqJbU5blXW32NJt9qWppa8NvJV52XXPGlXlXxli+aHGxp1ee75P9ny2/\nH1JGTzfK5WRZflcnnXX2VOidlmROUpVv3g23tx/D2HBXrfd/6g46J/8AlpcrgOn6Omq+Qsz76cjN\n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\nGeVnSu5CFEqcvIv75ST9abi5S03LUGvK29ePpot+Sr6fzuoOMnlc7h2YNOBVVa4VZEe6dVcY9jfp\nbSk1+0k9Lb0npG5qn+fJiKKc8+3h2Z27p7Kq6ixeH9XHndlSpVN0ZP0pxt04T3rempJ+219xVX1u\nm6yqTTlCTi9e209Glr5Gqrr/AI/kcvPxMjHry6LZ24ldkaq64yj8sYzipajFaS17L6k3lM+fVHAy\njdmxty8DIyclyuUkq8ZqtQjHS0k5+IxXhN/RE4qotM/qvBTVeI16MUDa/o853F4bF5SMsjGw+Qtd\nTpyMh5MYdkXLvh3Y7Vi3uL15i+3z9C0xOsacafGY8c6qvDhj5qyqqKZ+i7JTulUu1x3KKcoOO99u\n/o9lrrmmZiIv/V/hKMOmqLzVb+3Niy4jiZ8rbCnGycdXyjbN1T704xrrc22+3XlJpae9rzpeTZ8Z\n1Ni3XcZn5/MWx5mjj7KZ5F9mTFTs9dtRtnT+sl+rfjT91FN6WiRd1Pwr5LItryoxqnn8heu2maXb\nbjKEHrX1ntfevd/eZqxKoiYiO/k3RhUTaZq3a/w5kDcfox5jA4TJuycvkPhbfWpThZLIVdlKbc/F\nP7U/2dKfyab2mXnSfJUXY8aeP5T4fFx8LPlk8dGmaVsnG1xubUezXa4Lbaku3SWmWvEmm+WntdnD\nwortnq5fj1xtvrrnbCmEpJOyxScYL732pvX8k2fEklJpSUknra9mdQw+pOGxuJ4TFfJK2rFzMHIj\nGz4my2lQ27tqX6uKTbSVa8pLe2QsbqzGro47BfITjxzxM6vLpUJdk5zlc61Ja+b9qDT89rf08knE\nqztTu1/hYwqbRert5+zGcdxNmbx2fnO+ijGw1Hvla5bnOW+2EVFN7fbL30vHlorjR8Rzk8PozmeM\njnX1SysiiUKISkozilNWb14/2N799L30ajM6srzuY6hnidQ2cdddfW8LPkrko48XJyqj2Rc4JuUZ\na1puL3+Opqqiqcsv69/JmKKZpjPP+/bzc0B0vG6zw8fLxnhZUsXFt5yeRlQjT29+NKNSfcktdsu2\nbcFv+T0jOdR87Hl+AxqsnLnk5tGde6/UUm4Y8ow7IptaUdqWo/T7kSK6p6LOHTEZVbz8fD+GXB1H\nD6k4bG4nhMV8krasXMwciMbPibLaVDbu2pfq4pNtJVrykt7ZCwurcXJs4+7l8+dmXj5mX6N065Te\nLXOqKqmvHiMZ/MorzHXhew5lV5/KRhU5Xq1+WEw8HIzK8qeNX3xxqvWtfcl2w7lHfn38yXt956ct\nxt3GXUV5Eq5Sux6siPY20o2RUkntLzp+TfY/VaqWTiy6lslmW8ZKizlE71G231lOCb7fUl2w7oqT\nj/ia9vJCzeqcfJ47J4+3NlZx/wBjY1NWP2SUHkw9Lb1r9pKM13v6LW9aROOq+m815dFtd5ePjLAg\n6zyHWXHz5jFycfOwI4FKvljVwhlu/G7secYwcZ91UV3OKar8bSfsvGT6F52nB5vkMzk8lLMyMeca\nszJldLstcotylKpq1bSku6L3831TZYrqm+XRKsOmJiOLVkgdMp6yrxsvD1yOPBWcz6+c8Ou707Md\nwpjLbsXfKMu2fdGW3JrbT8Mi4XUuJmvjZ8tytyysPNyp0WylbH0qnXH0o91a74196a7YeUt61scy\nr/ycqnTi3mxnEcbLlMujFpyaK8m++vHrrs79yc3ru2otJJ63t78+EyHfW6brKpNOUJOL17bT0dPv\n6q4ifNyyZ5tcu7L4u6yyuu5qXoqStluac3ra8ybk/wATNdH8tVhdQchbPka8DHyYyhLI77q7VBzT\n/VTqhJxl4/xLta2mIrqm+W8lqw6Yyv29WSB0SfUmD/oZPjsDLxIbjfG+rNWV6uRKU3KNiVbdMp9r\nj5sW04++tH1yXVfH8rz/AC75TNtyeNpyFm8cpRk1KUG9VJNfLGal58Lyk2OZP/lJwqYj/s59bVCF\nVMo312SnFuUIqW63trUtpLf18Nrz9/g8joGF1Li3U4Mr+SeNyrwciv4+UJt4t88iVnduKcl3QbXd\nBNrv/mSOC6lowuIz8afK8fdyVmVOd+VmvNcM2uUEkt16lPTUvltjr5v5ia6oict33vNYwqZmI4t2\n+7m4JOdTj02VLEyviYyqhOcvTcO2binKGn79r2t/XWyMdXCYsAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAD3zcz\nKz73fnZN2Tc0ouy6xzlpLSW358I8ABoXuAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABOjzHJ\nx4x8bHkcxcc/fFV8vSfnf7G9e/n29yCCTF9SJmNAAFAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAe2HLHhk1yzKrbcdP54VWKuTX4ScZJf8GbWXTfDZfUHHcVx2PykLMnB+M7p5ULW2\n8d2xgkqo/wCLS39V9EYQ0dfU3ZzWLyHwm/QwFg+n6v7X6h0929ePfetfhv6nPEiZj8vj/Hu6YU0x\nP5t5+yPPpfloZsMWVFPdOn4lWrKqdPp77e9293Ylta8y9/HuSemOmZ8j1V9lchKNKqhZbdrIrg5R\nhBy1Ccn2vfjUltafd5SZK4PrSziqMOiFGRGurCsw7LMbKdFrUrXapQmovsael7STW/v8QKOoPR6m\nu5bsy8n1K7a9ZeV6tr76nXuVnau7Xdv2Xha/EkziZx4T+/RqIwspv1j9uqPPgsq/Hy8zBqqniUze\n645lN1sI96im4xak1tpdyik979iyw+ic+58nDIyOPouwsd3ut52PJtqaj2S/WfI/L3v2aSa8o9cT\nrCOP0rLh44mTFTodE3Xl9lMm7O/1HV2+bNJR7nJ+EvB6ZHV+HfbcrOMybKr8OeJfZZlweTa3OM1O\nVqqSk12JfNFvW/PtqTOJpELTGFlNU9vnop6ul+WtpxbK8auXxUq411+vX6n6x6g5Q7u6MZNrUpJL\nyvPlEa3hc6vNysR11yyMWuVtsYXQn2xity8ptNpe6Xlae14Zpl17ZKnifWhycrMB4/6mHJOOJaqZ\nRa3S4PUmorypa3519Cn6XulPqqrL9TGopVkrL/iLYwj6T8WR8td24ya7Vtv6I1evO/j8M8OHlbPT\n5eOH0zyuXGM66KYVuqN3qX5NVMFGTajuU5JJvT1FvbS2lo8307yqvqplhyhbblvBhCUoxbvXbuHl\n+P2o+X48+5aWdS4eRfytGdx9t3GZdtdlVVF6pnSqk41pScZLXZJprX3PaJWF1rSsyOTyfFyyJ08j\n9o48acn0owlqK7JbhJyjqEfqn493snFXrbeXyvDh6X3n4fbebO8ZwnIcplZOPhUKduNB2Xd1kYKu\nKkouTlJpaTa29+F59kz7zuA5LAjlSysdRhjOpWTVkJR/WJuDi02pJpNpx2i16Tz8KFvUdvJR3TkY\nM0qVeq5zburfbCTT+bSb9n7ex9T6pxL45eLl8ZdLjLa8euqmrKULK/RTUG5uDUt90t/Ktt+NDiqv\na3b5OHDtM3729Fff0tzFFV9luIoqnIeJKPrVuTu+X5Ix7tzfzLxFP/yZP4zo/Juu5PGynT8Xj4cs\niuFOZTNRkrIRasak1BJSk33OOtbfhM9OS61tyc+vMxsOFF1fKz5SHdZ3xTkoJQa0tpdnv9d/QhS5\nvj6Jcp9l8bk0Qz8WWPON2WrexuyE9x1XHwu3Wnt+fcl8SYzjdvdYjCidd3+3Z4V9L8rZytnGqrGj\nmwUGq7MumHqd6Tj2NzSntNa7W97R4vgORWLjXumv/vMlCmn1q/Wsbk4rVXd3tdya3268Gl4fr1cd\nk13rAyI2V1YlanjZnoykqIdrjOSg265+G4LXsvL0Q8XrCOJi4voYVluZj5UcquzKvjbCrU3Ptqj2\nKUE3ra72n5et6at8S+icOFa981Ln8Hm4GVj0ZKxlK+XbCcMqqyve9NOcZOMWnre2tb86NLl9BXY8\nudxqsirIysDIorrsWTTCpwn6ibm3JqD3CKSck9vX1RTdWc/9vZNNqlyrUHJqOfyDy1DbT1DcIuK8\nfjvwSOd6op5KPL+jgWUS5O+nJtc8hTUZw799vyLw+/2fla93vw/PMRfLv+8el1/1RM2z7ftPqg1d\nMcvZLKj8LGE8a2VE423V1ylZH9qEFKSc5L7o7flfeiLx/EZfIYuRk46ojRRpTsvyK6VtptRTnJd0\ntRfhbfg1kv0g2WrkYSq5PFqysueXD7P5J48oucUnGb7Gpx+Va8Jrz95Q9N85TxFGZDIxb8uN8dKl\n3xWPJ6aTsrlCXe03tNOLX0a9xE4lpvGeXyk04V4tOWd/R8w6X5aWFXlehVGmcYWalk1RnGEmlGyU\nHLujBtr55JR8p70zRdQ9BLFlyn2dk06462rHlG/Ox5O+clLclqS9P2WoPb9/PhlRldT03YU3Hj5w\n5S3Drwbcj191uuCitxr7dqTjCKb7mvfSW1r9z+qKs+fULvwbIrlL4ZMPTyEnRZDu7dtwfcvne18r\n8e6JPHM5bzj0vuyxyopz1+PdXR6d5aXp6wp/PlSw1uUV+uj+1F+fGvvfjw/Phn7j9Ocnk42LfjVU\nX15N1dEPSyapyU577IzipbhvT/aS9i8yuu7b/i9YMK1fj9kUrP2L2pqd3t5cvVs8fTa8+D2r69VW\nFx2PXgZChiX4t/pPM/Up0+/p19mod725Pcnt7F8Tt2+U4cLv3+FEuleXcshKmjdEuyX/AHun5pqP\nc4Q+b55pa3GO2tpNbPzjenrc/pvkeXhkY8IYdldbqnbXGU+5SbaTkn47Vpafdt69mWfEdZ2YPFZO\nBL7TqqsyZ5MJ8fyDxZbmknGfySU4+FrwmvPnyU/GcrTi8LyfHZGNZdHLlXZCcLVB1zr7tNpxfcvn\ne14fj3NRNfXw+SYwomLT3+Fp1X0bncRy2TVh49l2GstYtL9SFlrlLfYpQj80XJJtbit/QpeV4bN4\nu2qvKhU3bvsdF9d8W09Nd0JNbX1W9ra+8vv9Nba+V5LPx8KMLsrkKM+CnZ3Kt1Ob7X4Xcn3e/j2I\nPVXUC53Lou7uXarcn253IvK7NtPVbcIuK8fXf0+4zRzItFW8o9SuMO0zTOfT959HlLpblY8g8L08\nV5EISssUc2lxpjF6fqSU+2vT8ak15aQr6W5eeXkY7x6q5Y8YSsstyaq6kp/sNWSkoPu91pvfnW9F\ntk9YYmRdONvGZFuLfiyxsqduVF5V25xmpO5VJNpwjpyjJ62m/bXhLqjEyK8nDzuMtnxU40KmmrKU\nLa/RUox3Y4SUtqct/Ktt+O3WixOJ23v+yacLvv8AbfZFxOjudy3YqsJRlDIliuNt9dUndFJutKUk\n5S01pLbf03pkOrgeRs4yWfGmCx0pSSldCNkoxepSjW33yinvbSaWn9zLeXWNlmZhZFuFFyxuVfJd\nsbNJrVaVa2nrSrXnz7npb1pZf079l2rk6lCNsILF5F1UzjOTlq2rsanpya2nHa0vpsROJ2Xhwu+/\n27KfL6b5TF5iHFX0VrkJ71THIrk0/Ph6k0n48J+Xta91v54/p/ks+umzGoh6VsbJqyy6FcVGGlKU\nnJpRim0tvSb8LySMrkcjmusbOSwvSw8m/J+Ir9XIjCNUk+5bsl2pa17vRbct1NhLqDkq6sONvCW4\n7wlTj2ek+xTVndCTUtbsTl5i/D1ocVdo7sxTReZvldn83geSwYZU8nH7YY0q42SVkZR/WJuDi02p\nJqLacdr8T5+xOR+25cQsZvkYzdcqlKPyteXuW+1JJNt70kt7Ll9UYl0MzFzOMtnxtsKIU01ZShZX\n6Kahubg+7alLfyrbfjWtH5jc4+Q66yOTWHSoZ07VZjW5UaY9lkJRlH1ZaUXpvUn9deH7Fia+sd/S\n3qTTh5Wnt8oc+lOYryKaZ49K9WmWRC34qr0vTUu1zdnd2KO/G2/La17o9eS6Uy+O6dXKZV2NGXxM\n8eVCvqlJJKLUlqbct930XtqXs0zTZ3U2N03kcdicYrHVXxs8XIWFyKdtTnc7V25EI9rkvl20nHy1\nozXM9RU8nxd+JbTn2WvKeVVkZOarrE5QhCSsfYu/xBaa7dfiYirEnTT59m5ow6dZz+Pt3elnSeTd\n09xfJcdXK15GPdddGd0It+nZNP04NqUtRim0u5re/G0Vf2DyD4+nNqqqvotnCqKovrtmpy32xlCM\nnKLenpSS9i3wuqcbG4zj6/s2yXIYGPdRRkfEpV/rJTblKvs8td71qS8rb37E2XXtlXGYWNhYl1Vm\nLZjXQjPKc8eEqU/MKu1dve3uXltv6mr1xM2jr5Xn0ZthzGc9PO0fKvwulL3Pk6Mztll4+I7qasTI\nqubs9WuHZJQctP538vh70VMeGzp8z9lVVQtzu7tcK7YTimlt7mn26S3t70tPetMu+L6rxuD5TMze\nB467Hnk48qXDJyY3xi3ZGW1+rj8uouOn5872VvH8xjcb1BLOwsGUcKcZ1SxbL+5+nODhOKn2rXiT\n09PXje/qia+sdPMqjDtaJ6+T7j0nzM8p0Qx6ZtUPK9SOVU6vSUlFzVql2NJvTe/HnetM/MHprMt6\nqxOCy3Xi5F9tcHOVkJRUZpNSUu7tl8r2tP5vCXlkqzqamGHfhYOBZVhSwpYdUbchWTi5WxslOUlF\nKT3HWko+NfjuNZ1C3z/EcpHGSlx8MaCrc9qfoqK3vXjfb/lv6lia75pVGHbKc/68Pu+cvpjkcevI\nvfwrxach4zv+Mp7ZT+Xwn3+fEk3revO/Z6/JdMcrHMqxlTTOVtTvjbXlVTp9NNpydqk4JJppty8P\nwTf9JsWueJGjjHLFx+SlyHpZF6s71KME4NqCX+B+dfX28eZ3IdcQz7owy8TNycOWJPDuWTn+pkWR\ndnqKStcNJqSjr5WtLWjMTiWjLdvffVuacG857v8AbsgR6Vut4SF+P22Zqy7qbXHIqdEK6665d3qb\n7febW+7T8JeffMmqxOqcbG4LI4OPHWz4m/InfZCeSvV04wUdTUElKLhvetPemjPZ92PdbXLExfho\nRqhCUfUc+6ailKe37be3r6bN0zVf8znXFFo4Z3d852HlYGTLHzsa7GyIpOVV0HCS2trafn2LbqLG\n46jienLMDGupvycKV2TOy/vVk1fbXtLtXb/q3/k4r3TlKpzszKz8mWRnZN2TkSSUrbpucnpaW2/P\n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\n6/8A66Brg8GPx2D/AOv5Z6XN80t/96//AC4f9Cw5jOy839HWA82zvmueaT0l4+Fs+5fzJk8LhnvX\nU3T35+JD6mt4+rpfBwMLlOOz71ynxMo4d6t7YehOG3r28tL/ADNU0OOJ9XRXMU0TfPtLMgA9DQAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\nAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\n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\n",
     "labels": [
      "windows",
      "login",
      "desktop"
     ],
     "mime": "image/jpeg",
     "hash": -847163145,
     "text": "Attention!\nYour PC ran into a critical problem and all files have been encrypted with .missing extension.\nIncluding all partitions from all drives. Its impossible to decrypt your files by yourself or with\nthierd parties softwares and doing such a thing could damage all files forever.\nThe only safe method to recover your data is contacting the email below and purchasing for the right\ndecrypter software.\nEmaik  pcsolutionsmail.ru\nID code: .MISSING B1F2DOO3FR\nContact the email with your ID code and 1-2 files for free decryption to make sure the data is still safe and\nundamaged.\nIf you dont receive an answer within 12 h, email again from another email service.\nThe faster you purchase the software the sooner you get back on track. 1\nom.\n4 Windows Server-2008rz\nStandard"
    }
   },
   "ip_str": "82.248.108.145"
  }
 ],
 "total": 1,
 "_scroll_id": "FGluY2x1ZGVfY29udGV4dF91dWlkDnF1ZXJ5VGhlbkZldGNoGRRNZXItT0hnQlRudmtkS1k4VWR4XwAAAAAHE9eFFndYWjhPWF9oUnptaXNyQXFMV0puRUEUUjk3LU9IZ0J4U05GMmVsR1VVRi0AAAAABye0zhZIaVdqdlJ0VFJKLUFGQjBMVW1lRUhnFDlJVC1PSGdCV3U3THNsTjVVYWxfAAAAAAcwid4WdHFzbjZ3eGZRQU92NTdocVk2WElVURRhQjMtT0hnQkVQLVFWWGoxVVhsXwAAAAAG_46EFlR6dFlDZWw1UU5XU250dTdHN3pORWcUUjdmLU9IZ0JEdXN6aWgxbVVUbF8AAAAAByIp5BZESGpxdnZTVVRUS2JGd0lZeURIU0xBFHM4UC1PSGdCVmRkbmdBbXlVUjlfAAAAAAcnU9oWY0ppeXVEc0RRY3E4aUI1Qm9peWtsZxRuTGotT0hnQjRJdkVMWVJXVWZ1QQAAAAAHHerdFmcxbUlwUzgwVFoycVZ3SHl6amlVSkEUTjFiLU9IZ0JmWU5yYldaaFViYUEAAAAABxIHWxZpcnVHT2Q3aVRoYUlZdURhNnNlYzlnFEZ5ci1PSGdCQlZyUkdXTkFVWnVBAAAAAAcPXF4WLVVVYTVOMTZTLW1uRHROdHZfcmNKQRRtb18tT0hnQnFtUzlQcUtxVWVKXwAAAAAHHCw0FnJSQks3a25GUTl5b0NGZVJyVXh1QXcUMDFiLU9IZ0IxaC0taGRIM1ViZUEAAAAABzJdgRZFcGstLXJKNVM2ZTJwT1UzcTNscVR3FFBLMy1PSGdCUEpqenp4WUlVWEtCAAAAAAcbLq0WLVZnSmF0dl9ReUdvNU1TZk5pa1BLZxQ1aHotT0hnQnJpODF0VGZFVVhKLQAAAAAG8sFhFmNZMDNVSUt6UUtPblVBTDkyR0xFeVEUUXQ3LU9IZ0JSN0RJMzkzSFVXSl8AAAAABx2izBZmQV95Rkxka1RER19paFRBNnJhUHdRFGVUai1PSGdCeE1ob1J5bkFVZXBfAAAAAAcvf9YWM2VWcUVoQWRSX1cxVkpNZmhKcXNEdxRSNlgtT0hnQmVNcU1Wc2FyVVdaXwAAAAAHHev4FnNHejlEdzZCUm15dGtvWkpETE1ZRlEUUURqLU9IZ0JUZF9HZ3RlLVVmMkMAAAAABx8cYRY3dnZWNk9XQlNYR0hTbTF5LTdOTVFnFDdXai1PSGdCb2M2TjJLMk1VZUdDAAAAAAcYi5kWMXJ4VldZTGVRQXk5NW1pMmJlVzhpQRRHTzctT0hnQmtjajlsYzZmVVN5RAAAAAAHJEpjFjRtcU83MzlfU1FXbG8tME1PV2VCRXcUSnE3LU9IZ0JIUUgwWVcxTVVVaUQAAAAABxQvnRZZc3g2Q0RJSlNIeUxrYm9qLVFQcWV3FG5Xdi1PSGdCSHRvenpqdkJVWkdEAAAAAAcS1pMWeEVXS2R0ajlRY2k4MElLSXdnUkhXdxQ0b2ItT0hnQlR0SktjWUtGVVhpRAAAAAAHFNa2FjVVYk04S3kyUjJTZ0huaE9FOEpVWWcUcXFuLU9IZ0JWR3pUbGx2MVVadUQAAAAABzAw7xZreGlKRzlXOVFtdWVybmZDQWlLX0ZBFGVXRC1PSGdCdzA3Y1dLcGdVWGlEAAAAAAcYPjwWb1ZxM05oaHhTZHFoMnJqSHJ0TzM4dxR2eDMtT0hnQnlpRnMydU5LVVdTRAAAAAAHEwOgFkVqZkZGa1A4U0VpSEJsZmE3STgwbFE="
}
```

Si vemos el screenshot que viene como parte de los resultados:

![](<../.gitbook/assets/image (30).png>)

El mismo resultado que obtendríamos realizando la consulta en la web de Shodan.io:

![](<../.gitbook/assets/image (187).png>)

####

### Localizando Impresoras

Veamos ahora como podemos localizar otro tipo de dispositivos, como por ejemplo impresoras. Para esto haremos uso de la siguiente query para alimentar nuestro script en Python:

```python
query = 'Printer Type: Lexmark country:AR'
```

Actualizamos nuestro script con la nueva query y al ejecutarlo obtenemos lo siguiente:

```javascript
{
 "matches": [
  {
   "product": "Samba",
   "hash": -975219927,
   "asn": "AS12150",
   "timestamp": "2021-03-15T16:50:58.075947",
   "isp": "COTELCAM",
   "transport": "tcp",
   "hostnames": [
    "host210.200-59-15.cotelcam.net.ar"
   ],
   "data": "SMB Status:\n  Authentication: disabled\n  SMB Version: 1\n  OS: Windows 6.1\n  Software: Samba 4.10.7-Ubuntu\n  Capabilities: dfs, extended-security, infolevel-passthru, large-files, large-readx, large-writex, level2-oplocks, lock-and-read, nt-find, nt-smb, nt-status, raw-mode, rpc-remote-api, unicode, unix\n\nShares\nName                 Type       Comments\n------------------------------------------------------------------------\nprint$               Disk       Printer Drivers\nIPC$                 IPC        IPC Service (gaston server (Samba, Ubuntu))\nLexmark-X264dn       Printer    Lexmark X264dn\n",
   "_shodan": {
    "crawler": "dfd12d70c30ccb3812bf26f89905deeb85e98c77",
    "ptr": true,
    "id": "dccc9ea1-a56f-4869-8008-6a25fd88a222",
    "module": "smb",
    "options": {}
   },
   "port": 445,
   "version": "4.10.7-Ubuntu",
   "location": {
    "city": "Santa Catalina - Dique Lujan",
    "region_code": null,
    "area_code": null,
    "longitude": -58.70673,
    "country_code3": null,
    "latitude": -34.38375,
    "postal_code": null,
    "dma_code": null,
    "country_code": "AR",
    "country_name": "Argentina"
   },
   "ip": 3359313874,
   "domains": [
    "cotelcam.net.ar"
   ],
   "org": "COTELCAM",
   "os": "Windows 6.1",
   "smb": {
    "shares": [
     {
      "type": "Disk",
      "temporary": false,
      "name": "print$",
      "special": false,
      "comments": "Printer Drivers"
     },
     {
      "type": "IPC",
      "temporary": false,
      "name": "IPC$",
      "special": true,
      "comments": "IPC Service (gaston server (Samba, Ubuntu))"
     },
     {
      "type": "Printer",
      "temporary": false,
      "name": "Lexmark-X264dn",
      "special": false,
      "comments": "Lexmark X264dn"
     }
    ],
    "smb_version": 1,
    "raw": [
     "0000009fff534d4272000000008807c8170000000000000000000000000049af0000010011000003320001000441000000000100a6660000fdf38080e0e6b65dbb19d701b400005a00676173746f6e00000000000000000000604806062b0601050502a03e303ca00e300c060a2b06010401823702020aa32a3028a0261b246e6f745f646566696e65645f696e5f5246433431373840706c656173655f69676e6f7265",
     "00000128ff534d4273160000c08807c8170000000000000000000000000049af6dd2020004ff0000000000a900fd00a181a63081a3a0030a0101a10c060a2b06010401823702020aa2818d04818a4e544c4d53535000020000000c000c003800000005828ae24685bbc179758fd300000000000000004600460044000000060100000000000f47004100530054004f004e0002000c0047004100530054004f004e0001000c0047004100530054004f004e0004000200000003000c0067006100730074006f006e000700080036d7dc5dbb19d70100000000570069006e0064006f0077007300200036002e0031000000530061006d0062006100200034002e00310030002e0037002d005500620075006e0074007500000057004f0052004b00470052004f00550050000000",
     "00000088ff534d4273000000008807c8170000000000000000000000000049af6dd2030004ff000000010009005d00a1073005a0030a0100570069006e0064006f0077007300200036002e0031000000530061006d0062006100200034002e00310030002e0037002d005500620075006e0074007500000057004f0052004b00470052004f00550050000000",
     "00000038ff534d4275000000008807c8170000000000000000000000e00c49af6dd2040007ff0000000100ff010000ff010000070049504300000000",
     "00000087ff534d42a2000000008807c8170000000000000000000000e00c49af6dd205002aff00000000136401000000000000000000000000000000000000000000000000000000000000000000000080000000000000000000000000000000000000000200ff0500000000000000000000000000000000000000000000000000ff011f009b0112000000",
     "0000007cff534d4225000000008807c8170000000000000000000000e00c49af6dd206000a000044000000000038000000440038000000000045000005000c03100000004400000002000000b810b810f05300000d005c504950455c73727673766300000100000000000000045d888aeb1cc9119fe808002b10486002000000",
     "000001b8ff534d4225000000008807c8170000000000000000000000e00c49af6dd207000a000080010000000038000000800138000000000081010005000203100000008001000003000000680100000000000001000000010000000c0002000300000010000200030000001400020000000000180002001c00020003000080200002002400020001000000280002000700000000000000070000007000720069006e0074002400000000001000000000000000100000005000720069006e007400650072002000440072006900760065007200730000000500000000000000050000004900500043002400000000002c000000000000002c000000490050004300200053006500720076006900630065002000280067006100730074006f006e00200073006500720076006500720020002800530061006d00620061002c0020005500620075006e00740075002900290000000f000000000000000f0000004c00650078006d00610072006b002d00580032003600340064006e00000000000f000000000000000f0000004c00650078006d00610072006b002000580032003600340064006e0000000000030000002c0002000000000000000000",
     "00000023ff534d4204000000008807c8170000000000000000000000e00c49af6dd20800000000",
     "00000023ff534d4275220000c08807c8170000000000000000000000000049af6dd20900000000"
    ],
    "capabilities": [
     "rpc-remote-api",
     "raw-mode",
     "unicode",
     "dfs",
     "infolevel-passthru",
     "large-files",
     "nt-status",
     "level2-oplocks",
     "extended-security",
     "lock-and-read",
     "large-readx",
     "nt-smb",
     "nt-find",
     "unix",
     "large-writex"
    ],
    "anonymous": true,
    "os": "Windows 6.1",
    "software": "Samba 4.10.7-Ubuntu"
   },
   "ip_str": "200.59.15.210"
  }
 ],
 "total": 5,
 "_scroll_id": "FGluY2x1ZGVfY29udGV4dF91dWlkDnF1ZXJ5VGhlbkZldGNoGRRWcTBLT1hnQlBKanp6eFlJam82bgAAAAAHG0rHFi1WZ0phdHZfUXlHbzVNU2ZOaWtQS2cUTmQ0S09YZ0JSN0RJMzkzSGlYN0oAAAAABx2-vxZmQV95Rkxka1RER19paFRBNnJhUHdRFEF1NEtPWGdCa2NqOWxjNmZpVWV3AAAAAAckZU0WNG1xTzczOV9TUVdsby0wTU9XZUJFdxRaXzRLT1hnQnJwdHY4bS1jaVVpdwAAAAAHF7m-FjF2NmxpVUFwUUFPQS1MWEVkTURBdHcUNFlNS09YZ0J2alBvMmt0c2lob2QAAAAABw3W-hZXQ05uZ3VNZlFncWZfQXJWNFlsSEp3FHJSd0tPWGdCcmk4MXRUZkVpbzRjAAAAAAby3SgWY1kwM1VJS3pRS09uVUFMOTJHTEV5URREMnNLT1hnQi1ZblpJODRTaWVxdwAAAAAG-cj5FlRGZkVoNXB2Uy11QzNIUV9CRFM2NWcUOEtVS09YZ0JlTXFNVnNhcmluNEQAAAAABx4EoRZzR3o5RHc2QlJteXRrb1pKRExNWUZRFHYyc0tPWGdCSHRvenpqdkJpYXF4AAAAAAcS77UWeEVXS2R0ajlRY2k4MElLSXdnUkhXdxR6UjRLT1hnQjRTWEFiMFVIaWRPeAAAAAAHCXTSFk42LTBkRDF1UUl5WXduQmZaX0g0X3cUY1ZJS09YZ0JDSjZ1ai1IT2lmZXcAAAAABwYPuxZwZWNYdF9xaVNXLUNrY3dUZFlBQUpnFFZPa0tPWGdCOFhMZnR2U1lpWE96AAAAAAcEKjIWWnFuOF9zcVJUOW1Kdm1pVWVaT3pmZxQ1R0FLT1hnQncwN2NXS3BnaVpPeAAAAAAHGFmnFm9WcTNOaGh4U2RxaDJyakhydE8zOHcUTElJS09YZ0JXYXI4eEdhaWllbXcAAAAABxlrpRZEWkFHZ01BNFRRLXhJMU45Q2tFWnpBFE5xa0tPWGdCVkd6VGxsdjFpYmF4AAAAAAcwS3sWa3hpSkc5VzlRbXVlcm5mQ0FpS19GQRRLM29LT1hnQmJmblJfOFVzaVFleAAAAAAHEEt7FnJKRGllWUdfU3BxR1cxc2ozcGp5eWcUZWQ0S09YZ0J4U05GMmVsR2lWbTAAAAAAByfNABZIaVdqdlJ0VFJKLUFGQjBMVW1lRUhnFF9uRUtPWGdCZU93OTdmOEdpWnF4AAAAAAceGQ8WdjRsY09MLS1SZEtSSEhLS3FOY0RlZxRLTGtLT1hnQjRJdkVMWVJXaVJheAAAAAAHHgVpFmcxbUlwUzgwVFoycVZ3SHl6amlVSkEUMjBZS09YZ0J0WWpmRWhPWGlWdXQAAAAABxKM1xZENHVsc09OQVFZaWZzbE01ZEVjUUhnFEVhNEtPWGdCczVJVVF0X2tpWFN4AAAAAAcYv7YWU2Jxc1YxVFFTSC1raXp4Vld0RkJfURRKTFVLT1hnQnp3QUlobVhUaWFtdwAAAAAHG49XFm42Ql95STRWVERhTURjaW1WVlpXTVEUZzhNS09YZ0JWZGRuZ0FteWlUdXcAAAAABydvqhZjSml5dURzRFFjcThpQjVCb2l5a2xnFERCMEtPWGdCRVAtUVZYajFpWlN3AAAAAAb_qSgWVHp0WUNlbDVRTldTbnR1N0c3ek5FZxRCbUFLT1hnQk03aDVVQmJXaVFLeAAAAAAHBZcVFnlTY0hsbEVxUkhhYnZHTVdndHdlU3c="
}
```

Si analizamos este resultado, podemos ver que entre los datos devueltos por Shodan se incluye una enumeración de los Shares de SMB, o Samba. Los mismos podemos apreciarlos en un formato más ameno al realizar la misma consulta en la web de Shodan.io

![](<../.gitbook/assets/image (184).png>)

De igual manera que Shodan nos provee este tipo de resultados, también puede incluir la lista vulnerabilidades detectadas en dichos dispositivos. En el caso de esta práctica no veremos mas que un ejemplo de este tipo de resultados tanto en modo API response como en la Web de Shodan:

```javascript
"isp": "Universidad Nacional de La Plata",
 "longitude": -57.95453,
 "last_update": "2021-03-15T20:08:12.310077",
 "country_code3": null,
 "vulns": [
  "CVE-2010-2068",
  "CVE-2011-4317",
  "CVE-2017-7679",
  "CVE-2018-1312",
  "CVE-2011-3368",
  "CVE-2011-3348",
  "CVE-2012-3499",
  "CVE-2012-4558",
  "CVE-2011-3607",
  "CVE-2016-8612",
  "CVE-2016-4975",
  "CVE-2012-4557",
  "CVE-2019-9639",
  "CVE-2019-9638",
  "CVE-2017-7668",
  "CVE-2013-6438",
  "CVE-2012-2687",
  "CVE-2019-9637",
  "CVE-2011-4415",
  "CVE-2012-0031",
  "CVE-2013-2249",
  "CVE-2010-1452",
  "CVE-2013-1896",
  "CVE-2017-3167",
  "CVE-2012-0053",
  "CVE-2012-0883",
  "CVE-2017-3169",
  "CVE-2011-3639",
  "CVE-2011-0419",
  "CVE-2014-0231",
  "CVE-2013-1862",
  "CVE-2014-0098",
  "CVE-2019-9641",
  "CVE-2011-3192"
 ],
 "country_name": "Argentina",
```

Esta lista de vulnerabilidades se presenta de la siguiente forma en Shodan.io:

![](<../.gitbook/assets/image (65).png>)

![](<../.gitbook/assets/image (185).png>)

![](<../.gitbook/assets/image (168).png>)



De esta manera vimos como podemos aprovechar el poder de Shodan.io para fácilmente localizar distintos tipos de dispositivos conectados a internet. Y vimos como ubicar Webcams, Sesiones RDP comprometidas por ransomware y finalmente localizamos una impresora cuyas SMB Shares fueron enumeradas por Shodan.io.

{% hint style="info" %}
Estas prácticas están sujetas a modificaciones y correcciones, la versión más actualizada disponible se encuentra online en [el siguiente link](https://tzero86.gitbook.io/tzero86/).
{% endhint %}
