# 5.2 SERVICIO DE DIRECTORIO DE LINUX

1. Dominios
2. LDAP
3. Instalar y configurar
4. Añadir cliente en OpenLDAP
5. Dominios Samba
6. Configurar Samba
7. Comprobar dominio Samba

![1.png](imagenes/1.png)

# DOMINIOS

Un dominio suele asociarse directamente a un controlador de dominio, (Active Directory de Windows) a políticas centralizadas y a una gestión unificada de usuarios y equipos. En Linux, sin embargo, el concepto de dominio existe, pero se articula de una forma más modular, más flexible y, en muchos casos, más transparente.

Un dominio, en términos generales, no es más que un conjunto de sistemas, usuarios y recursos que comparten una infraestructura común de autenticación, autorización y, normalmente, resolución de nombres. En Linux no existe una única “pieza” que lo haga todo, sino que el dominio se construye combinando servicios: DNS, **LDAP**, Kerberos, servicios de archivos, políticas de acceso y mecanismos de integración entre sistemas.

Resumiendo, en Linux no se “instala un dominio”, sino que se diseña y se construye, obligando a entender qué hace cada servicio, cómo se comunica con los demás y qué papel juega dentro de la arquitectura global.

# DOMINIOS

En un sistema local, los usuarios están definidos en /etc/passwd, las contraseñas (o hashes) en /etc/shadow, los grupos en /etc/group, y los permisos se gestionan directamente en el sistema de archivos. Este modelo funciona perfectamente para un número muy reducido de máquinas, pero se vuelve ineficiente y difícil de mantener en cuanto el número de sistemas crece.

Crear usuarios manualmente en cada máquina, mantener contraseñas sincronizadas, asegurar que los permisos son coherentes y revocar accesos cuando un usuario deja la organización se convierte rápidamente en una tarea poco realista. Aquí es donde aparece la necesidad de un dominio. Este permite centralizar la información de usuarios y grupos, de modo que las máquinas cliente no almacenan esa información localmente, sino que la consultan a un servicio central. Esto implica que un usuario puede iniciar sesión en diferentes equipos con las mismas credenciales, que los cambios se aplican de forma inmediata y que la administración se simplifica enormemente.

# DOMINIOS

El DNS es la base sobre la que se apoyan prácticamente todos los servicios de red. Cuando hablamos de un dominio como sistemas.local, estamos definiendo un ámbito dentro del cual los nombres de máquinas, servicios y recursos tienen significado. En un entorno de dominio Linux, el DNS no solo sirve para traducir nombres a direcciones IP, sino también para localizar servicios, especialmente cuando se utilizan tecnologías como LDAP o Kerberos.

Un dominio necesita un DNS coherente y bien configurado. Cuando un cliente Linux quiere autenticarse contra un servidor LDAP, necesita saber dónde está ese servidor. Puede configurarse manualmente, pero lo habitual y lo profesional es que lo descubra mediante DNS. Los servicios no viven aislados, sino que se apoyan unos en otros.

Crear un dominio Linux implica:

- ☐ Definir un nombre de dominio coherente, tanto a nivel DNS como a nivel organizativo. Este nombre será el identificador lógico del dominio y aparecerá en múltiples configuraciones.
- ☐ Configurar un servidor DNS que gestione ese dominio y resuelva correctamente los nombres de los sistemas implicados.
- ☐ Instalar y configurar un servicio de directorio, normalmente LDAP, que almacenará la información de usuarios, grupos y, en algunos casos, equipos.
- ☐ Configurar los sistemas cliente para que utilicen ese servicio de directorio en lugar de (o además de) los ficheros locales para la autenticación.

# LDAP

LDAP (Lightweight Directory Access Protocol), protocolo de acceso a servicios de directorio. Un servicio de directorio es una estructura jerárquica diseñada para almacenar información que se consulta mucho y se modifica poco, como usuarios, grupos, equipos, políticas o servicios.

LDAP actúa como el corazón del sistema de autenticación centralizada. En lugar de que cada máquina tenga su propia lista de usuarios, todas consultan al servidor LDAP cuando necesitan autenticar a alguien y este puede almacenar cualquier tipo de información estructurada: direcciones de correo, certificados, claves públicas, información organizativa, etc. Sin embargo, en el ámbito de sistemas, su uso principal es la gestión centralizada de identidades.

![2.png](imagenes/2.png)

LDAP Arquitectura

Con **arquitectura** LDAP, nos referimos a cómo se organizan los distintos componentes, la arquitectura en Linux es **modular**, lo que significa que cada servicio cumple una función concreta y puede ser sustituido, escalado o reforzado de forma independiente. Esta modularidad es una de las grandes fortalezas del ecosistema Linux en entornos profesionales.

☐ El núcleo lo constituye el **servidor de directorio** LDAP, que actúa como repositorio central de identidades. Este servidor no autentica por sí mismo en el sentido tradicional, sino que almacena la información necesaria para que otros sistemas puedan hacerlo: usuarios, grupos, identificadores numéricos, shells, rutas de directorios personales y atributos organizativos.

☐ El DNS juega un papel estructural, ya que permite localizar los servicios del dominio de forma dinámica y coherente. En arquitecturas bien diseñadas, los clientes no se configuran con direcciones IP “a mano”, sino que descubren los servicios mediante nombres de dominio.

# LDAP Arquitectura

En los sistemas cliente, la arquitectura se completa con componentes de capas de autenticación y de integración entre el sistema operativo y el dominio como NSS y PAM.

La idea consiste en disponer de un servidor que facilite la autenticación de los clientes, de modo que éstos recurran al servidor cada vez que un usuario necesite identificarse. De esta forma, la cuenta de usuario no es específica de un equipo cliente, sino que será válida en cualquier equipo de la red que haya sido debidamente configurado.

![3.png](imagenes/3.png)

# LDAP Arquitectura

NSS (Name Service Switch) mecanismo que utiliza Linux para localizar información de usuarios y grupos. Permite al sistema saber si un usuario existe y obtener sus datos (UID, GID, directorio personal, etc.), consultando distintas fuentes como archivos locales o un servidor LDAP. En un entorno con LDAP, NSS se encarga de “preguntar” al directorio por los usuarios en lugar de buscarlos solo en el sistema local.

![4.png](imagenes/4.png)

PAM (Pluggable Authentication Modules) es el responsable de la autenticación. Su función es comprobar si el usuario puede iniciar sesión, validando la contraseña y aplicando las políticas de seguridad configuradas. Cuando se usa LDAP, PAM consulta al directorio para verificar las credenciales y decidir si se permite o no el acceso al sistema.

Es el método que utilizan la mayoría de las aplicaciones y herramientas de Linux que necesitan relacionarse, de algún modo, con la autenticación de los usuarios.

![5.png](imagenes/5.png)

# LDAP Arquitectura

