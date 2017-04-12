---
title: "Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes) | Microsoft Docs"
description: "En este documento se explica cómo establecer una estrategia de alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f95e944fc3ec3a831d97386443eb644420ae54dc
ms.lasthandoff: 04/03/2017


---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes) 

Los conceptos de alta disponibilidad y recuperación ante desastres son aspectos importantes de la ejecución de servidores importantes de SAP HANA en Azure (instancias grandes). Es importante trabajar con SAP, el integrador del sistema, o Microsoft para diseñar e implementar correctamente la estrategia correcta de alta disponibilidad y recuperación ante desastres. También resulta fundamental tener en cuenta los objetivos de punto de recuperación y de tiempo de recuperación, que son específicos del entorno.

## <a name="high-availability"></a>Alta disponibilidad

Microsoft admite los métodos estándar de alta disponibilidad de SAP HANA, entre los que se encuentran los siguientes:

- **Replicación de almacenamiento**: la capacidad que tiene el propio sistema de almacenamiento para replicar todos los datos a otra ubicación (del mismo centro de datos o uno independiente). SAP HANA funciona de forma independiente con respecto a este método.
- **Replicación del sistema de HANA**: la replicación de todos los datos de SAP HANA a un sistema SAP HANA independiente. El objetivo de tiempo de recuperación se minimiza a través de la replicación de datos a intervalos regulares. SAP HANA admite los modos asincrónico, sincrónico en memoria y sincrónico (se recomiendan solo para sistemas de SAP HANA que estén en el mismo centro de datos o a menos de 100 km de este). En el diseño actual de las marcas de instancias grandes de HANA, la replicación del sistema de HANA solo puede usarse para lograr alta disponibilidad.
- **Conmutación por error automática del host**: una solución de recuperación de errores local que se usará como alternativa a la replicación del sistema. Cuando el nodo maestro deja de estar disponible, uno o varios nodos en espera de SAP HANA están configurados en el modo de escalado horizontal y SAP HANA automáticamente conmuta por error a otro nodo.

Para obtener más información sobre la alta disponibilidad de SAP HANA, consulte la siguiente información de SAP:

- [SAP HANA High-Availability Whitepaper](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html) (Documento técnico sobre la alta disponibilidad de SAP HANA)
- [SAP HANA Administration Guide](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf) (Guía de administración de SAP HANA)
- [Vídeo de SAP sobre la replicación del sistema de SAP HANA](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Nota de soporte técnico de SAP 1999880: preguntas más frecuentes sobre la replicación del sistema de SAP HANA](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [Nota de soporte técnico de SAP 2165547: copia de seguridad y restauración de SAP HANA en el entorno de replicación del sistema de SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [Nota de soporte técnico de SAP 1984882: uso de la replicación del sistema de SAP HANA para el cambio de hardware con el tiempo de inactividad mínimo o cero](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="disaster-recovery"></a>Recuperación ante desastres

SAP HANA en Azure (instancias grandes) se ofrece en dos regiones de Azure de una región geopolítica. Entre las dos marcas de instancias grandes de dos regiones diferentes hay una conectividad de red directa para replicar los datos durante la recuperación ante desastres. La replicación de los datos se basa en la infraestructura del almacenamiento y no se realiza de forma predeterminada. El proceso se lleva a cabo para las configuraciones de clientes que solicitaron la funcionalidad de recuperación ante desastres. En el diseño actual, no se puede usar la replicación del sistema de HANA para realizar la recuperación ante desastres.

Sin embargo, para sacar provecho de la recuperación ante desastres, es preciso empezar a diseñar la conectividad de red a las dos regiones de Azure diferentes. Para ello, necesita un circuito ExpressRoute que se conecte desde el entorno local de su región de Azure principal y otro que se conecte desde el entorno local a su región de recuperación ante desastres. Esta medida bastaría para cubrir una situación en la que hay un problema en toda una región de Azure, incluida la ubicación del enrutador perimetral de Microsoft Enterprise (MSEE).

Como segunda medida, puede conectar todas las redes virtuales de Azure que se conectan a SAP HANA en Azure (instancias grandes) en una de las regiones a los dos circuitos ExpressRoute. Así se soluciona el caso de que solo una de las ubicaciones de MSEE que conecta la ubicación local con Azure se quede fuera de servicio.

En la siguiente figura se muestra la configuración óptima para la recuperación ante desastres:

![Configuración óptima para la recuperación ante desastres](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)

El caso óptimo para una configuración de recuperación ante desastres de la red consiste en tener dos circuitos ExpressRoute que vayan desde la ubicación local a las dos regiones de Azure. Un circuito va a la región 1, donde se ejecuta una instancia de producción. El segundo circuito ExpressRoute va a la región 2, donde se ejecutan algunas instancias de HANA que no son de producción (esto es importante en el caso de que una región de Azure entera, incluidos el MSEE y la marca de instancias grandes, quede fuera de la cuadrícula).

Como segunda medida, las distintas redes virtuales se conectan a los diferentes circuitos ExpressRoute que están conectados a SAP HANA en Azure (instancias grandes). Como se verá más adelante, puede pasar por alto la ubicación en la que se produce el error de MSEE, o bien reducir el objetivo de punto de recuperación ante desastres.

Estos son los requisitos para una configuración de recuperación ante desastres:

- Las SKU de SAP HANA en Azure (instancias grandes) deben tener el mismo tamaño que las SKU de producción y estar implementadas en la región de recuperación ante desastres. Estas instancias se pueden utilizar para realizar pruebas, espacios aislados o instancias de QA de HANA.
- Además, debe solicitar un perfil de recuperación ante desastres para cada una de las SKU de SAP HANA en Azure (instancias grandes) que desea recuperar en el sitio de recuperación ante desastres, en caso de que sea necesario. Esta acción provoca la asignación de volúmenes de almacenamiento, que son el destino de la replicación de almacenamiento de la región de producción a la región de recuperación ante desastres.

Cuando cumpla los requisitos anteriores, será su responsabilidad iniciar la replicación de almacenamiento. En la infraestructura de almacenamiento usada para SAP HANA en Azure (instancias grandes), la base de la replicación del almacenamiento son las instantáneas del almacenamiento. Para iniciar la replicación de recuperación ante desastres, debe realizar las siguientes tareas:

- Una instantánea del LUN de arranque que se describió anteriormente.
- Una instantánea de los volúmenes relacionados con HANA que se describieron anteriormente.

Después de la ejecución de estas instantáneas, se inicializa una réplica inicial de los volúmenes en aquellos asociados a su perfil de recuperación ante desastres de la región de la recuperación ante desastres.

Posteriormente, la instantánea de almacenamiento más reciente se usa cada hora para replicar los valores diferenciales que se desarrollan en los volúmenes de almacenamiento.

El objetivo de punto de recuperación que se logra con esta configuración es de 60 a 90 minutos. Para conseguir uno mejor en el caso de la recuperación ante desastres, copie las copias de seguridad del registro de transacciones de HAN fuera de SAP HANA en Azure (instancias grandes) y en la otra región de Azure. Para lograr este objetivo de punto de recuperación, haga lo siguiente:

1. Realizar una copia de seguridad del registro de transacciones de HANA tan frecuentemente como sea posible en /hana/log/backup.
2. Copie las copias de seguridad del registro de transacciones, cuando hayan terminado, en una máquina virtual de Azure, que es una red virtual conectada al servidor de SAP HANA en Azure (instancias grandes).
3. En esa máquina virtual, copie la copia de seguridad en una máquina virtual que se encuentre en una red virtual de la región de recuperación ante desastres.
4. Mantener las copias de seguridad del registro de transacciones de dicha región en la máquina virtual.

En caso de desastre, una vez que el perfil de recuperación ante desastres se haya implementado en un servidor real, copie las copias de seguridad del registro de transacciones de la máquina virtual al SAP HANA en Azure (instancias grandes) que es ahora la instancia principal en la región de recuperación ante desastres y restaurar dichas copias de seguridad. Esto es posible porque el estado de HANA en los discos de recuperación ante desastres es la de una instantánea de HANA. Se trata del punto de desplazamiento de las restauraciones posteriores de las copias de seguridad del registro de transacciones.

## <a name="backup-and-restore"></a>Copia de seguridad y restauración

Uno de los aspectos más importantes para el funcionamiento de las bases de datos es asegurarse de que la base de datos se puede proteger de varios incidentes catastróficos. Estos eventos pueden deberse a cualquier cosa, desde desastres naturales a simples errores del usuario.

La copia de seguridad de una base de datos, con la capacidad de realizar la restauración a cualquier momento dado (como antes de que alguien eliminara datos críticos), permite la restauración a un estado que sea lo más parecido posible al que había antes de que se produjera la interrupción.

Para que el resultado sea óptimo, es preciso realizar dos tipos de copias de seguridad:

- Copias de seguridad de bases de datos
- Copias de seguridad del registro de transacciones

Además de las copias de seguridad completas de la base de datos que se realizan a nivel de aplicación, se puede ser más exhaustivo, para lo que se deben realizar las copias de seguridad con instantáneas de almacenamiento. La realización de copias de seguridad del registro también es importante para restaurar la base de datos (y vaciar los registros de las transacciones confirmadas).

SAP HANA en Azure (instancias grandes) ofrece dos opciones de copia de seguridad y restauración:

- Opción personal (DIY, por las siglas de Do It Yourself). Después de calcular el espacio en disco y asegurarse de hay suficiente, realice copias de seguridad completas del registro y las bases de datos mediante los métodos de copia de seguridad de disco (para dichos discos). Con el tiempo, las copias de seguridad se copian en una cuenta de Azure Storage (después de configurar un servidor de archivos basado en Azure con un almacenamiento estándar prácticamente ilimitado) o se usan el almacén de Azure Backup o el almacenamiento en frío de Azure. Otra opción consiste en usar una herramienta de protección de datos de terceros, como Commvault, para almacenar las copias de seguridad una vez que se copian en una cuenta de almacenamiento. Esta opción de copia de seguridad DIY también podría ser necesaria para aquellos datos que tengan que almacenarse durante periodos más largos con fines de conformidad y auditoría.
- Use la funcionalidad de copia de seguridad y restauración que proporciona la infraestructura subyacente de SAP HANA en Azure (instancias grandes). Esta opción cubre la necesidad de copias de seguridad y hace que las copias de seguridad manuales sean casi obsoletas (excepto en los casos en que las copias de seguridad de datos sean necesarias por motivos de cumplimiento). En el resto de esta sección se trata la funcionalidad de copia de seguridad y restauración que se ofrece con las instancias grandes de HANA.

> [!NOTE]
> La tecnología de instantáneas que utiliza la infraestructura subyacente de HANA (instancias grandes) depende de las instantáneas de SAP HANA. Las instantáneas de SAP HANA no funcionan con los contenedores de base de datos multiinquilino de SAP HANA. Como resultado, este método de copia de seguridad no puede usarse para implementar contenedores de base de datos multiinquilino de SAP HANA.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Uso de instantáneas de almacenamiento de SAP HANA en Azure (instancias grandes)

La infraestructura de almacenamiento subyacente de SAP HANA en Azure (instancias grandes) admite la noción de una instantánea de almacenamiento de volúmenes. Se admiten tanto la copia de seguridad como la restauración de un volumen concreto, pero es preciso tener en cuenta lo siguiente:

- En lugar de las copias de seguridad de bases de datos, se toman instantáneas del volumen de almacenamiento con frecuencia.
- La instantánea de almacenamiento inicia una instantánea de SAP HANA antes de que se ejecuta la instantánea de almacenamiento. Esta instantánea de SAP HANA es el punto de configuración de las posibles restauraciones del registro después de la recuperación de la instantánea de almacenamiento.
- En el momento en que la instantánea de almacenamiento se ejecute correctamente se eliminará la instantánea de SAP HANA.
- Las copias de seguridad del registro se realizan con frecuencia y se almacenan en el volumen de copia de seguridad del registro o en Azure.
- Si la base de datos se debe restaurar a un momento dado, se realiza una solicitud al soporte técnico de Microsoft Azure (interrupción de producción) o a SAP HANA en Azure Service Management para que realicen la restauración a una instantánea de almacenamiento concreta (por ejemplo, una restauración planeada de un sistema de espacio aislado a su estado original).
- La instantánea de SAP HANA que se incluye en la instantánea de almacenamiento es un punto de desplazamiento para aplicar las copias de seguridad del registro que se han ejecutado y almacenado después de que se tomara la instantánea de almacenamiento.
- Estas copias de seguridad del registro se realizan para restaurar la base de datos a un momento dado.

Al especificar backup\_name, se crea una instantánea de los siguientes volúmenes:

- hana/data
- hana/log
- hana/log\_backup (montada como copia de seguridad en hana/log)
- hana/shared

### <a name="storage-snapshot-considerations"></a>Consideraciones de las instantáneas de almacenamiento

>[!NOTE]
>Las instantáneas de almacenamiento _no_ se proporcionan de forma gratuita, ya que se debe asignar espacio de almacenamiento adicional.

Mecánica concreta de las instantáneas de almacenamiento de SAP HANA en Azure (instancias grandes):

- Una instantánea de almacenamiento concreta (en el momento en que se toma) consume muy poco espacio de almacenamiento.
- Cuando el contenido de datos cambia y el contenido de los archivos de datos de SAP HANA cambia en el volumen de almacenamiento, la instantánea debe almacenar el contenido del bloque original.
- La instantánea de almacenamiento aumenta de tamaño. Cuanto más tiempo dure la instantánea, mayor será la instantánea de almacenamiento.
- Cuantos más cambios se realicen en el volumen de la base de datos de SAP HANA a lo largo de la vigencia de una instantánea de almacenamiento, mayor será el consumo de espacio de dicha instantánea.

SAP HANA en Azure (instancias grandes) incluye tamaños de volumen fijos para el volumen de datos y de registro de SAP HANA. La realización de instantáneas de dichos volúmenes acabará con el espacio del volumen, por lo que es responsabilidad suya programar las instantáneas de almacenamiento (en el proceso de instancias grandes de SAP HANA en Azure).

En las secciones siguientes se proporciona información para realizar estas instantáneas, donde también se incluyen recomendaciones generales:

- Aunque el hardware puede admitir 255 instantáneas por volumen, se recomienda encarecidamente no acercarse a este número.
- Antes de realizar instantáneas de almacenamiento, supervise y realice un seguimiento del espacio disponible.
- Reduzca el número de instantáneas del almacenamiento en función del espacio libre. Es posible que deba reducir el número de instantáneas que mantiene, o bien que tenga que ampliar los volúmenes (puede solicitar más almacenamiento en unidades de 1 TB).
- Durante actividades como mover datos a SAP HANA con herramientas de migración del sistema (con R3load o mediante la restauración de bases de datos de SAP HANA desde copias de seguridad), se recomienda encarecidamente no realizar instantáneas del almacenamiento (si en un sistema de SAP HANA nuevos se realiza una migración del sistema, no es preciso realizar instantáneas del almacenamiento).
- Durante reorganizaciones mayores de tablas de SAP HANA, debe evitarse realizar instantáneas de mantenimiento, siempre que sea posible.
- Las instantáneas del almacenamiento son un requisito previo para la interacción de las funcionalidades de recuperación ante desastres de SAP HANA en Azure (instancias grandes).

### <a name="setting-up-storage-snapshots"></a>Configuración de instantáneas del almacenamiento

1. Asegúrese de que Perl está instalado en el sistema operativo Linux en el servidor de instancias grandes de HANA.
2. Modifique /etc/ssh/ssh\_config para agregar la línea _MACs hmac-sha1_.
3. Cree una cuenta de usuario de copia de seguridad de SAP HANA en el nodo maestro de cada instancia de SAP HANA que se ejecute (si procede).
4. El cliente HDB de SAP HANA debe instalarse en todos los servidores de instancias grandes de SAP HANA.
5. En el primer servidor de instancias grandes de SAP HANA de cada región, debe crearse una clave pública para acceder a la infraestructura de almacenamiento subyacente que controla la creación de instantáneas.
6. Copie el script azure\_hana\_backup.pl de /scripts a la ubicación de **hdbsql** de la instalación de SAP HANA.
7. Copie el archivo HANABackupDetails.txt de /scripts a la ubicación en que se encuentre el script Perl.
8. Modifique el archivo HANABackupDetails.txt tanto como sea necesario para ajustarse a las especificaciones apropiadas del cliente.

### <a name="step-1-install-sap-hana-hdbclient"></a>Paso 1: Instalar HDBClient de SAP HANA

El sistema operativo Linux instalado en SAP HANA en Azure (instancias grandes) incluye las carpetas y los scripts necesarios para ejecutar las instantáneas de almacenamiento de SAP HANA para fines de recuperación ante desastres y de copia de seguridad. Sin embargo, es responsabilidad suya instalar HDBclient de SAP HANA durante la instalación de SAP HANA (Microsoft no instalará HDBclient ni SAP HANA).

### <a name="step-2-change-etcsshsshconfig"></a>Paso 2: Cambiar/etc/ssh/ssh\_config

Cambie /etc/ssh/ssh\_config agregando la línea _MACs hmac-sha1_, como se muestra a continuación:
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>Paso 3: Crear una clave pública

En el primer servidor de SAP HANA en Azure (instancias grandes) de cada región de Azure, cree una clave pública que se use para acceder a la infraestructura de almacenamiento, lo que permite crear instantáneas. La clave pública garantiza que no se necesita una contraseña para iniciar sesión en el almacenamiento y que las credenciales de contraseña no se mantienen. En Linux, en el servidor de instancias grandes de SAP HANA, ejecute el siguiente comando para generar la clave pública:
```
  ssh-keygen –t dsa –b 1024
```
La nueva ubicación es _/root/.ssh/id\_dsa.pub. No escriba una frase de contraseña real; de lo contrario, se le pedirá que la escriba la cada vez que inicie sesión. En su lugar, presione la tecla **Intro** dos veces para quitar el requisito de especificación de frase de contraseña al iniciar sesión.

Asegúrese de que la clave pública se corrigió según lo previsto cambiando las carpetas a /root/.ssh/ y, después, ejecutando el comando **ls**. Si la clave está presente, puede copiarla ejecutando el comando siguiente:

![Para copiar la clave pública, se ejecuta este comando](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

Póngase en contacto con SAP HANA en Azure Service Management y especifique la clave. El representante del servicio usa la clave pública para registrarla en la infraestructura de almacenamiento subyacente.

### <a name="step-4-create-an-sap-hana-user-account"></a>Paso 4: Crear una cuenta de usuario de SAP HANA

Cree una cuenta de usuario de SAP HANA en SAP HANA Studio para realizar copias de seguridad. Esta cuenta debe tener los siguientes privilegios: _Backup Admin_ (Administración de copias de seguridad) y _Catalog Read_ (Lectura de catálogos). En este ejemplo, se crea el nombre de usuario SCADMIN.

![Creación de un usuario en HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Paso 5: Autorizar la cuenta de usuario de SAPA HANA

Autorice la cuenta de usuario de SAPA HANA (que van a usar los scripts sin requerir autorización cada vez que se ejecuta el script). El comando de SAP HANA `hdbuserstore` permite crear una clave de usuario de SAP HANA, que se almacena en uno o varios nodos de SAP HANA. La clave de usuario también permite al usuario acceder a SAP HANA sin tener que administrar contraseñas mediante el proceso de scripting que se describe más adelante.

>[!IMPORTANT]
>Ejecute el siguiente comando como `_root_`. De lo contrario, el script no funcionará correctamente.

Escriba el comando `hdbuserstore` de esta forma:

![Ejecución del comando hdbuserstore](./media/hana-overview-high-availability-disaster-recovery/image4-hdbuserstore-command.png)

En el ejemplo siguiente, en el que el usuario es SCADMIN01 y el nombre del host es lhanad01, el comando es:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Administre todo el scripting desde un único servidor para las instancias de HANA de escalado horizontal. En este ejemplo, la clave de SAP HANA SCADMIN01 debe modificarse en cada host de forma que refleje que el host está relacionado con la clave. Es decir, se modificará la cuenta de copia de seguridad de SAP HANA con el número de instancia de la base de datos de HANA **lhanad**. La clave debe tener privilegios administrativos en el host al que se asigna a y el usuario de copia de seguridad para el escalado horizontal debe tener derechos de acceso a todas las instancias de SAP HANA.
```
hdbuserstore set SCADMIN01 lhanad:30015 SCADMIN <password>
hdbuserstore set SCADMIN02 lhanad:30115 SCADMIN <password>
hdbuserstore set SCADMIN03 lhanad:30215 SCADMIN <password>
```

### <a name="step-6-copy-items-from-the-scripts-folder"></a>Paso 6: Copiar elementos de la carpeta/scripts

Copie los siguientes elementos de la carpeta /scripts, incluida en la imagen dorada de la instalación, en el directorio de trabajo de **hdbsql**. En el caso de las instalaciones actuales de HANA, se trata de /hana/shared/D01/exe/linuxx86\_64/hdb.
```
azure\_hana\_backup.pl
testHANAConnection.pl
testStorageSnapshotConnection.pl
removeTestStorageSnapshot.pl
HANABackupCustomerDetails.txt
```
Copie los siguientes elementos si ejecutan el escalado horizontal u OLAP:
```
azure\_hana\_backup\_bw.pl
testHANAConnectionBW.pl
testStorageSnapshotConnectionBW.pl
removeTestStorageSnapshotBW.pl
HANABackupCustomerDetailsBW.txt
```
El archivo HANABackupCustomerDetails.txt se puede modificar como se indica a continuación para una implementación de escalado vertical. Se trata del archivo de control y configuración del script que ejecutará las instantáneas del almacenamiento. Debe haber recibido el _nombre de la copia de seguridad del almacenamiento_ y la _dirección IP de almacenamiento_ de SAP HANA en Azure Service Management cuando se implementaron las instancias. No puede modificar la secuencia, el orden ni el espaciado de ninguna de las variables, ya que, si lo hace, el script no se ejecutará correctamente.

En el caso de una implementación escalada verticalmente, el archivo de configuración sería similar a este:
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Created by customer using hdbuserstore
HANA Backup Name: SCADMIND01
```
En el caso de una configuración de escalado horizontal, así sería el archivo HANABackupCustomerDetailsBW.txt:
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 10.254.15.21
Node 1 HANA instance number: 01
Node 1 HANA Backup Name: SCADMIN01
Node 2 IP Address: 10.254.15.22
Node 2 HANA instance number: 02
Node 2 HANA Backup Name: SCADMIN02
Node 3 IP Address: 10.254.15.23
Node 3 HANA instance number: 03
Node 3 HANA Backup Name: SCADMIN03
Node 4 IP Address: 10.254.15.24
Node 4 HANA instance number: 04
Node 4 HANA Backup Name: SCADMIN04
Node 5 IP Address: 10.254.15.25
Node 5 HANA instance number: 05
Node 5 HANA Backup Name: SCADMIN05
Node 6 IP Address: 10.254.15.26
Node 6 HANA instance number: 06
Node 6 HANA Backup Name: SCADMIN06
Node 7 IP Address: 10.254.15.27
Node 7 HANA instance number: 07
Node 7 HANA Backup Name: SCADMIN07
Node 8 IP Address: 10.254.15.28
Node 8 HANA instance number: 08
Node 8 HANA Backup Name: SCADMIN08
```
>[!NOTE]
>Actualmente, solo se usa la información del nodo 1 en el script de instantánea de almacenamiento de HANA real. Se recomienda que pruebe el acceso en todos los nodos de HANA para que, si el nodo maestro de copia de seguridad cambia en algún momento, tenga seguro que cualquier otro pueda ocupar su lugar mediante la modificación de la información del nodo 1.

Para comprobar que las configuraciones del archivo de configuración son correctas o que la conectividad a las instancias HANA es adecuada, ejecute cualquiera de los siguientes scripts:
- Para una configuración de escalado vertical (independientemente de la carga de trabajo de SAP):

 ```
testHANAConnection.pl
```
- Para una configuración de escalado horizontal:

 ```
testHANAConnectionBW.pl
```

Asegúrese de que la instancia de HANA maestra tiene acceso a todos los servidores de HANA necesarios. En el script no hay parámetros, pero para que se ejecute correctamente, es preciso completar los archivos HANABackupCustomerDetails y HANABackupCustomerDetailsBW adecuados. Como solo se devuelven los códigos de error del comando shell, el script no puede comprobar los errores en todas las instancias. Aun así, el script proporciona algunos comentarios útiles que debe repasar.

Para ejecutar el script:
```
 ./testHANAConnection.pl
```
 Si el script obtiene correctamente el estado de la instancia de HANA, se mostrará un mensaje que indica que la conexión con HANA se ha establecido con éxito.

Además, hay un segundo tipo de script que puede usar comprobar la capacidad del servidor maestro de la instancia de HANA de iniciar sesión en el almacenamiento. Antes que el script azure\_hana\_backup(\_bw).pl, debe ejecutar el siguiente. Si un volumen no contiene instantáneas, no podrá determinar si el volumen está simplemente vacío o si algún error de SSH al obtener la información de instantánea. Por este motivo, el script ejecuta dos pasos:

- Comprueba que se puede acceder a la consola de almacenamiento.
- Crea una instantánea de prueba, o ficticia, para cada volumen por instancia de HANA.

Por este motivo, la instancia de HANA se incluye como argumento. Una vez más, no es posible proporcionar comprobación de errores de la conexión de almacenamiento, pero el script proporciona sugerencias útiles si se produce algún error en la ejecución.

El script se ejecuta como se indica a continuación:
```
 ./testStorageSnapshotConnection.pl <hana instance>
```
También se puede ejecutar como:
```
./testStorageSnapshotConnectionBW.pl <hana instance>
```
El script también mostrará un mensaje que indica que puede iniciar sesión correctamente en el inquilino de almacenamiento que ha implementado, que se organiza alrededor de los números de unidad lógica (LUN) que usan las instancias de servidor de las que es propietario.

Antes de ejecutar las primeras copias de seguridad basadas en instantáneas del almacenamiento, debe ejecutar estos scripts para asegurarse de que la configuración es correcta.

Después de ejecutar estos scripts, puede eliminar las instantáneas, para lo que debe ejecutar:
```
./removeTestStorageSnapshot.pl <hana instance>
```
o
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="step-7-perform-on-demand-snapshots"></a>Paso 7: Realizar instantáneas a petición

Realice instantáneas a petición (así como programar instantáneas periódicas mediante cron) tal y como se describe aquí.

Para configuraciones de escalado vertical, ejecute el script siguiente:
```
./azure_hana_backup.pl lhanad01 customer 20
```
Para configuraciones de escalado horizontal, ejecute el script siguiente:
```
./azure_hana_backup_bw.pl lhanad01 customer 20
```
El script de escalado horizontal realiza algunas comprobaciones adicionales para asegurarse de que todos los servidores de HANA y todas las instancias HANA devuelven el estado apropiado de la instancia antes de continuar con la creación de instantáneas de SAP HANA o del almacenamiento.

Los siguientes argumentos son obligatorios:

- La instancia de HANA que necesita una copia de seguridad.
- El prefijo de la instantánea del almacenamiento.
- El número de instantáneas que se mantendrán para el prefijo concreto.

```
./azure_hana_backup.pl lhanad01 customer 20
```

La ejecución del script crea la instantánea del almacenamiento en estas tres fases:

- Ejecutar una instantánea de HANA.
- Ejecutar una instantánea del almacenamiento.
- Quitar la instantánea de HANA.

Ejecute el script, para lo que debe llamarlo desde la carpeta del ejecutable de HDB en la que se copió. Realizará una copia de seguridad de, al menos, los siguientes volúmenes, pero también realizará una copia de seguridad de cualquier volumen que tenga el nombre de instancia de SAP HANA explícito en el nombre del volumen.
```
hana_data_<hana instance>_prod_t020_vol
hana_log_<hana instance>_prod_t020_vol
hana_log_backup_<hana instance>_prod_t020_vol
hana_shared_<hana instance>_prod_t020_vol
```
El periodo de retención se administra estrictamente con el número de instantáneas que se envían como parámetro al ejecutar el script (como 20, que se muestra arriba). Por lo tanto, la cantidad de tiempo es una función del período de ejecución y el número de instantáneas en la llamada del script. Si el número de instantáneas que se mantienen supera el número del parámetro de la llamada del script, la instantánea del almacenamiento más antigua de esta etiqueta (en nuestro caso anterior, _custom_), se eliminarán antes de ejecutar una nueva instantánea. Esto significa que el número que proporcione como último parámetro de la llamada es el que puede utilizar para controlar el número de instantáneas.

>[!NOTE]
>En cuanto cambie la etiqueta, el recuento se reinicia.

Es preciso que incluya el nombre de instancia de HANA que proporciona SAP HANA en Azure Service Management como argumento si son entornos de varios nodos de toma de instantáneas. En los entornos de un solo nodo, el nombre de la unidad de SAP HANA en Azure (instancias grandes) es suficiente, pero se recomienda usar el nombre de instancia de HANA.

Además, puede realizar una copia de seguridad de volúmenes de arranque o LUN usando el mismo script. Debe hacer una copia de seguridad de su volumen de arranque, al menos, una vez la primera vez que se ejecute HANA, aunque se recomienda programar una copia de seguridad semanal o nocturna del arranque en cron. En lugar de agregar un nombre de instancia de SAP HANA, solo tiene que insertar _boot_ como argumento en el script como se indica a continuación:
```
./azure_hana_backup boot customer 20
```
La misma directiva de retención se permite también en el volumen de arranque. Use las instantáneas a petición, como se ha descrito, solo para casos especiales (como durante una actualización de SAP EHP o cuando necesite crear una instantánea del almacenamiento distinta).

Se recomienda realizar instantáneas del almacenamiento programadas mediante cron y usar el mismo script para todas las copias de seguridad y las necesidades de recuperación ante desastres (mediante la modificación de las entradas del script para que se ajusten a los distintos tiempos de copia de seguridad solicitados). Todas ellas se programan de forma diferente en cron en función de su tiempo de ejecución: cada hora, cada 12 horas, a diario o semanalmente. La programación de cron está diseñada para crear instantáneas del almacenamiento que coincidan con las etiquetas de retención ya explicadas para la copia de seguridad a largo plazo fuera del sitio. El script incluye comandos para la realización de copias de seguridad de todos los volúmenes de producción según la frecuencia solicitada (las copias de seguridad de los archivos de datos y de registro se realizan cada hora, mientras que la copia de seguridad del volumen de arranque se realiza una vez al día).

Las entradas del siguiente script de cron se ejecutan cada hora, cada 12 horas y cada 24 horas (o a diario), siempre pasados 10 minutos de la hora en punto. Los trabajos de cron se crean de forma que solo se toma una instantánea del almacenamiento de SAP HANA durante cualquier hora, con el fin de que no se realice al mismo tiempo una copia de seguridad por hora y una copia de seguridad diaria (00:10). SAP HANA en Azure Service Management indicará la hora a la que se recomienda ejecutar las copias de seguridad para optimizar la creación y replicación de instantáneas.

Esta es la programación predeterminada de cron en /etc/crontab:
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 14
```
En las instrucciones de cron anteriores, los volúmenes de HANA (sin volumen de arranque) obtienen una instantánea con la etiqueta cada hora. De todas estas instantáneas se conservan 66. Además, se conservan 14 instantáneas con la etiqueta de 12 horas. Es posible que obtenga instantáneas cada hora durante 3 días, además de instantáneas cada 12 horas durante otros 4 días, por lo que estará recibiendo instantáneas durante toda una semana.

La programación en cron puede ser complicada, ya que no puede ejecutarse más de un script en un momento dado, a menos que el script se escalone en varios minutos. Si quiere copias de seguridad diarias para la retención a largo plazo, o se conserva una diaria junto con una de 12 horas (con un recuento de retención de siete cada una) o la instantánea se escalona para que se tome 10 minutos después, pero en el volumen de producción solo se conserva una instantánea diaria. Solo se conserva una instantánea diaria en el volumen de producción.
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 7
10 0 * * * ./azure_hana_backup.pl lhanad01 daily 7
```
Las frecuencias enumeradas aquí son solo ejemplos. Para llegar al número óptimo de instantáneas, use los criterios siguientes:

- Requisitos de objetivo de tiempo de recuperación para la recuperación a un momento dado.
- Uso del espacio.
- Requisitos de objetivos de punto de recuperación y de tiempo de recuperación para una posible recuperación ante desastres.
- Ejecución eventual de copias de seguridad completas de la base de datos de HANA en discos. Cada vez que se realiza una copia de seguridad completa de la base de datos en discos, o interfaz de _backint_, se producirá un error en la ejecución de las instantáneas del almacenamiento. Si planea ejecutar copias de seguridad de la base de datos completa sobre las instantáneas del almacenamiento, asegúrese de que la ejecución de las instantáneas del almacenamiento está deshabilitada durante este tiempo.

>[!IMPORTANT]
> El uso de instantáneas de almacenamiento para las copias de seguridad de SAP HANA solo es válido cuando se realiza junto con las copias de seguridad del registro de SAP HANA. Es preciso que dichas copias de seguridad del registro puedan cubrir los períodos entre las instantáneas del almacenamiento. Si ha adquirido un compromiso a los usuarios de una recuperación a un momento dado de 30 días, necesita lo siguiente:

- Capacidad para acceder a una instantánea del almacenamiento con una antigüedad de 30 días.
- Copias de seguridad del registro contiguas durante los últimos 30 días.

En el intervalo de las copias de seguridad del registro, crea una instantánea también del volumen del registro de la copia de seguridad. Sin embargo, asegúrese de realizar copias de seguridad periódicas del registro para que pueda realizar lo siguiente:

- Tener las copias de seguridad del registro contiguas necesarias para realizar recuperaciones a un momento dado.
- Evitar que el volumen del registro de SAP HANA se quede sin espacio.

Uno de los últimos pasos es programar los registros de copia de seguridad de SAP HANA en SAP HANA Studio. El destino del registro de copia de seguridad de SAP HANA es el volumen hana/log\_backups creado especialmente con el punto de montaje de /hana/log/backups.

![Programe los registros de copia de seguridad de SAP HANA en SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Puede elegir que las copias de seguridad se realicen con una frecuencia superior a 15 minutos. Algunos usuarios incluso realizan copias de seguridad del registro cada minuto; sin embargo, no se recomienda _superar_ los 15 minutos.

El último paso es crear una copia de seguridad en archivo (después de la instalación inicial de SAP HANA) para crear una entrada de copia de seguridad individual que debe existir en el catálogo de copias de seguridad. En caso contrario, SAP HANA no podrá iniciar las copias de seguridad del registro especificadas.

![Realizar una copia de seguridad en archivo para crear una entrada de copia de seguridad individual](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Supervisión del número y tamaño de instantáneas del volumen de disco

En un volumen de almacenamiento concreto se puede supervisar tanto el número de instantáneas como el consumo de almacenamiento de las instantáneas. El comando `ls` no muestra los archivos o el directorio de instantáneas. Sin embargo, el comando del sistema operativo Linux `du` sí lo hace, con los siguientes comandos:

- `du –sh .snapshot` proporciona un total de todas las instantáneas en el directorio de instantáneas.
- `du –sh --max-depth=1` enumera todas las instantáneas que se guardan en la carpeta .snapshot y el tamaño de cada una de ellas.
- `du –hc` proporciona el tamaño total que usan todas las instantáneas.

Utilice estos comandos para asegurarse de que las instantáneas que tomadas y almacenadas no consumen todo el almacenamiento de los volúmenes.

### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Reducción del número de instantáneas en un servidor

Como ya se ha explicado, es posible reducir el número de etiquetas concretas almacenadas. Los dos últimos parámetros del comando para iniciar una instantánea son una etiqueta y el número de instantáneas que se desean conservar.
```
./azure_hana_backup.pl lhanad01 customer 20
```
En el ejemplo anterior, la etiqueta de la instantánea es _customer_ y el número de instantáneas con esta etiqueta que se conservarán es _20_. A medida que responda al consumo de espacio en el disco es posible que desee reducir el número de instantáneas que se almacenan. La manera más sencilla de reducir el número de instantáneas es ejecutar el script con el último parámetro establecido en 5:
```
./azure_hana_backup.pl lhanad01 customer 5
```
Como consecuencia de la ejecución del script con la configuración anterior, se reducen las instantáneas, con lo que el número de ellas, incluida la nueva instantánea del almacenamiento, es _5_.

 >[!NOTE]
 > Este script reduce el número de instantáneas solo si la instantánea anterior más reciente tiene más de una hora de antigüedad. Este script no elimina las instantáneas que tienen menos de una hora de antigüedad.

Estas restricciones están relacionadas con la funcionalidad opcional de recuperación ante desastres que se ofrece.

Si ya no desea mantener un conjunto de instantáneas con el prefijo, puede ejecutar el script con _0_ como el número de retención para quitar todas las instantáneas que coincidan con ese prefijo. Sin embargo, la eliminación de todas las instantáneas puede afectar a las funcionalidades de recuperación ante desastres.

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>Recuperación a la instantánea más reciente de HANA

En el caso de que se encuentre ante un escenario de bajada de producción, el proceso de recuperación a partir de una instantánea del almacenamiento se puede iniciar como un incidente de cliente con SAP HANA en Azure Service Management. Por ejemplo, un escenario inesperado podría ser una cuestión muy urgente si los datos se eliminaran en un sistema de producción y la única manera de volver a obtenerlos fuera a través de la restauración de la base de datos de producción.

Por otro lado, una recuperación en un momento puede ser poco urgente y se puede planear con varios días de antelación. Para planearlo, se puede usar SAP HANA en Azure Service Management, en lugar de generar un problema de prioridad alta. Por ejemplo, podría estar planeando tratar de realizar una actualización del software SAP mediante la aplicación de un nuevo paquete de mejoras y, luego, necesitara revertir a una instantánea que represente el estado antes de la actualización de EHP.

Antes de emitir la solicitud, debe realizar una preparación. Después, el equipo de SAP HANA en Azure Service Management podrá encargarse de la solicitud y proporcionar los volúmenes restaurados. A partir de ese momento, es decisión suya restaurar la base de datos de HANA con las instantáneas. Aquí se muestra cómo preparar la solicitud:

>[!NOTE]
>La interfaz de usuario podría variar con respecto a las capturas de pantalla siguientes, en función de la versión de SAP HANA que use.

1. Decida qué instantánea desea restaurar. Salvo que se indique lo contrario solo se restauraría el volumen de datos/HANA.

2. Cierre la instancia de HANA.

 ![Cerrar la instancia de HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Desmonte los volúmenes de datos de todos los nodos de la base de datos de HANA. La restauración de la instantánea no se realizará si los volúmenes de datos no se desmontan.

 ![Desmontar los volúmenes de datos de todos los nodos de la base de datos de HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Abra una solicitud de soporte técnico de Azure para enviar la orden de restauración de una instantánea concreta.

 - Durante la restauración: SAP HANA en Azure Service Management podría pedirle que asista a una llamada de conferencia para asegurarse de que no se pierda ningún dato.

 - Después de la restauración: SAP HANA en Azure Service Management le enviará una notificación cuando la instantánea del almacenamiento se haya restaurado.

5. Una vez que el proceso de restauración se haya completado, vuelva a montar todos los volúmenes de datos.

 ![Volver a montar todos los volúmenes de datos](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. Seleccione las opciones de recuperación en SAP HANA Studio, en caso de que no aparezcan automáticamente al volver a conectarse a base de datos de HANA a través de HANA Studio. En el siguiente ejemplo se muestra una operación de restauración a la instantánea de HANA más reciente. Una instantánea del almacenamiento inserta una instantánea de HANA y, si va a realizar la restauración a la instantánea del almacenamiento más reciente, debe ser la instantánea de HANA más reciente. (Si va a realizar la restauración a instantáneas del almacenamiento más antiguas, será preciso que encuentre la instantánea de HANA por la hora en que se tomó la instantánea del almacenamiento.)

 ![Seleccionar las opciones de recuperación en SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. Seleccione **Recover the database to a specific data backup or storage snapshot** (Recuperar la base en una instantánea del almacenamiento o de copia de seguridad de datos concreta).

 ![La ventana "Specify Recovery Type" (Especificar tipo de recuperación)](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. Seleccione **Specify backup without catalog** (Especificar copia de seguridad sin catálogo).

 ![La ventana "Specify Backup Location" (Especificar ubicación de copia de seguridad)](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. En la lista **Destination Type** (Tipo de destino), seleccione **Snapshot** (Instantánea).

 ![La ventana "Specify the Backup to Recover" (Especificar la copia de seguridad que se recuperará)](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. Haga clic en **Finish** (Finalizar) para iniciar el proceso de recuperación.

 ![Clic en "Finish" (Finalizar) para iniciar el proceso de recuperación](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. La base de datos HANA se restaura y recupera en la instantánea de HANA incluida en la instantánea del almacenamiento.

 ![La base de datos HANA se restaura y recupera en la instantánea de HANA.](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>Recuperación al estado más reciente

El proceso siguiente restaura la instantánea de HANA que se incluye en la de almacenamiento. Luego, se restauran las copias de seguridad del registro de transacciones al estado más reciente de la base de datos antes de restaurar la instantánea de almacenamiento.

>[!IMPORTANT]
>Antes de continuar, asegúrese de que dispone de una cadena completa y contigua de copias de seguridad del registro de transacciones. Sin estas copias de seguridad, no podrá restaurar el estado actual de la base de datos.

1. Complete los pasos del 1 al 6 del procedimiento anterior de Recuperación a la instantánea más reciente de HANA.

2. Elija **Recover the database to its most recent state** (Recuperar la base de datos a su estado más reciente).

 ![Seleccione "Recover the database to its most recent state" (Recuperar la base de datos a su estado más reciente).](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. Especifique la ubicación de las copias de seguridad de registros de HANA más recientes. La ubicación tiene que contener todas las copias del registro de transacciones de HANA desde la instantánea de HANA al estado más reciente.

 ![Especifique la ubicación de las copias de seguridad de registros de HANA más recientes.](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. Elija una copia de seguridad como base desde la que recuperar la base de datos. En nuestro ejemplo, se trata de la instantánea de HANA que se incluyó en la instantánea del almacenamiento. Solo se muestra una instantánea en la siguiente captura de pantalla.

 ![Elija una copia de seguridad como base desde la que recuperar la base de datos.](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. Desactive la casilla **Use Delta Backups** (Usar copias de seguridad diferenciales) si no existe entre el momento de la instantánea de HANA y el estado más reciente.

 ![Desactive la casilla "Use Delta Backups" (Usar copias de seguridad diferenciales) si no hay datos diferenciales.](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. Haga clic en **Finish** (Finalizar) en la pantalla de resumen para iniciar el procedimiento de restauración.

 ![Haga clic en "Finish" (Finalizar) en la pantalla de resumen.](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>Recuperación a un momento dado
Para realizar la recuperación a un momento dado entre la instantánea de HANA (incluida en la instantánea del almacenamiento) y una que sea posterior a la recuperación a un momento dado de la instantánea de HANA, siga estos pasos:

1. Asegúrese de que dispone de todas las copias de seguridad del registro de transacciones desde la instantánea de HANA hasta el momento en que desee recuperar.
2. Inicie el procedimiento de Recuperación al estado más reciente.
3. En el paso 2 del procedimiento, en la ventana **Specify Recovery Type** (Especificar tipo de recuperación), seleccione **Recover the database to the following point in time** (Recuperar la base de datos al siguiente momento dado), especifique el momento dado y, luego, complete los pasos del 3 al 6.

## <a name="monitoring-the-execution-of-snapshots"></a>Supervisión de la ejecución de instantáneas

Debe supervisar la ejecución de las instantáneas de almacenamiento. El script que ejecuta una instantánea del almacenamiento escribe la salida en un archivo y, luego, guarda en la misma ubicación que los scripts Perl. Se escribe un archivo independiente para cada instantánea. La salida de cada archivo muestra claramente las distintas fases que ejecuta el script de la instantánea:

- Búsqueda de los volúmenes que necesitan crear una instantánea
- Búsqueda de las instantáneas tomadas de dichos volúmenes
- Eliminación de eventuales instantáneas existentes para ajustarse el número de instantáneas especificado
- Creación de una instantánea de HANA
- Creación de la instantánea del almacenamiento en los volúmenes
- Eliminación de la instantánea de HANA
- Cambio del nombre de la instantánea más reciente a **.0**

La parte más importante del script es esta:
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
En este ejemplo puede ver la forma en que el script registra la creación de la instantánea de HANA. En el caso del escalado horizontal, este proceso se inicia en el nodo maestro. El nodo maestro iniciará la creación sincrónica de las instantáneas en cada uno de los nodos de trabajador. Después, se toma la instantánea del almacenamiento. Después de la ejecución correcta de las instantáneas del almacenamiento, se elimina la instantánea de HANA.