Una de las ventajas que aporta LDAP cuando lo combinamos con NFS es que podemos guardar el perfil de una cuenta de usuario en el servidor NFS. De este modo, cuando un usuario se autentica en cualquier equipo de la red usando su cuenta LDAP, podrá acceder de forma automática a una carpeta compartida donde se guardan los perfiles de las cuentas. (Perfiles móviles de usuario)

A continuación, se muestran los pasos a seguir

![6.png](imagenes/6.png)

1. Crear una carpeta en el servidor para guardar la carpeta /home de los usuarios móviles (el equivalente a /home/usuario de cada usuario, pero en el servidor).
2. Modificar el archivo /etc/exports para compartir el directorio anterior con permisos de lectura/escritura para todos los usuarios.
3. Modificar las cuentas de usuario LDAP para indicar que la carpeta donde deben tener su perfil se encuentra dentro de la carpeta que crearemos en el siguiente paso.
4. Crear una carpeta en los equipos cliente para montar los perfiles móviles (el equivalente a /home/usuario de cada usuario en cada cliente).
5. Modificar el archivo /etc/fstab de cada cliente para que monte la carpeta que hemos creado en el paso 1 en el punto de montaje establecido en el paso 4 y reiniciar el equipo.

# LDAP

## CARACTERÍSTICAS

☐ Centralización. Todos los usuarios y grupos se gestionan desde un único punto, lo que reduce errores, simplifica tareas y mejora la trazabilidad. Cualquier cambio en el directorio se refleja inmediatamente en todos los sistemas integrados, sin necesidad de intervención manual en cada equipo.

☐ Escalabilidad es posible añadir réplicas del servidor LDAP, balancear carga, segmentar el directorio por unidades organizativas y adaptar el sistema a nuevas necesidades sin interrumpir el servicio.

☐ Interoperabilidad estándar ampliamente soportado, lo que permite que sistemas muy distintos puedan integrarse en el mismo dominio.

☐ Seguridad, permite aplicar controles de acceso precisos y coherentes. La autenticación centralizada facilita la revocación inmediata de permisos, algo crítico cuando un usuario deja de pertenecer a la organización.

☐ Coherencia de identidad. Cada usuario tiene un único identificador dentro del dominio, lo que evita problemas habituales como duplicidades de UID o inconsistencias en permisos.

## USOS

☐ Gestión centralizada de usuarios en entornos Linux. En aulas, centros educativos o empresas con múltiples equipos, un dominio LDAP permite que el alumnado o el personal utilice las mismas credenciales en cualquier máquina, manteniendo su entorno de trabajo y sus permisos.

☐ Integración de servicios. Servidores web, servicios de bases de datos, plataformas de aprendizaje, sistemas de control de versiones o aplicaciones corporativas pueden delegar la autenticación en LDAP.

☐ En entornos educativos, para unificar el acceso a recursos, como servidores de archivos, escritorios remotos o plataformas virtuales.

☐ En el ámbito empresarial, como base para infraestructuras híbridas, donde conviven distintos sistemas y tecnologías. LDAP actúa como nexo común, permitiendo que la identidad del usuario sea reconocida y validada en todos los servicios de la organización.

# LDAP

## Versiones de LDAP:

☐ LDAPv1: Fue la primera versión del protocolo, pero resultó ser poco práctica debido a limitaciones y falta de características clave. No se utilizó ampliamente en la implementación.

☐ LDAPv2: Esta versión mejoró algunas deficiencias de la versión anterior, pero aún tenía limitaciones significativas en términos de seguridad y capacidades.

☐ LDAPv3: Esta es la versión más ampliamente adoptada y utilizada de LDAP. Introdujo numerosas mejoras y características nuevas, incluyendo:

- Mecanismos de autenticación y seguridad mejorados, como TLS/SSL para cifrado de datos.
- Soporte para búsquedas más flexibles y eficientes.
- Capacidad para manejar datos binarios y Unicode.
- Soporte para extensiones, lo que permitió a los desarrolladores agregar funcionalidad adicional al protocolo.
- Esquema mejorado para describir los tipos de datos y atributos.

LDAP estructura

LDAP utiliza una estructura jerárquica en forma de árbol, conocida como DIT (Directory Information Tree).

☐ En la parte superior del árbol se encuentra la raíz del directorio, que suele corresponder al dominio. Por ejemplo, para el dominio asir.local, la base del directorio podría ser dc=asir, dc=local. A partir de ahí, se crean ramas que organizan la información de forma lógica.

☐ Normalmente, se crean ramas para usuarios, grupos, equipos y otros objetos. Cada entrada del directorio tiene un nombre distinguido o DN, que indica su posición exacta dentro del árbol. Este DN es fundamental para localizar y gestionar los objetos.

Este modelo jerárquico encaja muy bien con la idea de dominio, ya que refleja la estructura organizativa de una empresa o centro educativo. Además, permite aplicar políticas y permisos de forma granular.

![7.png](imagenes/7.png)

# LDAP Identificadores

☐ DC (Domain Component) representa una parte del nombre de dominio. Cada DC corresponde a un nivel del dominio DNS. Por ejemplo, en dc=asir,dc=local, asir y local son componentes del dominio. Los DC suelen utilizarse para definir la base del directorio y su correspondencia con el dominio de red.

☐ DN (Distinguished Name) es el identificador único y completo de un objeto dentro del directorio LDAP. Podría compararse con una ruta absoluta en un sistema de archivos. El DN indica exactamente dónde se encuentra un objeto dentro del DIT. Por ejemplo, un usuario podría tener un DN como cn=juan,ou=usuarios,dc=asir,dc=local. Este DN no solo identifica al usuario, sino también su posición jerárquica dentro del directorio.

☐ CN (Common Name) es el nombre común del objeto dentro de su contexto. En el caso de usuarios, suele ser el nombre del usuario; en el caso de grupos, el nombre del grupo; y en otros objetos, un identificador descriptivo. El CN no tiene por qué ser único en todo el directorio, pero sí debe ser único dentro de su rama inmediata.

☐ Atributos son las propiedades que describen a un objeto dentro del directorio LDAP. Un objeto, por sí solo, no es más que una entrada dentro del DIT; son los atributos los que le dan significado. Por ejemplo, un usuario tendrá atributos como nombre, identificador numérico, grupo principal, shell por defecto o directorio personal.

- Están definidos por los esquemas LDAP, que establecen qué atributos son obligatorios y cuáles son opcionales para cada tipo de objeto. Por ejemplo, un usuario de tipo posixAccount debe tener un UID y un GID, mientras que otros atributos pueden ser opcionales.
- Están asociados directamente al objeto dentro del DIT. No existen de forma independiente.

# LDAP

LDIF (LDAP Data Interchange Format) es un formato de archivo de texto utilizado para representar y transferir datos en sistemas que utilizan el protocolo LDAP.

Estos archivos contienen instrucciones para crear, modificar o eliminar entradas en un directorio. Cada instrucción está escrita en forma de texto plano y sigue una estructura predefinida. Un archivo LDIF puede contener varias instrucciones que describen acciones como agregar nuevos objetos, modificar atributos existentes o eliminar entradas.

Su importancia radica en que permite trabajar con LDAP de forma reproducible y automatizable, algo esencial en entornos profesionales. Gracias a LDIF, es posible versionar cambios, reutilizar configuraciones y aplicar modificaciones de forma controlada.

```txt
dn: cn=John Doe,ou=Usuarios,dc=miempresa,dc=com
objectClass: person
cn: John Doe
sn: Doe
```

```txt
dn: dc=yeraym,dc=asir
objectClass: top
objectClass: dcObject
objectClass: organization
b: yeraym.asir
dc: yeraym
structuralObjectClass: organization
entryUUID: b20ff03c-b561-103b-8b8b-6379bf4b2900
creatorsName: cn=admin,dc=yeraym,dc=asir
createTimestamp: 202109291111102
entryCSN: 20210929111110.477265Z#000000#000#000000
modifiersName: cn=admin,dc=yeraym,dc=asir
modifyTimestamp: 202109291111102
```

```txt
dn: cn=admin,dc=yeraym,dc=asir
objectClass: simpleSecurityObject
objectClass: organizationalRole
cn: admin
Description: LDAP administrator
userPassword:: e|NTSEF946|yeHVMc4|DNUczM2VsY3BLVVRzMndJUGNFRH2vbUY=
structuralObjectClass: organizationalRole
entryUUID: b2109906-b561-103b-8b8c-6379bf4b2900
creatorsName: cn=admin,dc=yeraym,dc=asir
createTimestamp: 202109291111102
entryCSN: 20210929111110.481603Z#000000#000#000000
modifiersName: cn=admin,dc=yeraym,dc=asir
modifyTimestamp: 202109291111102

# LDAP

LDAP es un protocolo, es decir, un conjunto de normas que define cómo se accede a un servicio de directorio, no es un software que se instale. OpenLDAP, en cambio, es una implementación concreta de ese protocolo.

Nadie “instala HTTP”; se instalan servidores web como Apache o Nginx que usan el protocolo HTTP. Con LDAP ocurre exactamente lo mismo. Por tanto, no existe eso de “tener LDAP instalado”. Lo que existe es tener un software que implemente el protocolo LDAP. Cuando alguien dice “tengo LDAP instalado”, en realidad lo que tiene instalado es:

☐ Un servidor LDAP, normalmente OpenLDAP (implementación concreta de ese protocolo)
☐ Y/o clientes LDAP (herramientas que hablan el protocolo LDAP)

![chunk-0-img-7.jpeg](chunk-0-img-7.jpeg)

# OPENLDAP

OpenLDAP es un software de código abierto y multiplataforma que implementa el protocolo LDAP, diseñado para crear y gestionar servicios de directorio. Técnicamente, incluye varios componentes, entre los que destacan:

- ☐ slapd (Stand-alone LDAP Daemon): el demonio o servidor LDAP que escucha peticiones de clientes LDAP y responde según el protocolo.
- ☐ Bibliotecas LDAP: implementaciones de las rutinas necesarias para que aplicaciones y herramientas puedan comunicarse con el servidor.
- ☐ Herramientas cliente: utilidades como ldapsearch, ldapadd, ldapmodify, etc., que permiten consultar y modificar el directorio LDAP desde la línea de comandos mediante archivos ldif.

https://www.openldap.org/

![chunk-0-img-8.jpeg](chunk-0-img-8.jpeg)

# LDAP

|  Comando | Descripción  |
| --- | --- |
|  ldapadd | Agrega un objeto al directorio LDAP.  |
|  ldapmodify | Modifica un objeto en el directorio LDAP. El protocolo permite tres modificaciones diferentes, añadir nuevo valor, reemplazar valor o eliminar valor.  |
|  ldapdelete | Elimina un objeto del directorio LDAP.  |
|  ldapsearch | Busca objetos en el directorio LDAP.  |
|  ldapcompare | Compara los atributos de dos objetos en el directorio LDAP.  |
|  ldapbind | Se conecta a un servidor LDAP.  |
|  ldapunbind | Se desconecta de un servidor LDAP.  |
|  ldappasswd | Cambia la contraseña de un usuario.  |
|  ldapurl | Convierte una URL LDAP en un nombre de objeto LDAP.  |
|  ldapschema | Obtiene el esquema de un servidor LDAP.  |

# INSTALAR Y CONFIGURAR

Instalaremos los siguientes paquetes:

- slapd: Representa el servidor LDAP en el proyecto OpenLDAP.
- ldap-utils: Contiene herramientas de línea de comandos que te permiten interactuar con servidores LDAP, realizar búsquedas, administrar entradas, entre otras operaciones.

dpkg-reconfigure slapd: Cuando ejecutas este comando en la terminal, se abrirá un asistente interactivo que te guiará a través de la configuración del servidor LDAP. Este te hará una serie de preguntas relacionadas con la configuración del servidor, como el nombre del dominio, la contraseña de administrador, el tipo de backend de base de datos (por ejemplo, HDB o BDB), entre otros aspectos.

```
:~# apt install slapd ldap-utils
```

```
:~# dpkg-reconfigure slapd

# INSTALAR Y CONFIGURAR

El archivo de configuración /etc/ldap/ldap.conf permite definir parámetros globales para las interacciones LDAP en el sistema.

☐ BASE: La base de búsqueda predeterminada para las consultas LDAP. (dc)
☐ URI: La URL del servidor LDAP al que se conectará el sistema.

![chunk-0-img-9.jpeg](chunk-0-img-9.jpeg)

# INSTALAR Y CONFIGURAR

El comando slapcat es una herramienta de línea de comandos utilizada en sistemas con OpenLDAP para exportar contenido de la base de datos del servidor LDAP en formato LDIF. Opciones:

☐ -l archivo: Redirige la salida a un archivo en lugar de mostrarla en la consola.

☐ -f archivo: Utiliza un archivo de configuración alternativo.

![chunk-0-img-10.jpeg](chunk-0-img-10.jpeg)

```txt
dn: dc=yeraym,dc=asir
objectClass: top
objectClass: dcObject
objectClass: organization
c: yeraym.asir
dc: yeraym
structuralObjectClass: organization
entryUUID: b20ff03c-b561-103b-8b8b-6379bf4b2900
creatorsName: cn=admin,dc=yeraym,dc=asir
createTimestamp: 20210929111110Z
entryCSN: 20210929111110.4772652#000000#000#000000
modifiersName: cn=admin,dc=yeraym,dc=asir
modifyTimestamp: 20210929111110Z
```

```txt
dn: cn=admin,dc=yeraym,dc=asir
objectClass: simpleSecurityObject
objectClass: organizationalRole
cn: admin
description: LDAP administrator
userPassword:: e1NTSEF9WKUyeHVMcWIDNUczM2VsY3BLVVRzMndJUGNFRH2vbUY=
structuralObjectClass: organizationalRole
entryUUID: b2109906-b561-103b-8b8c-6379bf4b2900
creatorsName: cn=admin,dc=yeraym,dc=asir
createTimestamp: 20210929111110Z
entryCSN: 20210929111110.4816032#000000#000#000000
modifiersName: cn=admin,dc=yeraym,dc=asir
modifyTimestamp: 20210929111110Z

# INSTALAR Y CONFIGURAR

El siguiente paso consistirá en comenzar a incluir contenido. Como cabe esperar, lo primero que debemos hacer es configurar la estructura básica del directorio. Es decir, debemos crear la estructura jerárquica del árbol (DIT – Directory Information Tree). Para este caso es yeraym.asir

![chunk-0-img-11.jpeg](chunk-0-img-11.jpeg)

A continuación, se muestra un archivo LDIF que define la estructura de dos unidades organizativas (OU). Este archivo se utilizará con el comando ldapadd para añadir dichas unidades organizativas a la estructura del dominio dentro del directorio LDAP.

```txt
dn: ou=alumnado,dc=yeraym,dc=asir
objectClass: organizationalUnit
ou: alumnado
dn: ou=profesorado,dc=yeraym,dc=asir
objectClass: organizationalUnit
ou: profesorado

# INSTALAR Y CONFIGURAR

A continuación, añadir la información a la base de datos OpenLDAP. Esto se hace con el comando ldapadd:

- ☐ -x autenticación simple sin SASL (Simple Authentication and Security Layer).
- ☐ -D nombre distinguido con el que nos conectamos a LDAP (ponemos el del administrador).
- ☐ -W pide la contraseña de forma interactiva.
- ☐ -f fichero a cargar. En este caso: estructura base.ldif

```shell
root@yeraymubuser22:~# ldapadd -x -D cn=admin,dc=yeraym,dc=asir -W -f base.ldif
Enter LDAP Password:
adding new entry "ou=alumnado,dc=yeraym,dc=asir"
adding new entry "ou=profesorado,dc=yeraym,dc=asir"
root@yeraymubuser22:~#

# INSTALAR Y CONFIGURAR

A continuación, añadimos algunos usuarios más:

```txt
@n: ou=yeraymalu08,dc=yeraym,dc=asir
@objectClass: inetOrgPerson
@objectClass: posixAccount
@objectClass: shadowAccount
@uid: yeraymalu08
@sn: Moreno
@givenName: Yeray
@gn: yeray01
@displayName: yeray
@uidNumber: 10008
@uidNumber: 10000
@userPassword: 12345
@gecos: yeray
@LoginShell: /bin/bash
@homeDirectory: /home/yeraymalu01
@shadowExpire: -1
@shadowFlag: 0
@shadowWarning: ?
@shadowMin: 8
@shadowMax: 999999
@shadowLastChange: 10877
@mail: yeray.moreno@cifpviliadeaguimes.es
@nostalCode: 35220
@s: asir
@initials: YM
```

```shell
root@yeraymubuser22:"# ldapadd -x -D cn=admin,dc=yeraym,dc=asir -W -f alumnado.ldif
Enter LDAP Password:
adding new entry "ou=yeraymalu01,dc=yeraym,dc=asir"
adding new entry "ou=yeraymalu02,dc=yeraym,dc=asir"
adding new entry "ou=yeraymalu03,dc=yeraym,dc=asir"
adding new entry "ou=yeraymalu04,dc=yeraym,dc=asir"

# INSTALAR Y CONFIGURAR

## Comprobaciones:

```txt
dn: ou=alumnado,dc=yeraym,dc=asir
objectClass: organizationalUnit
ou: alumnado
structuralObjectClass: organizationalUnit
entryUUID: 3387c536-b562-103b-85d3-bb7e73e698b5
creatorsName: cn=admin,dc=yeraym,dc=asir
createTimestamp: 202109291114472
entryCSN: 20210929111447.6886832#000000#000#000000
modifiersName: cn=admin,dc=yeraym,dc=asir
modifyTimestamp: 202109291114472
```

```txt
dn: ou=profesorado,dc=yeraym,dc=asir
objectClass: organizationalUnit
ou: profesorado
structuralObjectClass: organizationalUnit
entryUUID: 33891f80-b562-103b-85d4-bb7e73e698b5
creatorsName: cn=admin,dc=yeraym,dc=asir
createTimestamp: 202109291114472
entryCSN: 20210929111447.6975662#000000#000#000000
modifiersName: cn=admin,dc=yeraym,dc=asir
modifyTimestamp: 202109291114472
```

```txt
dn: ou=yeraymalu01,dc=yeraym,dc=asir
objectClass: inetOrgPerson
objectClass: posixAccount
uid: yeraymalu01
sn: Moreno
cn: yeray01
uidNumber: 10001
homeDirectory: /home/yeraymalu01
structuralObjectClass: inetOrgPerson
ou: yeraymalu01
entryUUID: 2c7a0e1a-b563-103b-85d5-bb7e73e698b5
creatorsName: cn=admin,dc=yeraym,dc=asir
createTimestamp: 202109291121452
gidNumber: 501
entryCSN: 20210929120620.3468452#000000#000#000000
modifiersName: cn=admin,dc=yeraym,dc=asir
modifyTimestamp: 202109291206202
```

```txt
dn: ou=yeraymalu02,dc=yeraym,dc=asir
objectClass: inetOrgPerson
objectClass: posixAccount
uid: yeraymalu02
sn: Moreno
cn: yeray02
uidNumber: 10002
gidNumber: 10000
homeDirectory: /home/yeraymalu02
structuralObjectClass: inetOrgPerson
ou: yeraymalu02
entryUUID: 2c7ba842-b563-103b-85d6-bb7e73e698b5
creatorsName: cn=admin,dc=yeraym,dc=asir
createTimestamp: 202109291121452
entryCSN: 20210929112145.3619952#000000#000#000000
modifiersName: cn=admin,dc=yeraym,dc=asir
modifyTimestamp: 202109291121452

# INSTALAR Y CONFIGURAR

ldapmodify: permite añadir entradas o realizar modificaciones. Posibilita modificar entradas de un directorio LDAP aceptando la introducción de datos a través de un fichero o de la línea de comandos si no se especifica.

```txt
dn: uid=aluyeraym01,ou=asir2,dc=yeraym,dc=asir
changetype: modify
add: mail
mail:yeray.moreno@cifpvilladeaguimes.es
```

```txt
root@yeraymUbuser21:~# ldapmodify -x -D 'cn=admin,dc=yeraym,dc=asir' -W -f modifica.ldif
Enter LDAP Password:
modifying entry "uid=aluyeraym01,ou=asir2,dc=yeraym,dc=asir"
```

# INSTALAR Y CONFIGURAR

ldapsearch: realiza consultas. Por ejemplo, a continuación, se muestran los nombres comunes y los correos de todos los usuarios del dominio:

- `-L` → salida en formato LDIF / `-LL` → elimina comentarios / `-LLL` → elimina también las líneas en blanco
- `-b` → define desde dónde buscar (Base DN)

```txt
root@yeraymUbuser21:"# ldapsearch -xLLL -b "dc=yeraym,dc=asir" "uid=*" cn mail
dn: uid=aluyeraym01,ou=asir2,dc=yeraym,dc=asir
cn: Yeray Asir
mail: yeray.moreno@cifpvilladeaguimes.es
dn: uid=aluyeraym02,ou=asir2,dc=yeraym,dc=asir
cn: Yeray Asir
dn: uid=aluyeraym03,ou=asir1,dc=yeraym,dc=asir
cn: Yeray Asir
dn: uid=aluyeraym04,ou=asir1,dc=yeraym,dc=asir
cn: Yeray Asir

# INSTALAR Y CONFIGURAR

ldapdelete: permite borrar entradas del directorio mediante un fichero o desde línea de comando.

```txt
root@yeraymUbuser21:~# ldapdelete -x -W -D 'cn=admin,dc=yeraym,dc=asir' "uid=aluyeraym04,ou=asir1,dc=yeraym,dc=asir"
Enter LDAP Password:
root@yeraymUbuser21:~# ldapsearch -xLLL -b "dc=yeraym,dc=asir" "uid=*" cn
dn: uid=aluyeraym03,ou=asir1,dc=yeraym,dc=asir
cn: Yeray Asir
dn: uid=aluyeraym01,ou=asir2,dc=yeraym,dc=asir
cn: Yeray Asir
dn: uid=aluyeraym02,ou=asir2,dc=yeraym,dc=asir
cn: Yeray Asir

# INSTALAR Y CONFIGURAR

Para gestionar LDAP mediante una interfaz gráfica, puedes considerar varias opciones de software que facilitan la administración de manera visual:

- Apache Directory Studio: Herramienta de administración LDAP ampliamente utilizada que proporciona una interfaz gráfica intuitiva para administrar directorios LDAP.
- PHPLDAPAdmin: Herramienta basada en web que te permite administrar directorios LDAP a través de una interfaz web.
- JXplorer: Herramienta Java gratuita y de código abierto que proporciona una interfaz gráfica para la administración de directorios LDAP.
- LDAP Account Manager (LAM): Herramienta que ofrece una interfaz web para administrar cuentas y grupos en un directorio LDAP.

![chunk-0-img-12.jpeg](chunk-0-img-12.jpeg)

![chunk-0-img-13.jpeg](chunk-0-img-13.jpeg)

# INSTALAR Y CONFIGURAR

Antes de establecer conexión con el dominio se debe configurar el phpldapadmin. El archivo /etc/phpldapadmin/config.php es el archivo de configuración de phpldapadmin. El método setValue te permite configurar diversas opciones que afectan la forma en que se interactúa con tu servidor LDAP. Algunas opciones que puedes configurar utilizando setValue:

☐ server

- 'name': Nombre del servidor.
- 'host': Dirección IP o nombre del servidor LDAP.
- 'port': Puerto del servidor LDAP.
- 'base': Base DN para búsquedas y operaciones.
- 'tls': Habilitar o deshabilitar TLS/SSL.

![chunk-0-img-14.jpeg](chunk-0-img-14.jpeg)

# INSTALAR Y CONFIGURAR

![chunk-0-img-15.jpeg](chunk-0-img-15.jpeg)

![chunk-0-img-16.jpeg](chunk-0-img-16.jpeg)

# AÑADIR CLIENTE EN OPENLDAP

En Ubuntu, necesitaremos ajustar el comportamiento de los servicios NSS y PAM en cada cliente que debamos configurar. Comenzamos por instalar los siguientes paquetes:

☐ libnss-ldap: Permitirá que NSS obtenga de LDAP información administrativa de los usuarios (Información de las cuentas, de los grupos, información de la máquina, los alias, etc.)
☐ libpam-ldap: Que facilitará la autenticación con LDAP a los usuarios que utilicen PAM.
☐ ldap-utils: Facilita la interacción de LDAP desde cualquier máquina de la red.

root@yeraym:~# apt install libnss-ldap libpam-ldap ldap-utils -y

# AÑADIR CLIENTE EN OPENLDAP

En el primer paso, nos solicita la dirección URi del servidor LDAP. En nuestro caso, escribiremos la dirección IP del servidor y sustituiremos el protocolo ldapi:// por ldap://

![chunk-0-img-17.jpeg](chunk-0-img-17.jpeg)

En el siguiente paso, debemos indicar el nombre global único (Distinguished Name – DN).

Inicialmente aparece en valor dc=example,dc=net pero nosotros lo sustituiremos por dc=nombre,dc=sistemas.

![chunk-0-img-18.jpeg](chunk-0-img-18.jpeg)

# AÑADIR CLIENTE EN OPENLDAP

El asistente nos pide el número de versión del protocolo LDAP que estamos utilizando. De forma predeterminada aparece seleccionada la versión 3.

Indicaremos si las utilidades que utilicen PAM deberán comportarse del mismo modo que cuando cambiamos contraseñas locales. Esto hará que las contraseñas se guarden en un archivo independiente que sólo podrá ser leído por el superusuario.

![chunk-0-img-19.jpeg](chunk-0-img-19.jpeg)

![chunk-0-img-20.jpeg](chunk-0-img-20.jpeg)

# AÑADIR CLIENTE EN OPENLDAP

Después, el sistema nos pregunta si queremos que sea necesario identificarse para realizar consultas en la base de datos de LDAP.

Nombre de la cuenta LDAP que tendrá privilegios para realizar cambios en las contraseñas.

![chunk-0-img-21.jpeg](chunk-0-img-21.jpeg)

![chunk-0-img-22.jpeg](chunk-0-img-22.jpeg)

# AÑADIR CLIENTE EN OPENLDAP

La contraseña que usará la cuenta (como siempre, habrá que escribirla por duplicado para evitar errores tipográficos). Deberá coincidir con la que escribimos en el apartado Instalar OpenLDAP en el servidor.

![chunk-0-img-23.jpeg](chunk-0-img-23.jpeg)

Para completar la tarea, deberemos cambiar algunos parámetros en los archivos de configuración del cliente. En concreto, deberemos editar:

- /etc/nsswitch.conf
- /etc/pam.d/common-password
- /etc/pam.d/common-session.

# AÑADIR CLIENTE EN OPENLDAP

/etc/nsswitch.conf

Se incluyen las fuentes desde las que se obtiene la información del servicio de nombres en diferentes categorías y en qué orden. Cada categoría de información se identifica bajo un nombre.

☐ Localizamos las líneas que comienzan por passwd, group y shadow y les añadimos el texto ldap, para indicar el nuevo origen para autenticar las cuentas.

![chunk-0-img-24.jpeg](chunk-0-img-24.jpeg)

# AÑADIR CLIENTE EN OPENLDAP

/etc/pam.d/common-password

Proporciona un conjunto común de reglas PAM para la comprobación de contraseñas. En particular, la opción use_authtok, que impide utilizar un segundo método de autenticación cuando ya ha sido aplicado otro anterior, aunque éste haya sido insatisfactorio.

☐ Deberemos eliminar la opción use_authtok

![chunk-0-img-25.jpeg](chunk-0-img-25.jpeg)

# AÑADIR CLIENTE EN OPENLDAP

## /etc/pam.d/common-sesión

Ofrece un conjunto de reglas PAM para el inicio de sesión, tanto si éste es interactivo como si es no interactivo. Aquí será donde indiquemos que se debe crear un directorio home durante el primer inicio de sesión, también para los usuarios autenticados mediante LDAP.

☐ Este comportamiento lo conseguiremos añadiendo al final del archivo la siguiente línea:

session optional pam_mkhomedir.so skel=/etc/skel umask=077

```html
|  EN | root@yeraymc -  |
| --- | --- |
|  GNU nano 4.8 | /etc/pam.d/common-session  |
```

```txt
# /etc/pam.d/common-session - session-related modules common to all services
# This file is included from other service-specific PAM config files,
# and should contain a list of modules that define tasks to be performed
# at the start and end of sessions of "any" kind (both interactive and
# non-interactive).
# As of pam 1.0.1-6, this file is managed by pam-auth-update by default.
# To take advantage of this, it is recommended that you configure any
# local modules either before or after the default block, and use
# pam-auth-update to manage selection of other modules. See
# pam-auth-update(8) for details.
# here are the per-package modules (the "Primary" block)
# session [default=1] pam_permit.so
# here's the fallback if no module succeeds
# session requisite pam_deny.so
# prime the stack with a positive return value if there isn't one already;
# this avoids us returning an error just because nothing sets a success code
# since the modules above will each just jump around
# session required pam_permit.so
# The pam_umask module will set the umask according to the system default in
# /etc/login.defs and user settings, solving the problem of different
# umask settings with different shells, display managers, remote sessions etc.
# See "man pam_umask".
# session optional pam_umask.so
# and here are more per-package modules (the "Additional" block)
# session required pam_unix.so
# session optional pam_ldap.so
# session optional pam_systemd.so
# session optional pam_mkhomedir.so skel=/etc/skel umask=077
# end of pam-auth-update config

# AÑADIR CLIENTE EN OPENLDAP

Una vez terminada la instalación, ya podemos activar el servicio libnss-ldap.

![chunk-0-img-26.jpeg](chunk-0-img-26.jpeg)

La forma más sencilla de comprobar que podemos iniciar sesión en el servidor usando LDAP consiste en arrancar el sistema en modo texto (o arrancarlo en modo gráfico y usar la combinación de teclas alt + ctrl + f1 para ir a una consola de texto) y escribir las credenciales de un usuario LDAP.

```bash
root@yeraym:-\# systemctl start libnss-ldap
root@yeraym:-\# systemctl status libnss-ldap
@libnss-ldap.service - LSB: Updates /etc/ldap.conf
Loaded: loaded (/etc/lnlt.d/libnss-ldap; generated)
Active: active (exited) since Tue 2021-09-28 12:07:52 WEST; 8s ago
Docs: man:systemd-sysv-generator(8)
Process: 3617 ExecStart=/etc/lnlt.d/libnss-ldap start (code=exited, status=0/SUCCESS)
sep 28 12:07:52 yeraym systemd[1]: Starting LSB: Updates /etc/ldap.conf...
sep 28 12:07:52 yeraym systemd[1]: Started LSB: Updates /etc/ldap.conf.
root@yeraym:~m
```

```bash
yerayma1u08@yeraym:/$ whoami
yerayma1u08
yerayma1u08@yeraym:/$ _
```

#

# AÑADIR CLIENTE EN OPENLDAP

La mayoría de las veces necesitarás que los clientes puedan iniciar sesión en la interfaz gráfica. El problema es que el gestor de sesiones que utiliza Ubuntu es LightDM. Este es quien se encarga de presentarnos la pantalla de autenticación y, en el caso de LightDM, sólo incluye en la lista aquellos usuarios que ya conoce.

Si necesitas que muchos usuarios puedan iniciar sesión en un determinado cliente, quizás la solución más sencilla consista en obligar a LightDM a preguntar por el nombre de la cuenta, antes de pedir la contraseña. Para hacer esto, debemos editar el archivo /usr/share/lightdm/lightdm.conf.d/50-ubuntu.conf

![chunk-0-img-27.jpeg](chunk-0-img-27.jpeg)

# DOMINIOS SAMBA

Un dominio Samba permite a un servidor Linux actuar como elemento central de autenticación y gestión de recursos en una red, ofreciendo compatibilidad con entornos Windows. Samba implementa los protocolos necesarios para que equipos Windows y Linux puedan compartir usuarios, grupos, archivos y otros servicios de forma integrada. En este contexto, Samba no se limita a la compartición de recursos, sino que puede asumir el rol de controlador de dominio, gestionando identidades y accesos de manera centralizada.

A partir de Samba 4, el servicio incorpora una implementación completa de Active Directory, integrando servicios como LDAP, Kerberos y DNS dentro de una misma infraestructura. Esto permite definir un dominio en el que los usuarios y equipos se autentican contra un único punto, utilizando credenciales comunes independientemente del sistema operativo.

El uso de dominios Samba es habitual en entornos corporativos donde se requiere compatibilidad con clientes Windows sin depender exclusivamente de servidores Windows. Samba permite desplegar dominios funcionales sobre Linux, manteniendo control centralizado sobre usuarios y recursos, y ofreciendo una solución flexible, escalable y alineada con estándares ampliamente utilizados en redes empresariales.

# DOMINIOS SAMBA

Procedimiento para implementar un Controlador de dominio en Ubuntu, donde los equipos clientes con Windows puedan iniciar sesión y utilizar sus recursos sin notar la diferencia con un servidor Windows Server. Antes de comenzar tendremos en cuenta las siguientes configuraciones bases y prepararemos el servidor para seguir con la instalación de paquetes

☐ Nombre del controlador de dominio de Active Directory: nombre-dc-smb
☐ Nombre DNS del dominio de Active Directory: nombre.sistemas
☐ Nombre del Reino Kerberos: nombre.sistemas
☐ Nombre NetBIOS del dominio: nombre
☐ Dirección IP fija del servidor: 172.16.31.200
☐ Rol del servidor: Domain Controller (DC)
☐ Reenviador DNS:172.16.31.1

![chunk-0-img-28.jpeg](chunk-0-img-28.jpeg)

![chunk-0-img-29.jpeg](chunk-0-img-29.jpeg)

# DOMINIOS SAMBA

Actualizar el sistema

```txt
root@yeray-dc-smb:"# apt update 88 apt upgrade -y
obj:1 http://es.archive.ubuntu.com/ubuntu jammy InRelease
Res:2 http://es.archive.ubuntu.com/ubuntu jammy-updates InRelease [114 kB]
Res:3 http://es.archive.ubuntu.com/ubuntu jammy-backports InRelease [99,8 kB]
Res:4 http://es.archive.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
Res:5 http://es.archive.ubuntu.com/ubuntu jammy-updates/main amd64 Packages [726 kB]
Res:6 http://es.archive.ubuntu.com/ubuntu jammy-updates/restricted amd64 Packages [445 kB]
Res:7 http://es.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 Packages [756 kB]
descargados 2.251 kB en 5s (429 kB/s)
leyendo lista de paquetes... Hecho
creando árbol de dependencias... Hecho
leyendo la información de estado... Hecho
todos los paquetes están actualizados.
leyendo lista de paquetes... Hecho
creando árbol de dependencias... Hecho
leyendo la información de estado... Hecho
calculando la actualización... Hecho
#
# News about significant security updates, features and services will
# appear here to raise awareness and perhaps tease /r/Linux ;)
# Use 'pro config set apt_news=false' to hide this and future APT news.
#
# actualizados, # nuevos de Instalar@u, # para eliminar o # no actualizados.
```

Establecer un nombre adecuado para el servidor:

nombre-dc-smb

```txt
root@yeray-dc-smb:"# hostnamect1
Static hostname: yeray-dc-smb
Icon name: computer-vm
Chassis: vm
Machine ID: 5fb0d3de3b444eb97f04de29acea3cd
Boot ID: 814254c13bb14c90a6d74475bfefcd3
Virtualization: oracle
Operating System: Ubuntu 22.04.1 LTS
Kernel: Linux 5.15.0-53-generic
Architecture: x86-64
Hardware Vendor: innotek GmbH
Hardware Model: VirtualBox
root@yeray-dc-smb:"# cat /etc/hosts
$27.0.0.1 localhost
$27.0.1.1 yeray-dc-smb.yeray.sistemas yeray-dc-smb
$72.16.31.200 yeray-dc-smb.yeray.sistemas yeray-dc-smb
# The following lines are desirable for IPv6 capable hosts
::1 ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
root@yeray-dc-smb:"#
```

# DOMINIOS SAMBA

Lo siguiente será configurar las características de red según las necesidades del servidor. Recuerda que un servidor debe tener IP fija.

![chunk-0-img-30.jpeg](chunk-0-img-30.jpeg)

Necesitaremos disponer de los siguientes paquetes preinstalados antes de comenzar con el proceso de configuración, aunque todos ellos están en los repositorios:

☐ samba: servidor de archivos, impresión y autenticación para SMB/CIFS.
☐ smbclient: clientes de línea de comandos para SMB/CIFS.
☐ krb5-config: Archivos de configuración para Kerberos Version 5.
☐ winbind: Servicio para resolver información sobre usuarios y grupos de servidores Windows NT.

![chunk-0-img-31.jpeg](chunk-0-img-31.jpeg)

# DOMINIOS SAMBA

☐ En la instalación de Kerberos, nos preguntará por el reino (realm), se refiere al nombre del dominio:

☐ Después nos solicita el nombre de los servidores de Kerberos para nuestro reino. En este caso, se refiere a los controladores de dominio que tengamos definidos. En nuestro ejemplo sólo tenemos uno:

☐ Por último, nos solicita el servidor administrativo para nuestro reino de Kerberos, como sólo tenemos uno:

![chunk-0-img-32.jpeg](chunk-0-img-32.jpeg)

# CONFIGURAR SAMBA

Debemos cambiar de nombre el archivo smb.conf que contiene la configuración predeterminada, para evitar que Samba intente usarlo durante el proceso de configuración y estar seguros de que todos los datos del nuevo archivo de configuración se producen desde cero. Además, también podremos recuperarlo en caso de que algo salga mal.

```
root@yeray-dc-smb:~# mv /etc/samba/smb.conf /etc/samba/smb.conf.old
root@yeray-dc-smb:~#
```

Ahora ya estamos listos para promover nuestro equipo como controlador de un dominio Samba 4 que actúe como un reemplazo completo de un servidor de dominio de Active Directory.

# CONFIGURAR SAMBA

Para promover nuestro equipo como controlador de un dominio lograrlo, usaremos el comando **samba-tool domain provision**, y lo haremos de forma interactiva, para que sea el propio comando el que nos sugiera sus valores predeterminados. Así, si éstos coinciden con los que nosotros esperamos, será muy probable que los pasos anteriores hayan sido los correctos.

```text
root@yeray-dc-smb:"# samba-tool domain provision
Realm [YERAY.SISTEMAS]:
Domain [YERAY]:
Server Role (dc, member, standalone) [dc]:
DNS backend (SAMBA_INTERNAL, BIND9_FLATFILE, BIND9_DLZ, NONE) [SAMBA_INTERNAL]:
DNS forwarder IP address (write 'none' to disable forwarding) [127.0.0.53]:
Administrator password:
Retype password:
```

```text
INFO 2022-11-22 12:55:03,855 pid:3055 /usr/lib/python3/dist-packages/samba/provision/_init_.py #4
:: Server Role: active directory domain controller
INFO 2022-11-22 12:55:03,855 pid:3055 /usr/lib/python3/dist-packages/samba/provision/_init_.py #4
#: Hostname: yeray-dc-smb
INFO 2022-11-22 12:55:03,855 pid:3055 /usr/lib/python3/dist-packages/samba/provision/_init_.py #4
#: NetBIOS Domain: YERAY
INFO 2022-11-22 12:55:03,855 pid:3055 /usr/lib/python3/dist-packages/samba/provision/_init_.py #4
#: DNS Domain: yeray.sistemas
INFO 2022-11-22 12:55:03,855 pid:3055 /usr/lib/python3/dist-packages/samba/provision/_init_.py #4
#: DOMAIN SID: S-1-5-21-351225152-702372258-301823131
root@yeray-dc-smb:"#

# CONFIGURAR SAMBA

Con el anterior comando se generó el archivo de configuración de Kerberos en la ruta /var/lib/samba/private/krb5.conf. Solo tenemos que copiarlo a la ubicación adecuada.

Seguimos ajustando la resolución de nombres, y comenzaremos deteniendo los servicios implicados y deshabilitándolos para que no vuelvan a iniciarse si reiniciamos el equipo.

```bash
#qeray-dc-smb:~# cp /var/lib/samba/private/krb5.conf /etc
#qeray-dc-smb:~#
```

```bash
#qeray-dc-smb:~# systemctl stop smbd nmbd winbind systemd-resolved
#qeray-dc-smb:~# systemctl disable smbd nmbd winbind systemd-resolved
#qeray-dc-smb:~# systemctl stop smbd nmbd winbind systemd-resolved
#qeray-dc-smb:~# /lib/systemd/systemd-sysv-install disable smbd
#qeray-dc-smb:~# /lib/systemd/systemd-sysv-install disable nmbd
#qeray-dc-smb:~# yuchronizing state of nmbd.service with SysV service script with /lib/systemd/systemd-sysv-install
#qeray-dc-smb:~# executing /lib/systemd/systemd-sysv-install disable nmbd
#qeray-dc-smb:~# yuchronizing state of winbind.service with SysV service script with /lib/systemd/systemd-sysv-install
#:

# CONFIGURAR SAMBA

Aseguramos de que el servicio **samba-ad-dc** se podrá iniciar sin dificultades, evitando cualquier enmascaramiento que pueda existir y eliminamos el archivo **resolv.conf** que, en realidad, será un enlace a `../run/systemd/resolve/stub-resolv.conf` y generamos uno nuevo con escribiremos los valores adecuados para nuestro dominio y por último activamos el servicio:

```shell
root@yeray-dc-smb:~# systemctl unmask samba-ad-dc
Removed /etc/systemd/system/samba-ad-dc.service.
root@yeray-dc-smb:~# ls -l /etc/resolv.conf
Inuxrwxrwx 1 root root 39 ago 9 12:56 /etc/resolv.conf -&gt; ../run/systemd/resolve/stub-resolv.conf
root@yeray-dc-smb:~# rm /etc/resolv.conf
root@yeray-dc-smb:~# nano_/etc/resolv.conf
```

```txt
GNU nano 6.2 /etc/resolv.conf *
domain yeray.sistemas
nameserver 127.0.0.1_
```

```shell
root@yeray-dc-smb:~# systemctl start samba-ad-dc
root@yeray-dc-smb:~# systemctl enable samba-ad-dc
Synchronizing state of samba-ad-dc.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable samba-ad-dc
Created symlink /etc/systemd/system/multi-user.target.wants/samba-ad-dc.service + /lib/systemd/system/samba-ad-dc.service.
root@yeray-dc-smb:~#
```

# COMPROBAR DOMINIO SAMBA

Una vez finalizada la instalación vamos a proceder a la comprobación del dominio de Samba realizando las siguientes tareas:

1. Consultar el nivel del dominio y crear un nuevo usuario.
2. Confirmar que el servidor DNS funciona de forma adecuada
3. Comprobar el funcionamiento de Kerberos
4. Comprobación de inicio de sesión en el servidor y su integridad
5. Añadir un equipo con Windows al dominio y comprobar en servidor.

# COMPROBAR DOMINIO SAMBA

☐ El comando samba-tool domain level show te permite verificar el nivel funcional del dominio en tu controlador de dominio Samba. En este caso equivale a una instalación Windows Server 2008 R2.

☐ A continuación, crearemos un nuevo usuario en el dominio llamado nombre01.

```txt
root@yeray-dc-smb:~# samba-tool domain level show
Domain and forest function level for domain 'DC=yeray,DC=sistemas'
Forest function level: (Windows) 2008 R2
Domain function level: (Windows) 2008 R2
Lowest function level of a DC: (Windows) 2008 R2
root@yeray-dc-smb:~# samba-tool user create yeray01
New Password:
Retype Password:
User 'yeray01' added successfully
root@yeray-dc-smb:~#

# COMPROBAR DOMINIO SAMBA

Comprobaciones del servidor DNS interno del propio Samba, mas concretamente de los registros SRV encargados de almacenar, dentro de la base de datos DNS, la relación entre el nombre de un servicio y el nombre DNS del ordenador que ofrece dicho servicio.

☐ Comprobar el servicio LDAP sobre el protocolo TCP.
☐ Comprobar el registro SRV para el protocolo Kerberos sobre UDP.
☐ Comprobar la resolución del nombre de nuestro servidor.

```
root@yeray-dc-smb:~# host -t SRV _ldap._tcp.yeray.sistemas
_ldap._tcp.yeray.sistemas has SRV record 0 100 389 yeray-dc-smb.yeray.sistemas.
root@yeray-dc-smb:~# host -t SRV _kerberos._udp.yeray.sistemas
_kerberos._udp.yeray.sistemas has SRV record 0 100 88 yeray-dc-smb.yeray.sistemas.
root@yeray-dc-smb:~# host -t A yeray-dc-smb.yeray.sistemas
yeray-dc-smb.yeray.sistemas has address 172.16.31.200

# COMPROBAR DOMINIO SAMBA

Ahora nos aseguramos de que se resuelven correctamente los nombres y las IPs del dominio. Al ejecutar nslookup sin argumentos, aparece un signo ‘mayor que’ a modo de prompt, donde podemos ir escribiendo argumentos.

```txt
root@yeray-dc-smb:^# nslookup
&gt; server 172.16.31.200
Default server: 172.16.31.200
Address: 172.16.31.200#53
&gt; set type=SRV
&gt; _ldap._tcp.yeray.sistemas
Server: 172.16.31.200
Address: 172.16.31.200#53
_ldap._tcp.yeray.sistemas service = 0 100 389 yeray-dc-smb.yeray.sistemas.
&gt; exit

# COMPROBAR DOMINIO SAMBA

Para comprobar el funcionamiento de Kerberos podemos usar el comando smbclient para comprobar los servicios que puede obtener un determinado usuario.

```bash
root@yeray-dc-smb:~# smbclient -L yeray-dc-smb.yeray.sistemas -U 'administrator'
Password for [YERAY\administrator]:

# COMPROBAR DOMINIO SAMBA

Comprobar el inicio de sesión en el servidor verificando la integridad del archivo de configuración de Samba con el comando testparm

```bash
root@yeray-dc-smb:~# smbclient //localhost/netlogon -U 'administrator'
Password for [YERAY\administrator]:
Try "help" to get a list of possible commands.
smb: \&gt; ls
. D 0 Tue Nov 22 12:54:20 2022
. D 0 Tue Nov 22 12:54:22 2022
50254368 blocks of size 1024. 39725420 blocks available
smb: \&gt; exit
root@yeray-dc-smb:~# testparm
Load smb config files from /etc/samba/smb.conf
Loaded services file OK.
Weak crypto is allowed
Server role: ROLE_ACTIVE_DIRECTORY_DC
Press enter to see a dump of your service definitions

# COMPROBAR DOMINIO SAMBA

![chunk-0-img-33.jpeg](chunk-0-img-33.jpeg)

![chunk-0-img-34.jpeg](chunk-0-img-34.jpeg)

![chunk-0-img-35.jpeg](chunk-0-img-35.jpeg)



