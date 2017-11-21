---
title: "Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes) | Microsoft Docs"
description: "En este documento se explica cómo establecer una estrategia de alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)"
services: virtual-machines-linux
documentationcenter: 
author: saghorpa
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 09aa98a35fa8286828a99c49a33a80d5938afe3a
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Alta disponibilidad y recuperación ante desastres de SAP HANA para instancias grandes en Azure 

Los conceptos de alta disponibilidad y recuperación ante desastres (DR) son aspectos importantes de la ejecución de servidores críticos de SAP HANA en Azure (instancias grandes). Es importante trabajar con SAP, el integrador del sistema, o Microsoft para diseñar e implementar correctamente la estrategia correcta de alta disponibilidad y recuperación ante desastres. También resulta fundamental tener en cuenta los objetivos de punto de recuperación (RPO) y de tiempo de recuperación, que son específicos del entorno.

Microsoft admite algunas funcionalidades de alta disponibilidad de SAP HANA con instancias grandes de HANA. Estas son algunas de ellas:

- **Replicación de almacenamiento**: la capacidad que tiene el propio sistema de almacenamiento para replicar todos los datos a otra marca de instancias grandes de HANA en otra región de Azure. SAP HANA funciona de forma independiente con respecto a este método.
- **Replicación del sistema de HANA**: la replicación de todos los datos de SAP HANA a un sistema SAP HANA independiente. El objetivo de tiempo de recuperación se minimiza a través de la replicación de datos a intervalos regulares. SAP HANA admite los modos asincrónico, sincrónico en memoria y sincrónico. El modo sincrónico solo se recomienda para sistemas de SAP HANA que estén en el mismo centro de datos o a menos de 100 km de este. En el diseño actual de las marcas de instancias grandes de HANA, la replicación del sistema de HANA solo puede usarse para lograr alta disponibilidad. Actualmente, la replicación del sistema de HANA requiere un componente de proxy inverso de terceros para configuraciones de recuperación ante desastres en otra región de Azure. 
- **Conmutación por error automática del host**: una solución de recuperación para SAP HANA que se usará como alternativa a la replicación del sistema de HANA. Si el nodo maestro deja de estar disponible, se configura uno o varios nodos en espera de SAP HANA en el modo de escalado horizontal y SAP HANA automáticamente conmuta por error a un nodo en espera.

SAP HANA en Azure (instancias grandes) se ofrece en dos regiones de Azure que cubren tres regiones geopolíticas distintas (EE. UU., Australia y Europa). Dos regiones distintas que hospedan marcas de instancias grandes de HANA se conectan con circuitos de red dedicados independientes que se usan para replicar instantáneas de almacenamiento para proporcionar métodos de recuperación ante desastres. La replicación no se establece de manera predeterminada. Se configura para los clientes que solicitaron la funcionalidad de recuperación ante desastres. La replicación de almacenamiento depende del uso de instantáneas de almacenamiento para instancias grandes de HANA. No es posible elegir una región de Azure como región de recuperación ante desastres, que se encuentra en otra área geopolítica. 

En la tabla siguiente se muestran los métodos y las combinaciones de alta disponibilidad y recuperación ante desastres que se admiten actualmente:

| Escenario compatible con instancias grandes de HANA | Opción de alta disponibilidad | Opción de recuperación ante desastres | Comentarios |
| --- | --- | --- | --- |
| Nodo único | No disponible. | Configuración de recuperación ante desastres dedicada.<br /> Configuración de recuperación ante desastres multipropósito. | |
| Conmutación por error automática de host: N+m<br /> incluido 1+1 | Posible con el modo en espera tomando el rol activo.<br /> HANA controla el cambio de rol. | Configuración de recuperación ante desastres dedicada.<br /> Configuración de recuperación ante desastres multipropósito.<br /> Sincronización de recuperación ante desastres mediante replicación de almacenamiento. | Los conjuntos de volúmenes de HANA se adjuntan a todos los nodos (n+m).<br /> El sitio de recuperación ante desastres debe tener el mismo número de nodos. |
| Replicación del sistema de HANA | Posible con configuración principal o secundaria.<br /> La configuración secundaria pasa al rol principal en caso de conmutación por error.<br /> Replicación del sistema de HANA y conmutación por error de control del SO. | Configuración de recuperación ante desastres dedicada.<br /> Configuración de recuperación ante desastres multipropósito.<br /> Sincronización de recuperación ante desastres mediante replicación de almacenamiento.<br /> La recuperación ante desastres mediante replicación del sistema de HANA todavía no es posible sin componentes de terceros. | El conjunto independiente de volúmenes de discos se adjunta a cada nodo.<br /> Solo los volúmenes de disco de la réplica secundaria en el sitio de producción se replican a la ubicación de recuperación ante desastres.<br /> Se requiere un conjunto de volúmenes en el sitio de recuperación ante desastres. | 

Una configuración de recuperación ante desastres dedicada es aquella en la que no se usa la unidad de instancia grande de HANA en el sitio de recuperación ante desastres para ejecutar ninguna otra carga de trabajo o ningún sistema que no sea de producción. La unidad es pasiva y solo se implementa si se ejecuta una conmutación por error ante desastres. Sin embargo, esta no es la opción preferida para muchos clientes.

Una configuración de recuperación ante desastres multipropósito es aquella en la que la unidad de instancia grande de HANA en el sitio de recuperación ante desastres ejecuta una carga de trabajo no de producción. En el caso de un desastre, se apaga el sistema no de producción, se montan los conjuntos de volúmenes replicados en el almacenamiento (adicionales) y luego se inicia la instancia de HANA de producción. La mayoría de los clientes que usan la funcionalidad de recuperación ante desastres de instancias grandes de HANA usan esta configuración. 


Para más información sobre la alta disponibilidad de SAP HANA, consulte los artículos siguientes sobre SAP: 

- [SAP HANA High-Availability Whitepaper](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html) (Documento técnico sobre la alta disponibilidad de SAP HANA)
- [SAP HANA Administration Guide](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf) (Guía de administración de SAP HANA)
- [Vídeo de SAP sobre la replicación del sistema de SAP HANA](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Nota de soporte técnico de SAP 1999880: preguntas más frecuentes sobre la replicación del sistema de SAP HANA](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [Nota de soporte técnico de SAP 2165547: copia de seguridad y restauración de SAP HANA en el entorno de replicación del sistema de SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [Nota de soporte técnico de SAP 1984882: uso de la replicación del sistema de SAP HANA para el cambio de hardware con el tiempo de inactividad mínimo o cero](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Consideraciones de red para recuperación ante desastres con instancias grandes de HANA

Para aprovechar la funcionalidad de recuperación ante desastres de instancias grandes de HANA, es preciso diseñar la conectividad de red a las dos regiones de Azure distintas. Necesita un circuito ExpressRoute que se conecte desde el entorno local de su región de Azure principal y otro que se conecte desde el entorno local a su región de recuperación ante desastres. Esta medida cubre una situación en la que hay un problema en la región de Azure, incluida la ubicación del enrutador perimetral de Microsoft Enterprise (MSEE).

Como segunda medida, puede conectar todas las redes virtuales de Azure que se conectan a SAP HANA en Azure (instancias grandes) en una de las regiones a un circuito ExpressRoute que conecta las instancias grandes de HANA en la otra región. Con esa *conexión cruzada*, los servicios que se ejecutan en una red virtual de Azure en la región 1 se pueden conectar a unidades de instancia grande de HANA en la región 2 y viceversa. Así se soluciona el caso en el que solo una de las ubicaciones de MSEE que conecta la ubicación local con Azure se queda sin conexión.

En los gráficos siguientes se ilustra una configuración resistente para la recuperación ante desastres:

![Configuración óptima para la recuperación ante desastres](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-when-you-use-hana-large-instances-storage-replication-for-disaster-recovery"></a>Otros requisitos cuando se usa la replicación de almacenamiento de instancias grandes de HANA para la recuperación ante desastres

Los siguientes son los requisitos adicionales de una configuración de recuperación ante desastres con instancias grandes HANA:

- Las SKU de SAP HANA en Azure (instancias grandes) deben tener el mismo tamaño que las SKU de producción y estar implementadas en la región de recuperación ante desastres. En las implementaciones de cliente actuales, estas instancias se usan para ejecutar instancias de HANA que no son de producción. Nos referimos a ellas como *configuraciones de recuperación ante desastres multipropósito*.   
- Debe solicitar almacenamiento adicional en el sitio de recuperación ante desastres para cada una de las SKU de SAP HANA en Azure (instancias grandes) que desea recuperar en el sitio de recuperación ante desastres. La compra de almacenamiento adicional le permite asignar los volúmenes de almacenamiento. Puede asignar los volúmenes que son el destino de la replicación de almacenamiento de la región de Azure de producción a la región de Azure de recuperación ante desastres.
 

## <a name="backup-and-restore"></a>Copia de seguridad y restauración

Uno de los aspectos más importantes para el funcionamiento de las bases de datos es protegerlas de diferentes incidentes catastróficos. La causa de estos eventos pueden deberse a cualquier cosa, desde desastres naturales a simples errores del usuario.

La copia de seguridad de una base de datos, con la capacidad de realizar la restauración a cualquier momento dado (como antes de que alguien eliminara datos críticos), posibilita la restauración a un estado que sea lo más parecido posible al que había antes de que la interrupción.

Para que el resultado sea óptimo, es preciso realizar dos tipos de copias de seguridad:

- Copias de seguridad de bases de datos: copias de seguridad completas, incrementales o diferenciales
- Copias de seguridad del registro de transacciones

Además de las copias de seguridad completas de la base de datos que se realizan en un nivel de aplicación, puede realizar copias de seguridad con instantáneas de almacenamiento. Las instantáneas de almacenamiento no reemplazan las copias de seguridad del registro de transacciones. Las copias de seguridad del registro de transacciones siguen siendo importantes para restaurar la base de datos a un momento dado o para vaciar los registros de las transacciones ya confirmadas. Sin embargo, las instantáneas de almacenamiento pueden acelerar la recuperación al proporcionar rápidamente una imagen de puesta al día de la base de datos. 

SAP HANA en Azure (instancias grandes) ofrece dos opciones de copia de seguridad y restauración:

- Opción personal (DIY, por las siglas de Do It Yourself). Después de calcular el espacio en disco y asegurarse de que hay suficiente, realice copias de seguridad completas del registro y las bases de datos mediante los métodos de copia de seguridad de disco (para dichos discos). Puede hacer copias de seguridad directamente en volúmenes conectados a las unidades de instancia grande de HANA o en recursos compartidos de red (NFS) configurados en una máquina virtual (VM) de Azure. En el último caso, los clientes configuran una VM Linux en Azure, adjuntan Azure Storage a dicha VM y comparten el almacenamiento a través de un servidor NFS configurado en esa máquina virtual. Si se realiza la copia de seguridad en los volúmenes que directamente se adjuntan a unidades de instancia grande de HANA, necesita copiar las copias de seguridad a una cuenta de Azure Storage (una vez que configure una VM de Azure que exporte los recursos compartidos NFS). O bien, puede usar un almacén de copia de seguridad de Azure o el almacenamiento en frío de Azure. 

   Otra opción consiste en usar una herramienta de protección de datos de terceros para almacenar las copias de seguridad una vez que se copian en una cuenta Azure Storage. Esta opción de copia de seguridad DIY también podría ser necesaria para aquellos datos que necesita almacenar durante períodos más largos de tiempo con fines de conformidad y auditoría. En todos los casos, las copias de seguridad se copian en recursos compartidos NFS que se representan a través de una VM y Azure Storage.

- Use la funcionalidad de copia de seguridad y restauración que proporciona la infraestructura subyacente de SAP HANA en Azure (Instancias grandes). Esta opción cumple con la necesidad de copias de seguridad y restauraciones rápidas. En el resto de esta sección se trata la funcionalidad de copia de seguridad y restauración que se ofrece con las instancias grandes de HANA. Esta sección también trata la relación que la copia de seguridad y la restauración tienen con funcionalidad de recuperación ante desastres que proporcionan las instancias grandes de HANA.

> [!NOTE]
> La tecnología de instantáneas que utiliza la infraestructura subyacente de instancias grandes de HANA depende de las instantáneas de SAP HANA. En este momento, las instantáneas de SAP HANA no funcionan con los distintos inquilinos de los contenedores de base de datos multiinquilino de SAP HANA. Por tanto, este método de copia de seguridad no se puede usar cuando se implementan varios inquilinos en contenedores de base de datos multiinquilino de SAP HANA. Si solo se implementa un inquilino, las instantáneas de SAP HANA funcionan.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Uso de instantáneas de almacenamiento de SAP HANA en Azure (Instancias grandes)

La infraestructura de almacenamiento subyacente de SAP HANA en Azure (instancias grandes) admite instantáneas de almacenamiento de volúmenes. Se admite tanto la copia de seguridad como la restauración de volúmenes, pero es preciso tener en cuenta lo siguiente:

- En lugar de las copias de seguridad de bases de datos completas, se toman instantáneas del volumen de almacenamiento con frecuencia.
- Cuando se desencadena una instantánea sobre volúmenes /hana/data, hana/log y /hana/shared (incluye /usr/sap), la instantánea de almacenamiento inicia una instantánea de SAP HANA antes de ejecutar la instantánea de almacenamiento. Esta instantánea de SAP HANA es el punto de configuración de las posibles restauraciones del registro después de la recuperación de la instantánea de almacenamiento.
- Después del momento en que la instantánea de almacenamiento se ejecuta correctamente, se elimina la instantánea de SAP HANA.
- Las copias de seguridad del registro de transacciones se realizan con frecuencia y se almacenan en el volumen /hana/logbackups o en Azure. Puede desencadenar el volumen /hana/logbackups que contiene las copias de seguridad del registro de transacciones para tomar una instantánea por separado. En ese caso, no es necesario ejecutar una instantánea de HANA.
- Si debe restaurar una base de datos a un punto determinado en el tiempo, solicite al soporte técnico de Microsoft Azure (para una interrupción de producción) o a SAP HANA en Azure Service Management que restaure a una instantánea de almacenamiento determinada. Un ejemplo es una restauración planeada de un sistema de espacio aislado a su estado original.
- La instantánea de SAP HANA que se incluye en la instantánea de almacenamiento es un punto de desplazamiento para aplicar las copias de seguridad del registro de transacciones que se han ejecutado y almacenado después de que se tomara la instantánea de almacenamiento.
- Estas copias de seguridad del registro de transacciones se realizan para restaurar la base de datos a un momento dado.

Puede realizar instantáneas de almacenamiento que estén dirigidas a tres clases de volúmenes distintas:

- Una instantánea combinada en /hana/data y /hana/shared (incluye /usr/sap). Esta instantánea requiere la creación de una instantánea de SAP HANA como preparación para la instantánea de almacenamiento. La instantánea de SAP HANA se asegurará de que la base de datos esté en un estado coherente desde un punto de vista de almacenamiento.
- Una instantánea independiente en /hana/logbackups.
- Una partición del SO (solo para el tipo I de las instancias grandes de HANA).


### <a name="storage-snapshot-considerations"></a>Consideraciones de las instantáneas de almacenamiento

>[!NOTE]
>Las instantáneas de almacenamiento consumen espacio de almacenamiento que se ha asignado a las unidades de instancia grande de HANA. Por tanto, es necesario considerar los aspectos siguientes en cuanto a programar instantáneas de almacenamiento y cuántas de esas instantáneas mantener. 

Mecánica concreta de las instantáneas de almacenamiento de SAP HANA en Azure (instancias grandes):

- Una instantánea de almacenamiento concreta (en el momento en que se toma) consume poco espacio de almacenamiento.
- Cuando el contenido de datos cambia y el contenido de los archivos de datos de SAP HANA cambia en el volumen de almacenamiento, la instantánea debe almacenar el contenido del bloque original, así como los cambios de datos.
- El resultado es que la instantánea de almacenamiento aumenta de tamaño. Cuanto más tiempo dure la instantánea, mayor será la instantánea de almacenamiento.
- Cuantos más cambios se realicen en el volumen de la base de datos de SAP HANA a lo largo de la vigencia de una instantánea de almacenamiento, mayor será el consumo de espacio de dicha instantánea.

SAP HANA en Azure (instancias grandes) incluye tamaños de volumen fijos para los volúmenes de datos y de registro de SAP HANA. Realizar instantáneas de esos volúmenes consume el espacio del volumen. Debe determinar cuándo se deben programar las instantáneas de almacenamiento. También necesita supervisar el consumo de espacio de los volúmenes de almacenamiento, así como administrar el número de instantáneas que se almacenan. Puede deshabilitar las instantáneas de almacenamiento cuando importa masas de datos o realiza otros cambios importantes en la base de datos de HANA. 


En las secciones siguientes se proporciona información para realizar estas instantáneas, donde también se incluyen recomendaciones generales:

- Aunque el hardware puede admitir 255 instantáneas por volumen, se recomienda encarecidamente no acercarse a este número.
- Antes de realizar instantáneas de almacenamiento, supervise y realice un seguimiento del espacio disponible.
- Reduzca el número de instantáneas del almacenamiento en función del espacio libre. Puede reducir el número de instantáneas que mantiene, o ampliar los volúmenes. Puede solicitar más almacenamiento en unidades de un terabyte.
- Durante actividades como mover datos a SAP HANA con herramientas de migración de la plataforma SAP (R3load) o restaurar bases de datos de SAP HANA a partir de copias de seguridad, deshabilite las instantáneas de almacenamiento en el volumen /hana/data. 
- Durante reorganizaciones mayores de tablas de SAP HANA, debe evitarse la realización de instantáneas de mantenimiento, siempre que sea posible.
- Las instantáneas de almacenamiento son un requisito previo para aprovechar las funcionalidades de recuperación ante desastres de SAP HANA en Azure (instancias grandes).

### <a name="setting-up-storage-snapshots"></a>Configuración de instantáneas del almacenamiento

Los pasos para configurar las instantáneas de almacenamiento con instancias grandes de HANA son los siguientes:
1. Asegúrese de que Perl está instalado en el sistema operativo Linux en el servidor de instancias grandes de HANA.
2. Modifique /etc/ssh/ssh\_config para agregar la línea _MACs hmac-sha1_.
3. Cree una cuenta de usuario de copia de seguridad de SAP HANA en el nodo maestro de cada instancia de SAP HANA que se ejecute, si procede.
4. Instale el cliente HDB de SAP HANA en todos los servidores de instancias grandes de SAP HANA.
5. En el primer servidor de instancias grandes de SAP HANA de cada región, cree una clave pública para acceder a la infraestructura de almacenamiento subyacente que controla la creación de instantáneas.
6. Copie los scripts y el archivo de configuración de [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) a la ubicación de **hdbsql** en la instalación de SAP HANA.
7. Modifique el archivo HANABackupDetails.txt tanto como sea necesario para ajustarse a las especificaciones apropiadas del cliente.

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Paso 1: Instalar al cliente de HDB de SAP HANA

El sistema operativo Linux instalado en SAP HANA en Azure (instancias grandes) incluye las carpetas y los scripts necesarios para ejecutar las instantáneas de almacenamiento de SAP HANA para fines de recuperación ante desastres y de copia de seguridad. Busque las versiones más recientes en [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). La versión más reciente de los scrips es 2.1.
Sin embargo, es responsabilidad suya instalar el cliente de HDB de SAP HANA en las unidades de instancia grande de HANA durante la instalación de SAP HANA. (Microsoft no instala el cliente de HDB o SAP HANA).

### <a name="step-2-change-the-etcsshsshconfig"></a>Paso 2: Cambiar /etc/ssh/ssh\_config

Cambie `/etc/ssh/ssh_config` agregando la línea _MACs hmac-sha1_, como se muestra a continuación:
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

Para habilitar el acceso a las interfaces de instantánea de almacenamiento del inquilino de instancia grande de HANA, se debe establecer un inicio de sesión a través de la clave pública. En el primer servidor de SAP HANA en Azure (instancias grandes) del inquilino, cree una clave pública que se use para acceder a la infraestructura de almacenamiento, lo que permite crear instantáneas. La clave pública garantiza que no se necesita una contraseña para iniciar sesión en las interfaces de instantánea de almacenamiento. La creación de una clave pública también significa que no es necesario mantener credenciales de contraseña. En Linux, en el servidor de instancias grandes de SAP HANA, ejecute el siguiente comando para generar la clave pública:
```
  ssh-keygen –t dsa –b 1024
```
La nueva ubicación es **_/root/.ssh/id\_dsa.pub**. No escriba una contraseña real; de lo contrario, se le pedirá que la escriba cada vez que inicie sesión. En su lugar, seleccione la tecla **Intro** dos veces para quitar el requisito de especificación de contraseña al iniciar sesión.

Asegúrese de que la clave pública se corrigió según lo previsto cambiando las carpetas a **/root/.ssh/** y, después, ejecutando el comando `ls`. Si la clave está presente, puede copiarla ejecutando el comando siguiente:

![La clave pública se copia ejecutando este comando](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

En este momento, póngase en contacto con SAP HANA en Azure Service Management y proporcióneles la clave pública. El representante del servicio usa la clave pública para registrarla en la infraestructura de almacenamiento subyacente que se crea para el inquilino de instancia grande de HANA.

### <a name="step-4-create-an-sap-hana-user-account"></a>Paso 4: Crear una cuenta de usuario de SAP HANA

Para iniciar la creación de instantáneas de SAP HANA, es necesario crear una cuenta de usuario en SAP HANA, que los scripts de instantánea de almacenamiento puedan usar. Cree una cuenta de usuario de SAP HANA en SAP HANA Studio para esta finalidad. Esta cuenta debe tener los siguientes privilegios: **Backup Admin** (Administración de copias de seguridad) y **Catalog Read** (Lectura de catálogos). En este ejemplo, el nombre de usuario es **SCADMIN**. El nombre de la cuenta de usuario que se creó en HANA Studio distingue mayúsculas de minúsculas. Asegúrese de seleccionar **No** para requerir al usuario que cambie la contraseña en el siguiente inicio de sesión.

![Creación de un usuario en HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Paso 5: Autorizar la cuenta de usuario de SAPA HANA

En este paso se autoriza a la cuenta de usuario de SAP HANA que creó, de manera que los scripts no necesitan enviar contraseñas en tiempo de ejecución. El comando de SAP HANA `hdbuserstore` habilita la creación de una clave de usuario de SAP HANA, que se almacena en uno o varios nodos de SAP HANA. La clave de usuario permite que el usuario acceda a SAP HANA sin tener que administrar contraseñas mediante el proceso de scripting. El proceso de scripting se describe más adelante.

>[!IMPORTANT]
>Ejecute el siguiente comando como `root`. De lo contrario, el script no funcionará correctamente.

Escriba el comando `hdbuserstore` de esta forma:

**Para la configuración de HANA distinta de MDC**
```
hdbuserstore set <key> <host>:<3[instance]15> <user> <password>
```

**Para la configuración de HANA MDC**
```
hdbuserstore set <key> <host>:<3[instance]13> <user> <password>
```

En el ejemplo siguiente, el usuario es **SCADMIN01**, el nombre del host es **lhanad01** y el número de instancia es **01**:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Si tiene una configuración de escala horizontal de SAP HANA, debe administrar todo el scripting desde un solo servidor. En este ejemplo, la clave de SAP HANA **SCADMIN01** debe modificarse en cada host de forma que refleje qué host está relacionado con la clave. Modifique la cuenta de copia de seguridad de SAP HANA con el número de instancia de la base de datos de HANA. La clave debe tener privilegios administrativos en el host al que se asigna y el usuario de copia de seguridad para configuraciones de escalado horizontal debe tener derechos de acceso a todas las instancias de SAP HANA. Si los tres nodos de escalado horizontal se llaman **lhanad01**, **lhanad02** y **lhanad03**, la secuencia de comandos es la siguiente:

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Paso 6: Obtener los scripts de instantánea, configurar las instantáneas y probar la configuración y la conectividad

Descargue la versión más reciente de los scripts de [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). Copie los scripts descargados y el archivo de texto en el directorio de trabajo de **hdbsql**. En el caso de las instalaciones actuales de HANA, este directorio es similar a /hana/shared/D01/exe/linuxx86\_64/hdb. 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl 
HANABackupCustomerDetails.txt 
``` 


Esta es la finalidad de los distintos scripts y archivos:

- **azure\_hana\_backup.pl**: programe este script con cron para ejecutar instantáneas de almacenamiento en los volúmenes data/log/shared de HANA, en el volumen /hana/logbackups o en el sistema operativo (en las SKU de tipo I de las instancias grandes de HANA).
- **azure\_hana\_replication\_status.pl**: este script está diseñado para proporcionar detalles básicos sobre el estado de la replicación desde el sitio de producción al sitio de recuperación ante desastres. El script realiza la supervisión para asegurarse de que se está llevando a cabo la replicación y muestra el tamaño de los elementos que se están replicando. También brinda una guía de lo que se debe hacer si la replicación tarda demasiado o si el vínculo no está activo.
- **azure\_hana\_snapshot\_details.pl**: este script proporciona una lista de los detalles básicos sobre todas las instantáneas, por volumen, que existen en el entorno. Este script se puede ejecutar en el servidor principal o en una unidad de servidor en la ubicación de recuperación ante desastres. El script proporciona la siguiente información desglosada por cada volumen que contiene instantáneas:
   * Tamaño de las instantáneas totales de un volumen
   * Cada instantánea de ese volumen incluye los detalles siguientes: 
      - Nombre de la instantánea 
      - Hora de creación 
      - Tamaño de la instantánea
      - Frecuencia de la instantánea
      - Identificador de copia de seguridad de HANA asociado con esa instantánea, si procede
- **azure\_hana\_snapshot\_delete.pl**: este script elimina una instantánea de almacenamiento o un conjunto de instantáneas. Puede usar el identificador de copia de seguridad de SAP HANA que se encuentra en Studio HANA o el nombre de la instantánea de almacenamiento. Actualmente, el identificador de la copia de seguridad solo está asociado a las instantáneas creadas para los volúmenes data/log/shared de HANA. En caso contrario, si se escribe el identificador de la instantánea, busca todas las instantáneas que coincidan con dicho identificador.  
- **testHANAConnection.pl**: este script prueba la conexión con la instancia de SAP HANA y es necesario para configurar las instantáneas de almacenamiento.
- **testStorageSnapshotConnection.pl**: este script tiene dos propósitos. En primer lugar, garantiza que la unidad de instancia grande de HANA que ejecuta los scripts tiene acceso a la máquina virtual de almacenamiento asignada y a la interfaz de la instantánea de almacenamiento de instancia grande de HANA. La segunda finalidad es crear una instantánea temporal para la instancia de HANA que se prueba. Este script se debe ejecutar para cada instancia de HANA en un servidor a fin de garantizar que los scripts de copia de seguridad funcionan según lo previsto.
- **removeTestStorageSnapshot.pl**: este script elimina la instantánea de prueba que se creó con el script **testStorageSnapshotConnection.pl**. 
- **HANABackupCustomerDetails.txt**: este archivo es un archivo de configuración modificable que se debe modificar para adaptarlo a la configuración de SAP HANA.

 
El archivo HANABackupCustomerDetails.txt es el archivo de control y configuración del script que ejecuta las instantáneas de almacenamiento. Ajuste el archivo según sus finalidades y la configuración. Debe haber recibido el **nombre de la copia de seguridad del almacenamiento** y la **dirección IP de almacenamiento** de SAP HANA en Azure Service Management cuando se implementaron las instancias. No puede modificar la secuencia, el orden ni el espaciado de ninguna de las variables de este archivo. De lo contrario, los scripts no se ejecutarán correctamente. Además, recibió la dirección IP del nodo de escalado vertical o del nodo maestro (si se trata de escalado horizontal) desde SAP HANA en Azure Service Management. También conoce el número de instancia de HANA que obtuvo durante la instalación de SAP HANA. Ahora necesita agregar un nombre de copia de seguridad al archivo de configuración.

Para una implementación de escalado vertical u horizontal, el archivo de configuración tendrá un aspecto similar al ejemplo siguiente una vez que rellene el nombre de la copia de seguridad del almacenamiento y la dirección IP del almacenamiento. También necesita rellenar los datos siguientes en el archivo de configuración:
- Nodo único o dirección IP del nodo maestro
- Número de instancia de HANA
- Nombre de la copia de seguridad 
    
```
#Provided by Microsoft Service Management
Storage Backup Name: client1hm3backup
Storage IP Address: 10.240.20.31
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 
Node 1 HANA instance number:
Node 1 HANA userstore Name:
```

>[!NOTE]
>Actualmente, solo se usa la información del nodo 1 en el script de instantánea de almacenamiento de HANA real. Se recomienda que pruebe el acceso en todos los nodos de HANA para que, si el nodo maestro de copia de seguridad cambia en algún momento, tenga seguro que cualquier otro pueda ocupar su lugar mediante la modificación de la información del nodo 1.

Después de colocar todos los datos de configuración en el archivo HANABackupCustomerDetails.txt, es necesario comprobar si las configuraciones son correctas con respecto a los datos de la instancia de HANA. Use el script `testHANAConnection.pl`. Este script es independiente de una configuración de escalado vertical o escalado horizontal de SAP HANA.

```
testHANAConnection.pl
```

Si se trata de una configuración de escalado horizontal de SAP HANA, asegúrese de que la instancia de HANA principal tiene acceso a todos los servidores e instancias de HANA necesarios. En el script de prueba no hay parámetros, pero es preciso agregar los datos en el archivo de configuración HANABackupCustomerDetails.txt para que el script se ejecute correctamente. Solo se devuelven los códigos de error del comando shell, por lo que el script no puede comprobar los errores en todas las instancias. Aun así, el script proporciona algunos comentarios útiles que debe repasar.

Para ejecutar el script, escriba el siguiente comando:
```
 ./testHANAConnection.pl
```
Si el script obtiene correctamente el estado de la instancia de HANA, se mostrará un mensaje que indica que la conexión con HANA se ha establecido con éxito.


El paso siguiente es comprobar la conectividad con el almacenamiento según los datos que proporciona en el archivo de configuración HANABackupCustomerDetails.txt y, a continuación, ejecutar una instantánea de prueba. Antes de ejecutar el script `azure_hana_backup.pl`, debe ejecutar esta prueba. Si un volumen no contiene instantáneas, no podrá determinar si el volumen está vacío o si se produce algún error de SSH al obtener la información de la instantánea. Por este motivo, el script ejecuta dos pasos:

- Comprueba que es posible que los scripts accedan a las interfaces y a la máquina virtual de almacenamiento del inquilino a fin de ejecutar las instantáneas.
- Crea una instantánea de prueba, o ficticia, para cada volumen por instancia de HANA.

Por este motivo, la instancia de HANA se incluye como argumento. Si se produce un error en la ejecución, no es posible realizar la comprobación de errores para la conexión del almacenamiento. Incluso si no hay ninguna comprobación de errores, el script proporciona sugerencias útiles.

El script se ejecuta como se indica a continuación:
```
 ./testStorageSnapshotConnection.pl <HANA SID>
```
A continuación, el script intenta iniciar sesión en el almacenamiento utilizando la clave pública que se proporcionó en los pasos de configuración anteriores y con los datos configurados en el archivo HANABackupCustomerDetails.txt. Si el inicio de sesión se realiza correctamente, se muestra el contenido siguiente:

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

Si se produce algún problema al conectarse con la consola de almacenamiento, el resultado sería el siguiente:

```
**********************Checking access to Storage**********************
WARNING: Storage check status command 'volume show -type RW -fields volume' failed: 65280
WARNING: Please check the following:
WARNING: Was publickey sent to Microsoft Service Team?
WARNING: If passphrase entered while using tool, publickey must be re-created and passphrase must be left blank for both entries
WARNING: Ensure correct IP address was entered in HANABackupCustomerDetails.txt
WARNING: Ensure correct Storage backup name was entered in HANABackupCustomerDetails.txt
WARNING: Ensure that no modification in format HANABackupCustomerDetails.txt like additional lines, line numbers or spacing
WARNING: ******************Exiting Script*******************************
```

Después de un inicio de sesión correcto en las interfaces de la máquina virtual de almacenamiento, el script continúa con la fase 2 y crea una instantánea de prueba. El resultado se muestra aquí para una configuración de escalado horizontal de tres nodos de SAP HANA:

```
**********************Creating Storage snapshot**********************
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_shared_hm3_t020_vol ...
Snapshot created successfully.
```

Si la instantánea de prueba se ejecutó correctamente con el script, puede continuar con la configuración de las instantáneas de almacenamiento reales. Si no se realiza correctamente, investigue los problemas antes de continuar. Se debe conservar la instantánea de prueba hasta que se hayan realizado las primeras instantáneas reales.


### <a name="step-7-perform-snapshots"></a>Paso 7: Realizar instantáneas

A medida que finalizan todos los pasos de preparación, puede comenzar a establecer la configuración de instantánea de almacenamiento real. El script que se va a programar funciona con configuraciones de escalado vertical o escalado horizontal de SAP HANA. Debe programar la ejecución de los scripts a través de cron. 

Se pueden crear tres tipos de copias de seguridad de instantánea:
- **HANA**: copia de seguridad de la instantánea combinada en la que la instantánea coordinada abarca los volúmenes que contienen /hana/data y /hana/shared (que también contiene /usr/sap). Es posible realizar una sola restauración de archivos a partir de esta instantánea.
- **Registros**: copia de seguridad de la instantánea del volumen /hana/logbackups. Ninguna instantánea de HANA se desencadena para ejecutar esta instantánea de almacenamiento. Este volumen de almacenamiento es el volumen pensado para contener las copias de seguridad del registro de transacciones de SAP HANA. Las copias de seguridad del registro de transacciones de SAP HANA se realizan más frecuentemente para limitar el crecimiento del registro y evitar la posible pérdida de datos. Es posible realizar una sola restauración de archivos a partir de esta instantánea. No debe reducir la frecuencia por debajo de cinco minutos.
- **Arranque**: instantánea del volumen que contiene el número de unidad lógica (LUN) de arranque de la instancia grande de HANA. La copia de seguridad de esta instantánea solo es posible con las SKU de tipo I de las instancias grandes de HANA. No es posible realizar restauraciones de archivos únicas a partir de la instantánea del volumen que contiene el LUN de arranque. En las SKU de tipo II de instancias grandes de HANA, puede realizar la copia de seguridad de nivel de sistema operativo y restaurar también archivos individuales. Para más información, consulte el documento "[Copia de seguridad del sistema operativo para SKU de tipo II](os-backup-type-ii-skus.md)".


La sintaxis de llamada de estos tres tipos distintos de instantáneas es la siguiente:
```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <HANA SID> manual 30

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <HANA SID> manual 30

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot none manual 30

```

Es necesario especificar los parámetros siguientes:

- El primer parámetro caracteriza el tipo de la copia de seguridad de la instantánea. Los valores permitidos son **hana**, **logs** y **boot**. 
- El segundo parámetro es **HANA SID** (como HM3) o **none**. Si el primer valor de los parámetros proporcionado es **hana** o **logs**, el valor de este parámetro es **HANA SID** (como HM3), y para la copia de seguridad del volumen de arranque, el valor es **none**. 
- El tercer parámetro es una instantánea o etiqueta de copia de seguridad para el tipo de instantánea. Tiene dos finalidades. La primera finalidad le corresponde a usted y consiste en asignarle un nombre, para que sepa de qué tratan estas instantáneas. La segunda finalidad es para el script azure\_hana\_backup.pl para determinar el número de instantáneas de almacenamiento que se conservan bajo esa etiqueta específica. Si programa dos copias de seguridad de instantánea de almacenamiento del mismo tipo (como **hana**), con dos etiquetas diferentes y define que deben conservarse 30 instantáneas para cada una de ellas, terminará con 60 instantáneas de almacenamiento de los volúmenes afectados. 
- El cuarto parámetro define la retención de las instantáneas de manera indirecta al definir el número de instantáneas con el mismo prefijo de instantánea (etiqueta) que se deben conservar. Este parámetro es importante para una ejecución programada a través de cron. 

En el caso de escalado horizontal, el script realiza algunas comprobaciones adicionales para asegurarse de que puede tener acceso a todos los servidores de HANA. El script también comprueba que todas las instancias de HANA devuelven el estado adecuado de las instancias, antes de crear una instantánea de SAP HANA. La instantánea de SAP HANA va seguida de una instantánea de almacenamiento.

La ejecución del script `azure_hana_backup.pl` crea la instantánea de almacenamiento en las tres fases siguientes:

1. Ejecuta una instantánea de SAP HANA
2. Ejecuta una instantánea de almacenamiento
3. Quita la instantánea de SAP HANA que se creó antes de la ejecución de la instantánea de almacenamiento

Para ejecutar el script, lo llama desde la carpeta ejecutable de HDB en la que se copió. 

El período de retención se administra con el número de instantáneas que se envían como parámetro al ejecutar el script (como **30**, que se muestra arriba). Por lo tanto, la cantidad de tiempo que cubren las instantáneas de almacenamiento es una función de dos cosas: el período de ejecución y el número de instantáneas que se envían como parámetro al ejecutar el script. Si el número de instantáneas que se mantienen supera el número del parámetro de la llamada del script, la instantánea de almacenamiento más antigua de la misma etiqueta (en nuestro caso anterior, **manual**), se eliminarán antes de ejecutar una nueva instantánea. El número que proporcione como último parámetro de la llamada es el que puede usar para controlar el número de instantáneas que se conservan. Con este número también puede controlar indirectamente el espacio en disco que se usa para las instantáneas. 

> [!NOTE]
>En cuanto cambie la etiqueta, el recuento se reinicia. Esto significa que el etiquetado se debe realizar de forma estricta de manera que las instantáneas no se eliminen accidentalmente.

### <a name="snapshot-strategies"></a>Estrategias de instantánea
La frecuencia de las instantáneas para los distintos tipos depende de si usa o no la funcionalidad de recuperación ante desastres de instancias grandes de HANA. La funcionalidad de recuperación ante desastres de instancias grandes de HANA se basa en las instantáneas de almacenamiento. Confiar en instantáneas de almacenamiento podría requerir algunas recomendaciones especiales en cuanto a la frecuencia y los períodos de ejecución de las instantáneas de almacenamiento. 

En las consideraciones y recomendaciones siguientes, se supone que *no* se usa la funcionalidad de recuperación ante desastres que ofrecen las instancias grandes de HANA. En su lugar, las instantáneas de almacenamiento se usan como una forma de tener copias de seguridad y poder proporcionar la recuperación a un momento dado durante los últimos 30 días. Dadas las limitaciones del número de instantáneas y el espacio, los clientes han considerado los siguientes requisitos:

- El tiempo de recuperación para una recuperación a un momento dado.
- El espacio utilizado.
- Los objetivos de punto de recuperación y de tiempo de recuperación para una posible recuperación ante desastres.
- La ejecución eventual de copias de seguridad completas de la base de datos de HANA en discos. Cada vez que se realiza una copia de seguridad completa de la base de datos en discos o la interfaz de **backint**, se produce un error en la ejecución de las instantáneas de almacenamiento. Si planea ejecutar copias de seguridad de la base de datos completa sobre las instantáneas del almacenamiento, asegúrese de que la ejecución de dichas instantáneas está deshabilitada durante este tiempo.
- El número de instantáneas por volumen está limitado a 255.


En el caso de los clientes que no usan la funcionalidad de recuperación ante desastres de las instancias grandes de HANA, el período de la instantánea es menos frecuente. En dichos casos, vemos clientes que realizan las instantáneas combinadas en /hana/data y /hana/shared (incluye /usr/sap) en períodos de 12 o 24 horas y mantienen esas instantáneas de modo que abarquen todo un mes. Lo mismo ocurre con las instantáneas del volumen de copia de seguridad del registro. Sin embargo, la ejecución de las copias de seguridad del registro de transacciones de SAP HANA en el volumen de la copia de seguridad del registro tiene lugar en períodos de cinco a quince minutos.

Le animamos a realizar instantáneas de almacenamiento programadas mediante cron. También le recomendamos que utilice el mismo script para todas las copias de seguridad y necesidades de recuperación ante desastres. Debe modificar las entradas de script para que coincidan con los distintos tiempos de copia de seguridad solicitados. Todas estas instantáneas se programan de forma diferente en cron en función de su tiempo de ejecución: cada hora, cada 12 horas, a diario o semanalmente. 

Un ejemplo de programación cron en /etc/crontab podría ser la siguiente:
```
00 1-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 46
10 00 * * *  ./azure_hana_backup.pl hana HM3 dailyhana 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log HM3 dailylogback 28
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```
En el ejemplo anterior, se trata de una instantánea combinada por hora que abarca los volúmenes que contienen las ubicaciones /hana/data y /hana/shared (incluye /usr/sap). Este tipo de instantánea se podría usar para una recuperación a un momento dado más rápida en un período de dos días antes. Además, en esos volúmenes hay una instantánea diaria. Por tanto, tiene dos días de cobertura por instantáneas por hora más cuatro semanas de cobertura por instantáneas diarias. Además, se crea una copia de seguridad del volumen de la copia de seguridad del registro de transacciones una vez cada día. Estas copias de seguridad también se conservan durante cuatro semanas. Como se ve en la tercera línea de crontab, la copia de seguridad del registro de transacciones de HANA está programada para ejecutarse cada cinco minutos. Los minutos iniciales de los distintos trabajos de cron que ejecutan las instantáneas de almacenamiento son escalonados, por lo que esas instantáneas no se ejecutan todas a la vez en un momento dado. 

En el ejemplo siguiente, cada hora se realiza una instantánea combinada que abarca los volúmenes que contienen las ubicaciones /hana/data y /hana/shared (incluido /usr/sap). Las instantáneas se conservan durante dos días. Las instantáneas de los volúmenes de copia de seguridad del registro de transacciones se ejecutan cada cinco minutos y se conservan durante cuatro horas. Como antes, la copia de seguridad del registro de transacciones de HANA se programa para ejecutarse cada cinco minutos. La instantánea del volumen de la copia de seguridad del registro de transacciones se realiza con un retraso de dos minutos una vez que se inicia la copia de seguridad del registro de transacciones. En circunstancias normales, la copia de seguridad del registro de transacciones de SAP HANA debería completarse en esos dos minutos. Como antes, una instantánea de almacenamiento crea una copia de seguridad al día del volumen que contiene el LUN de arranque y se conserva durante cuatro semanas.

```
10 0-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log HM3 logback 48
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```

En el siguiente gráfico se ilustran las secuencias del ejemplo anterior, excepto el LUN de arranque:

![Relación entre copias de seguridad e instantáneas](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA realiza escrituras normales en el volumen /hana/log para documentar los cambios confirmados en la base de datos. De manera regular, SAP HANA escribe un punto de retorno en el volumen /hana/data. Tal como se especifica en crontab, una copia de seguridad del registro de transacciones de SAP HANA se ejecuta cada cinco minutos. También se ve que una instantánea de SAP HANA se ejecuta cada hora como resultado del desencadenamiento de una instantánea de almacenamiento combinada sobre los volúmenes /hana/data y /hana/shared. Una vez que la instantánea de HANA se realiza correctamente, se ejecuta la instantánea de almacenamiento combinada. Tal como se indica en crontab, la instantánea de almacenamiento en el volumen /hana/logbackup se ejecuta cada cinco minutos alrededor de dos minutos después de la copia de seguridad del registro de transacciones de HANA.


>[!IMPORTANT]
> El uso de instantáneas de almacenamiento para las copias de seguridad de SAP HANA solo resulta valioso cuando las instantáneas se realizan junto con las copias de seguridad del registro de transacciones de SAP HANA. Es preciso que dichas copias de seguridad del registro de transacciones puedan cubrir los períodos entre las instantáneas de almacenamiento. 

Si ha adquirido un compromiso con los usuarios de una recuperación a un momento dado de treinta días, haga lo siguiente:

- En casos extremos, necesita poder acceder a una instantánea de almacenamiento combinada en /hana/data y /hana/shared, que tiene una antigüedad de 30 días.
- Tenga copias de seguridad del registro de transacciones contiguas que abarquen el tiempo entre cualquiera de las instantáneas del almacenamiento combinado. Por tanto, la instantánea de mayor antigüedad del volumen de copia de seguridad del registro de transacciones debe tener 30 días. Este no es el caso si copia las copias de seguridad del registro de transacciones en otro recurso compartido NFS ubicado en Azure Storage. En ese caso, podría extraer copias de seguridad del registro de transacciones anteriores de ese recurso compartido NFS.

Para beneficiarse de las instantáneas de almacenamiento y la posible replicación de almacenamiento de las copias de seguridad del registro de transacciones, es necesario cambiar la ubicación donde SAP HANA escribe las copias de seguridad del registro de transacciones. Puede realizar este cambio en HANA Studio. Si bien SAP HANA crea automáticamente copias de seguridad de segmentos de registros completos, se debe especificar un intervalo de copia de seguridad de registros para que sea determinista. Hay que tener esto especialmente en cuenta cuando se usa la opción de recuperación ante desastres porque habitualmente querrá ejecutar las copias de seguridad de registros con un período determinista. En el caso siguiente, se tomó un intervalo de copia de seguridad de registros de quince minutos.

![Programe los registros de copia de seguridad de SAP HANA en SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Puede elegir que las copias de seguridad se realicen con una frecuencia superior a 15 minutos. Esto se realiza frecuentemente junto con la recuperación ante desastres. Algunos clientes realizan copias de seguridad del registro de transacciones cada cinco minutos.  

Si nunca se realizó una copia de seguridad de la base de datos, el último paso es crear una copia de seguridad en archivo para crear una entrada de copia de seguridad individual que debe existir en el catálogo de copias de seguridad. En caso contrario, SAP HANA no podrá iniciar las copias de seguridad del registro especificadas.

![Realizar una copia de seguridad en archivo para crear una entrada de copia de seguridad individual](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Después de ejecutar correctamente las primeras instantáneas de almacenamiento, también puede eliminar la instantánea de prueba que se ejecutó en el paso 6. Para ello, ejecute el script `removeTestStorageSnapshot.pl`:
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Supervisión del número y tamaño de instantáneas del volumen de disco

En un volumen de almacenamiento concreto se puede supervisar tanto el número de instantáneas como el consumo de almacenamiento de esas instantáneas. El comando `ls` no muestra los archivos o el directorio de instantáneas. Sin embargo, el comando `du` del sistema operativo Linux muestra detalles sobre esas instantáneas de almacenamiento debido a que se almacenan en los mismos volúmenes. El comando se puede usar con las opciones siguientes:

- `du –sh .snapshot`: proporciona un total de todas las instantáneas del directorio de instantáneas.
- `du –sh --max-depth=1`: enumera todas las instantáneas que se guardan en la carpeta **.snapshot** y el tamaño de cada una de ellas.
- `du –hc`: proporciona el tamaño total que usan todas las instantáneas.

Utilice estos comandos para asegurarse de que las instantáneas que tomadas y almacenadas no consumen todo el almacenamiento de los volúmenes.

>[!NOTE]
>Las instantáneas del LUN de arranque no son visibles con los comandos anteriores.

### <a name="getting-details-of-snapshots"></a>Detalles de las instantáneas
Para más detalles sobre las instantáneas, también puede utilizar el script `azure_hana_snapshot_details.pl`. Este script se puede ejecutar en cualquier ubicación si hay un servidor activo en la ubicación de recuperación ante desastres. El script proporciona la siguiente salida desglosada por cada volumen que contiene instantáneas: 
   * Tamaño de las instantáneas totales de un volumen
   * Cada instantánea de ese volumen incluye los detalles siguientes: 
      - Nombre de la instantánea 
      - Hora de creación 
      - Tamaño de la instantánea
      - Frecuencia de la instantánea
      - Identificador de copia de seguridad de HANA asociado con esa instantánea, si procede

La sintaxis de ejecución del script es la siguiente:

```
./azure_hana_snapshot_details.pl 
```

Dado que el script intenta recuperar el identificador de copia de seguridad de HANA, necesita conectarse a la instancia de SAP HANA. Esta conexión requiere que el archivo de configuración HANABackupCustomerDetails.txt esté establecido correctamente. Una salida de dos instantáneas en un volumen podría ser la siguiente:

```
**********************************************************
****Volume: hana_shared_SAPTSTHDB100_t020_vol       ***********
**********************************************************
Total Snapshot Size:  411.8MB
----------------------------------------------------------
Snapshot:   customer.2016-09-20_1404.0
Create Time:   "Tue Sep 20 18:08:35 2016"
Size:   2.10MB
Frequency:   customer 
HANA Backup ID:   
----------------------------------------------------------
Snapshot:   customer2.2016-09-20_1532.0
Create Time:   "Tue Sep 20 19:36:21 2016"
Size:   2.37MB
Frequency:   customer2
HANA Backup ID:   
```


### <a name="file-level-restore-from-a-storage-snapshot"></a>Restauración de nivel de archivo desde una instantánea de almacenamiento
Para los tipos de instantánea hana y logs, es posible acceder a las instantáneas directamente en los volúmenes del directorio **.snapshot**. Hay un subdirectorio para cada una de las instantáneas. Debería poder copiar cada archivo cubierto por la instantánea en el estado que tenía en el momento de la instantánea desde ese subdirectorio a la estructura de directorios real.

>[!NOTE]
>La restauración de archivos única no funciona para las instantáneas del LUN de arranque. El directorio **.snapshot** no se expone en el LUN de arranque. 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Reducción del número de instantáneas en un servidor

Como ya se ha explicado, es posible reducir el número de etiquetas concretas almacenadas. Los dos últimos parámetros del comando para iniciar una instantánea son la etiqueta y el número de instantáneas que se desean conservar.

```
./azure_hana_backup.pl hana HM3 hanadaily 30
```

En el ejemplo anterior, la etiqueta de la instantánea es **customer** y el número de instantáneas con esta etiqueta que se conservarán es **30**. A medida que responda al consumo de espacio en el disco es posible que desee reducir el número de instantáneas que se almacenan. La manera más sencilla de reducir el número de instantáneas a 15, por ejemplo, es ejecutar el script con el último parámetro establecido en **15**:

```
./azure_hana_backup.pl hana HM3 hanadaily 15
```

Si ejecuta el script con la configuración anterior, se reducen las instantáneas, con lo que el número de ellas, incluida la nueva instantánea del almacenamiento, es 15. Se conservan las 15 instantáneas más recientes, mientras que se eliminan las 15 instantáneas más antiguas.

 >[!NOTE]
 > Este script reduce el número de instantáneas solo si hay instantáneas que tienen más de una hora de antigüedad. Este script no elimina las instantáneas que tienen menos de una hora de antigüedad. Estas restricciones están relacionadas con la funcionalidad opcional de recuperación ante desastres que se ofrece.

Si ya no desea mantener un conjunto de instantáneas con una etiqueta de copia de seguridad específica **hanadaily** en los ejemplos de sintaxis, es posible ejecutar el script con **0** como el número de retención. Esto quita todas las instantáneas que coincidan con esa etiqueta. Sin embargo, la eliminación de todas las instantáneas puede afectar a las funcionalidades de recuperación ante desastres.

Otra posibilidad de eliminar instantáneas específicas es usar el script `azure_hana_snapshot_delete.pl`. Este script está diseñado para eliminar una instantánea o un conjunto de instantáneas, ya sea mediante el identificador de la copia de seguridad de HANA como aparece en HANA Studio, o bien a través del nombre mismo de la instantánea. Actualmente, el identificador de la copia de seguridad solo está asociado a las instantáneas creadas para el tipo de instantánea **hana**. Las copias de seguridad de instantánea de tipo **logs** y **boot** no realizan una instantánea de SAP HANA. Por tanto, no se encontrarán identificadores de copia de seguridad para esas instantáneas. Si se escribe el nombre de la instantánea, busca todas las instantáneas en los distintos volúmenes que coincidan con el nombre de instantánea que se escribió. La sintaxis de llamada del script es:

```
./azure_hana_snapshot_delete.pl 

```

Ejecute el script como usuario **raíz**.

Si selecciona una instantánea, tiene la capacidad de eliminar cada instantánea individualmente. Primero suministra el volumen que contiene la instantánea y luego el nombre de la instantánea. La instantánea se elimina si está en ese volumen y tiene más de una hora de antigüedad. Puede ejecutar el script `azure_hana_snapshot_details` para buscar los nombres de los volúmenes y los nombres de las instantáneas. 

>[!IMPORTANT]
>Si hay datos que solo existen en la instantánea que se elimina, los datos se perderán para siempre si ejecuta la eliminación.

   

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>Recuperación a la instantánea más reciente de HANA

Si se encuentra ante un escenario de bajada de producción, el proceso de recuperación a partir de una instantánea de almacenamiento se puede iniciar como un incidente de cliente con Soporte técnico de Microsoft Azure. Es una cuestión muy urgente si los datos se eliminaran en un sistema de producción y la única manera de volver a obtenerlos fuera a través de la restauración de la base de datos de producción.

En otra situación, una recuperación a un momento dado podría ser poco urgente y se podría planear con días de antelación. Para planearlo, se puede usar SAP HANA en Azure Service Management, en lugar de generar un problema de prioridad alta. Por ejemplo, podría estar pensando en actualizar el software de SAP mediante la aplicación de un nuevo paquete de mejora. A continuación, necesita volver a una instantánea que representa el estado antes de la actualización del paquete mejora.

Antes de enviar la solicitud, necesita prepararse. Después, el equipo de SAP HANA en Azure Service Management puede encargarse de la solicitud y proporcionar los volúmenes restaurados. A partir de ese momento, restaurará la base de datos de HANA con las instantáneas. Aquí se muestra cómo preparar la solicitud:

>[!NOTE]
>La interfaz de usuario podría variar con respecto a las capturas de pantalla siguientes, en función de la versión de SAP HANA que use.

1. Decida qué instantánea desea restaurar. Salvo que se indique lo contrario, solo se restaura el volumen de datos/HANA. 

2. Cierre la instancia de HANA.

 ![Cerrar la instancia de HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Desmonte los volúmenes de datos de todos los nodos de la base de datos de HANA. Si los volúmenes de datos siguen montados en el sistema operativo, se produce un error en la restauración de la instantánea.
 ![Desmontar los volúmenes de datos de todos los nodos de la base de datos de HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Abra una solicitud de soporte técnico de Azure para enviar la orden restauración de una instantánea concreta.

 - Durante la restauración: SAP HANA en Azure Service Management podría pedirle que asista a una llamada de conferencia para garantizar la coordinación, la comprobación y la confirmación de que se restaure la instantánea de almacenamiento correcta. 

 - Después de la restauración: SAP HANA en Azure Service Management le enviará una notificación cuando la instantánea del almacenamiento se haya restaurado.

5. Una vez que el proceso de restauración se haya completado, vuelva a montar todos los volúmenes de datos.

 ![Volver a montar todos los volúmenes de datos](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. Seleccione las opciones de recuperación en SAP HANA Studio, en caso de que no aparezcan automáticamente al volver a conectarse a base de datos de HANA a través de HANA Studio. En el siguiente ejemplo se muestra una operación de restauración a la instantánea de HANA más reciente. Una instantánea de almacenamiento inserta una instantánea de HANA. Si restaura a la instantánea de almacenamiento más reciente, debe ser la instantánea de HANA más reciente. (Si restaura a una instantánea de almacenamiento más antigua, será preciso que encuentre la instantánea de HANA por la hora en que se tomó la instantánea de almacenamiento.)

 ![Seleccionar las opciones de recuperación en SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. Seleccione **Recover the database to a specific data backup or storage snapshot** (Recuperar la base en una instantánea del almacenamiento o de copia de seguridad de datos concreta).

 ![Ventana Specify Recovery Type (Especificar tipo de recuperación)](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. Seleccione **Specify backup without catalog** (Especificar copia de seguridad sin catálogo).

 ![Ventana Specify Backup Location (Especificar ubicación de copia de seguridad)](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. En la lista **Destination Type** (Tipo de destino), seleccione **Snapshot** (Instantánea).

 ![Ventana Specify the Backup to Recover (Especificar la copia de seguridad que se recuperará)](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. Seleccione **Finish** (Finalizar) para iniciar el proceso de recuperación.

 ![Seleccionar "Finish" (Finalizar) para iniciar el proceso de recuperación](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. La base de datos HANA se restaura y recupera en la instantánea de HANA incluida en la instantánea del almacenamiento.

 ![La base de datos HANA se restaura y recupera en la instantánea de HANA.](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>Recuperación al estado más reciente

El proceso siguiente restaura la instantánea de HANA que se incluye en la de almacenamiento. Luego, se restauran las copias de seguridad del registro de transacciones al estado más reciente de la base de datos antes de restaurar la instantánea de almacenamiento.

>[!IMPORTANT]
>Antes de continuar, asegúrese de que dispone de una cadena completa y contigua de copias de seguridad del registro de transacciones. Sin estas copias de seguridad, no podrá restaurar el estado actual de la base de datos.

1. Complete los pasos 1 a 6 de [Recuperación a la instantánea más reciente de HANA](#recovering-to-the-most-recent-hana-snapshot).

2. Elija **Recover the database to its most recent state** (Recuperar la base de datos a su estado más reciente).

 ![Seleccione "Recover the database to its most recent state" (Recuperar la base de datos a su estado más reciente).](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. Especifique la ubicación de las copias de seguridad de registros de HANA más recientes. La ubicación tiene que contener todas las copias del registro de transacciones de HANA desde la instantánea de HANA al estado más reciente.

 ![Especifique la ubicación de las copias de seguridad de registros de HANA más recientes.](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. Elija una copia de seguridad como base desde la que recuperar la base de datos. En el ejemplo, la instantánea de HANA que aparece en la captura de pantalla es la instantánea de HANA que se incluyó en la instantánea de almacenamiento. 

 ![Elija una copia de seguridad como base desde la que recuperar la base de datos.](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. Desactive la casilla **Use Delta Backups** (Usar copias de seguridad diferenciales) si no existe entre el momento de la instantánea de HANA y el estado más reciente.

 ![Desactive la casilla "Use Delta Backups" (Usar copias de seguridad diferenciales) si no hay datos diferenciales.](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. En la pantalla de resumen, seleccione **Finish** (Finalizar) para iniciar el procedimiento de restauración.

 ![Haga clic en "Finish" (Finalizar) en la pantalla de resumen.](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>Recuperación a un momento dado
Para realizar la recuperación a un momento dado entre la instantánea de HANA (incluida en la instantánea del almacenamiento) y una que sea posterior a la recuperación a un momento dado de la instantánea de HANA, siga estos pasos:

1. Asegúrese de que dispone de todas las copias de seguridad del registro de transacciones desde la instantánea de HANA hasta el momento al que desea recuperar.
2. Inicie el procedimiento de [Recuperación al estado más reciente](#recovering-to-the-most-recent-state).
3. En el paso 2 del procedimiento, en la ventana **Specify Recovery Type** (Especificar tipo de recuperación), seleccione **Recover the database to the following point in time** (Recuperar la base de datos al siguiente momento dado) y especifique el momento dado. Luego complete los pasos del 3 al 6.

### <a name="monitoring-the-execution-of-snapshots"></a>Supervisión de la ejecución de instantáneas

Cuando se usan las instantáneas de almacenamiento de instancias grandes de HANA, también se debe supervisar la ejecución de esas instantáneas de almacenamiento. El script que ejecuta una instantánea del almacenamiento escribe la salida en un archivo y, luego, guarda en la misma ubicación que los scripts Perl. Se escribe un archivo independiente para cada instantánea de almacenamiento. La salida de cada archivo muestra claramente las distintas fases que ejecuta el script de la instantánea:

1. Busque los volúmenes que necesitan crear una instantánea.
2. Busque las instantáneas tomadas de estos volúmenes.
3. Elimine instantáneas eventuales existentes para ajustarse al número de instantáneas especificado.
4. Cree una instantánea de SAP HANA.
5. Cree la instantánea de almacenamiento en los volúmenes.
6. Elimine la instantánea de SAP HANA.
7. Cambie el nombre de la instantánea más reciente a **.0**.

La parte más importante del script cab identificada es esta parte:
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
En este ejemplo puede ver la forma en que el script registra la creación de la instantánea de HANA. En el caso del escalado horizontal, este proceso se inicia en el nodo maestro. El nodo maestro inicia la creación sincrónica de las instantáneas de SAP HANA en cada uno de los nodos de trabajado. Después, se toma la instantánea de almacenamiento. Después de la ejecución correcta de las instantáneas del almacenamiento, se elimina la instantánea de HANA. La eliminación de la instantánea HANA se inicia desde el nodo maestro.


## <a name="disaster-recovery-principles"></a>Principios de la recuperación ante desastres
Con las instancias grandes de HANA, se ofrece una funcionalidad de recuperación ante desastres entre las marcas de instancias grandes de HANA en las distintas regiones de Azure. Por ejemplo, si implementa unidades de instancia grande de HANA en la región Oeste de EE. UU. de Azure, puede usar unidades de instancia grande de HANA en la región Este de EE. UU. como unidades de recuperación ante desastres. Como se mencionó anteriormente, la recuperación ante desastres no se configura automáticamente porque es necesario que se pague por otra unidad de instancia grande de HANA en la región de la recuperación ante desastres. El programa de instalación de la recuperación ante desastres funciona tanto en configuraciones de escalado vertical como de escalado horizontal. 

En los escenarios implementados hasta ahora, nuestros clientes usan la unidad de la región de recuperación ante desastres para ejecutar sistemas que no son de producción que usan una instancia instalada de HANA. La unidad de instancia grande de HANA debe tener la misma SKU que la que se usa con fines de producción. La configuración de discos entre la unidad del servidor en la región de producción de Azure y la región de recuperación ante desastres es la siguiente:

![Configuración de la recuperación ante desastres desde el punto de vista de los discos](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Como se muestra en este gráfico de información general, necesita solicitar un segundo conjunto de volúmenes de disco. Los volúmenes de disco de destino tienen el mismo tamaño que los volúmenes de producción para la instancia de producción en las unidades de recuperación ante desastres. Estos volúmenes de disco están asociados a la unidad del servidor de instancia grande de HANA en el sitio de recuperación ante desastres. Los siguientes volúmenes se replican desde la región de producción al sitio de recuperación ante desastres:

- /hana/data
- /hana/logbackups 
- /hana/shared (incluye /usr/sap)

El volumen /hana/log no se replica porque el registro de transacciones de SAP HANA no es necesario en la forma en que se realiza la restauración a partir de esos volúmenes. 

La base de la funcionalidad de recuperación ante desastres ofrecida es la funcionalidad de replicación de almacenamiento que se brinda en la infraestructura de instancias grandes de HANA. La funcionalidad que se usa en el almacenamiento no es un flujo constante de los cambios que se replican de manera asincrónica a medida que se realizan los cambios en el volumen de almacenamiento. En su lugar, se trata de un mecanismo que se basa en el hecho de que las instantáneas de estos volúmenes se crean de manera habitual. La diferencia entre una instantánea ya replicada y una instantánea nueva que todavía no se replica luego se transfiere al sitio de recuperación ante desastres a los volúmenes de disco de destino.  Estas instantáneas se almacenan en los volúmenes y, en el caso de una conmutación por error de recuperación ante desastres, deben restaurarse en esos volúmenes.  

La primera transmisión de los datos completos del volumen debe ser antes de que la cantidad de datos sea menor que las diferencias entre las instantáneas. Como resultado, los volúmenes que se encuentran en el sitio de recuperación ante desastres contienen cada una de las instantáneas de volumen realizadas en el sitio de producción. A la larga, esto permite usar ese sistema de recuperación ante desastres para ir a un estado anterior con el fin de recuperar los datos perdidos sin tener que revertir el sistema de producción.

En casos donde se usa la replicación del sistema de HANA como funcionalidad de alta disponibilidad en el sitio de producción, solo se replican los volúmenes de la instancia de nivel 2 (o réplica). Esta configuración podría provocar un retraso en la replicación del almacenamiento en el sitio de recuperación ante desastres si mantiene o da de baja la unidad del servidor de la réplica secundaria (nivel 2) o la instancia de SAP HANA en esta unidad. 

>[!IMPORTANT]
>Al igual que lo que ocurre con la replicación del sistema de HANA de varios niveles, apagar la unidad del servidor o la instancia de HANA de nivel 2 bloquea la replicación en el sitio de recuperación ante desastres cuando se use la funcionalidad de recuperación ante desastres de instancias grandes de HANA.


>[!NOTE]
>La funcionalidad de replicación del almacenamiento de instancias grandes de HANA crea reflejos y réplicas de las instantáneas de almacenamiento. Por lo tanto, si no realiza instantáneas de almacenamiento como se indicó en la sección sobre copias de seguridad de este documento, no puede haber ninguna replicación en el sitio de recuperación ante desastres. La ejecución de la instantánea de almacenamiento es un requisito previo para la replicación del almacenamiento en el sitio de recuperación ante desastres.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Preparación del escenario de recuperación ante desastres
Se supone que tiene un sistema de producción que se ejecuta en instancias grandes de HANA en la región de Azure de producción. En la siguiente documentación, se supone que el SID de ese sistema HANA es "PRD". También se supone que tiene un sistema de no producción que se ejecuta en instancias grandes de HANA que, a su vez, se ejecutan en la región de Azure de recuperación ante desastres. Para la documentación, se supone que su SID es "TST". Por tanto, la configuración es la siguiente:

![Inicio de la configuración de recuperación ante desastres](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Si la instancia del servidor no se ha solicitado ya con el conjunto de volúmenes de almacenamiento adicional, SAP HANA en Azure Service Management asociará el conjunto de volúmenes adicional como un destino para la réplica de producción a la unidad de instancia grande de HANA en la que se está ejecutando la instancia de TST HANA. Para ello, debe proporcionar el SID de la instancia de HANA de producción. Después de que SAP HANA en Azure Service Management confirme la asociación de esos volúmenes, debe montar dichos volúmenes en la unidad de instancia grande de HANA.

![Paso siguiente en la configuración de recuperación ante desastres](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

El paso siguiente que debe dar es la instalación de la segunda instancia de SAP HANA en la unidad de instancia grande de HANA en la región de Azure de recuperación ante desastres, donde se ejecuta la instancia de TST HANA. La instancia de SAP HANA recién instalada debe tener el mismo SID. Los usuarios creados necesitan tener el mismo UID e identificador de grupo que la instancia de producción. Si la instalación se realizó correctamente, necesita:
- Detener la instancia de SAP HANA recién instalada en la unidad de instancia grande de HANA en la región de Azure de recuperación ante desastres.
- Desmontar los volúmenes PRD y ponerse en contacto con SAP HANA en Azure Service Management. Los volúmenes no pueden permanecer montados en la unidad porque no se puede acceder a ellos mientras funcionen como destino de replicación de almacenamiento.  

![Paso de la configuración de recuperación ante desastres antes de establecer la replicación](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

El equipo de operaciones va a establecer la relación de replicación entre los volúmenes PRD de la región de Azure de producción y los volúmenes PRD de la región de Azure de recuperación ante desastres.

>[!IMPORTANT]
>El volumen /hana/log no se replicará porque no es necesario para restaurar la base de datos de SAP HANA replicada a un estado coherente en el sitio de recuperación ante desastres.

El siguiente paso que debe dar es configurar o ajustar la programación de copia de seguridad de instantáneas de almacenamiento para llegar a su RTO y RPO en caso de desastre. Para minimizar el objetivo de punto de recuperación, establezca los siguientes intervalos de replicación en el servicio de instancias grandes de HANA:
- Los volúmenes que la instantánea combinada (tipo de instantánea = **hana**) abarca se replican cada 15 minutos en los destinos de volúmenes de almacenamiento equivalentes que se encuentran en el sitio de recuperación ante desastres.
- El volumen de copia de seguridad del registro de transacciones (tipo de instantánea = **logs**) se replica cada tres minutos en los destinos de volúmenes de almacenamiento equivalentes que se encuentran en el sitio de recuperación ante desastres.

Para minimizar el objetivo de punto de recuperación, configure lo siguiente:
- Realice una instantánea de almacenamiento de tipo **hana** (consulte el "Paso 7: Realizar instantáneas") entre cada treinta minutos y una hora.
- Realice copias de seguridad del registro de transacciones de SAP HANA cada cinco minutos.
- Realice una instantánea de almacenamiento de tipo **logs** entre cada cinco o quince minutos. Con este período de intervalo, debería poder alcanzar un RPO de alrededor de quince a 25 minutos.

Con esta configuración, la secuencia de copias de seguridad del registro de transacciones, las instantáneas de almacenamiento y la replicación del volumen de copia de seguridad del registro de transacciones de HANA y /hana/data y /hana/log, /hana/shared (incluye /usr/sap) podría tener el aspecto de los datos de este gráfico:

 ![Relación entre una instantánea de copia de seguridad del registro de transacciones y un reflejo de instantáneas en un eje de tiempo](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Para conseguir un RPO incluso mejor en el caso de la recuperación ante desastres, puede copiar las copias de seguridad del registro de transacciones de HAN fuera de SAP HANA en Azure (instancias grandes) y en la otra región de Azure. Para alcanzar esta reducción adicional del RPO, siga estos pasos:

1. Realizar una copia de seguridad del registro de transacciones de HANA tan frecuentemente como sea posible en /hana/logbackups.
2. Usar rsync para copiar las copias de seguridad del registro de transacciones al recurso compartido NFS hospedado en máquinas virtuales de Azure. Las VM se encuentran en redes virtuales de Azure en la región de producción de Azure y en las regiones de recuperación ante desastres. Debe conectar ambas redes virtuales de Azure al circuito conectando las instancias grandes de HANA de producción a Azure. Ver los gráficos de la sección [Consideraciones de red para recuperación ante desastres con instancias grandes de HANA](#Network-considerations-for-disaster-recovery-with-HANA-Large-Instances). 
3. Mantenga las copias de seguridad del registro de transacciones en la región de la VM asociada al almacenamiento exportado de NFS.
4. En caso de una conmutación por error ante desastres, complemente las copias de seguridad del registro de transacciones que encuentre en el volumen /hana/logbackups con copias de seguridad del registro de transacciones creadas más recientemente en el recurso compartido NFS del sitio de recuperación ante desastres. 
5. Ahora puede iniciar la restauración de una copia de seguridad del registro de transacciones a la copia de seguridad más reciente que se podría guardar en la región de recuperación ante desastres.

Cuando las operaciones de instancias grandes de HANA confirman que tienen la configuración de relación de replicación e inicia las copias de seguridad de instantánea de almacenamiento de ejecución, los datos empiezan a replicarse.

![Paso de la configuración de recuperación ante desastres antes de establecer la replicación](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

A medida que la replicación progresa, las instantáneas de los volúmenes PRD de las regiones de Azure de recuperación ante desastres no se restauran. Solo se almacenan. Si los volúmenes se montan en ese estado, representan el estado en el que desmontó esos volúmenes después de que la instancia de SAP HANA PRD se instalara en la unidad del servidor en la región de Azure de recuperación ante desastres. También representan las copias de seguridad de almacenamiento que no se han restaurado todavía.

En el caso de una conmutación por error, también puede optar por restaurar a una instantánea de almacenamiento más antigua en lugar de la instantánea de almacenamiento más reciente.

## <a name="disaster-recovery-failover-procedure"></a>Procedimiento de conmutación por error de recuperación ante desastres
En caso de que desee o necesite conmutar por error al sitio de recuperación ante desastres, debe interactuar con SAP HANA en el equipo de operaciones de Azure. Hasta ahora, los pasos del proceso son los siguientes:

1. Debido a que se ejecuta una instancia que no es de producción de HANA en la unidad de recuperación ante desastres de instancia grande de HANA, es necesario apagar esta instancia. Se supone que hay instalada previamente una instancia de producción de HANA inactiva.
2. Asegúrese de que no hay ningún proceso de SAP HANA en ejecución. Utilice el siguiente comando para esta comprobación: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. El resultado debería mostrar el proceso **hdbdaemon** en estado detenido y ningún otro proceso de HANA en ejecución o iniciado.
3. Determine qué nombre de instantánea o identificador de copia de seguridad de SAP HANA desea que tenga el sitio de recuperación ante desastres restaurado. En casos reales de recuperación ante desastres, esta instantánea suele ser la instantánea más reciente. Si necesita recuperar datos perdidos, elija una instantánea anterior.
4. Póngase en contacto con el soporte técnico de Azure a través de una solicitud de soporte técnico de alta prioridad y solicite la restauración de esa instantánea (nombre y fecha de la instantánea) o el identificador de copia de seguridad de HANA en el sitio de recuperación ante desastres. El valor predeterminado es que las operaciones solo restauran el volumen /hana/data. Si también desea tener los volúmenes /hana/logbackups, debe indicarlo específicamente. *No recomendamos restaurar el volumen /hana/shared.* En su lugar, debe elegir archivos específicos, como global.ini fuera del directorio **.snapshot** y sus subdirectorios después de volver a montar el volumen /hana/shared para PRD. En lo que respecta a las operaciones, se realizarán los pasos siguientes: a. Se detiene la replicación de instantáneas del volumen de producción en los volúmenes de recuperación ante desastres. Esto podría haber ocurrido ya si una interrupción en el sitio de producción es el motivo que necesita para una recuperación ante desastres.
    b. El nombre de la instantánea de almacenamiento o la instantánea con el identificador de copia de seguridad que eligió se restaura en los volúmenes de recuperación ante desastres.
    c. Después de la restauración, los volúmenes de recuperación ante desastres están disponibles para montarlos en las unidades de instancia grande de HANA en la región de recuperación ante desastres.
5. Monte los volúmenes de recuperación ante desastres en la unidad de instancia grande de HANA del sitio de recuperación ante desastres. 
6. Inicie la instancia de producción de SAP HANA hasta ahora inactiva.
7. Si eligió copiar registros de la copia de seguridad del registro de transacciones de manera adicional para reducir el tiempo de RPO, es necesario combinar esas copias de seguridad del registro de transacciones en el directorio /hana/logbackups de recuperación ante desastres que se acaba de montar. No sobrescriba copias de seguridad existentes. Simplemente copie las copias de seguridad más recientes que no se hayan replicado con la última replicación de una instantánea de almacenamiento.
8. También puede restaurar un solo archivo fuera de las instantáneas que se han replicado en el volumen /hana/shared/PRD en la región de Azure de recuperación ante desastres.

La secuencia de pasos siguiente implica la recuperación de la instancia de producción de SAP HANA según la instantánea de almacenamiento restaurada y las copias de seguridad del registro de transacciones que están disponibles. Los pasos son los siguientes:

1. Cambie la ubicación de copia de seguridad a **/hana/logbackups** con SAP HANA Studio.
   ![Cambiar la ubicación de copia de seguridad para la recuperación de DR](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

2. SAP HANA examina las ubicaciones del archivo de copia de seguridad y sugiere la copia de seguridad del registro de transacciones más reciente para restaurar. El examen puede tardar unos minutos hasta que aparezca una pantalla similar a la siguiente: ![Lista de copias de seguridad del registro de transacciones para recuperación de DR](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

3. Ajuste algunas de las configuraciones predeterminadas:

      - Desactive la casilla **Use Delta Backups** (Usar copias de seguridad diferenciales).
      - Seleccione **Initialize Log Area** (Inicializar el área de registro).

   ![Establecer la inicialización del área de registro](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

4. Seleccione **Finalizar**.

   ![Finalizar la restauración de DR](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Debería aparecer una ventana de progreso, como la que se muestra aquí. Recuerde que el ejemplo trata de una restauración de recuperación ante desastres de una configuración de SAP HANA de escalado horizontal de tres nodos.

![Progreso de la restauración](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Si la restauración deja de responder en la **Finish** (Finalizar) y no aparece la pantalla de progreso, confirme que todas las instancias de SAP HANA de los nodos de trabajo se estén ejecutando. Si es necesario, inicie manualmente las instancias de SAP HANA.


### <a name="failback-from-dr-to-a-production-site"></a>Conmutación por recuperación desde DR a un sitio de producción
Puede conmutar por recuperación desde DR a un sitio de producción. Echemos un vistazo al caso en el que la conmutación por error en el sitio de recuperación ante desastres se debió a problemas en la región de Azure de producción y no a que el usuario necesitara recuperar datos perdidos. Esto significa que la carga de trabajo de producción de SAP se ha estado ejecutando durante un tiempo en el sitio de recuperación ante desastres. Una vez que se resuelven los problemas en el sitio de producción, es posible que el usuario quiera realizar la conmutación por recuperación con el sitio de producción. Como no se pueden perder datos, el paso de regreso al sitio de producción implica varios pasos y una cooperación estrecha con SAP HANA en el equipo de operaciones de Azure. Es tarea del usuario desencadenar el equipo de operaciones para iniciar la sincronización de vuelta al sitio de producción una vez que se resuelvan los problemas.

La secuencia de pasos es la siguiente:

1. SAP HANA en el equipo de operaciones de Azure obtiene el desencadenador para sincronizar los volúmenes de almacenamiento de producción desde los volúmenes de almacenamiento de recuperación ante desastres, que ahora representan el estado de producción. En este estado, la unidad de instancia grande de HANA en el sitio de producción está apagada.
2. SAP HANA en el equipo de operaciones de Azure supervisa la replicación y garantiza que se logre una actualización antes de informarle al cliente.
3. El usuario apaga las aplicaciones que usan la instancia de HANA de producción en el sitio de recuperación ante desastres. Después, realiza una copia de seguridad del registro de transacciones de HANA. Luego, detiene la instancia de HANA que se ejecuta en las unidades de instancia grande de HANA en el sitio de recuperación ante desastres.
4. Una vez que se apaga la instancia de HANA que se ejecuta en la unidad de instancia grande de HANA del sitio de recuperación ante desastres, el equipo de operaciones vuelve a sincronizar manualmente los volúmenes de disco.
5. SAP HANA en el equipo de operaciones de Azure inicia de nuevo la unidad de instancia grande de HANA en el sitio de producción y se la entrega al usuario. Asegúrese de que la instancia de SAP HANA esté apagada en el momento en que se inicia la unidad de instancia grande de HANA.
6. El usuario realiza los mismos pasos de la restauración de la base de datos que siguió cuando se realizó la conmutación por error al sitio de recuperación ante desastres anteriormente.

### <a name="monitoring-disaster-recovery-replication"></a>Supervisión de la replicación de recuperación ante desastres

Puede supervisar el estado del progreso de la replicación de almacenamiento ejecutando el script `azure_hana_replication_status.pl`. Este script se debe ejecutar desde una unidad que esté en ejecución en la ubicación de recuperación ante desastres. De lo contrario, no funcionará según lo previsto. El script funciona independientemente de si la replicación está activa o no. El script se puede ejecutar para cada unidad de instancia grande de HANA del inquilino en la ubicación de recuperación ante desastres. No se puede usar para obtener detalles sobre el volumen de arranque.

Llame al script de esta forma:
```
./replication_status.pl <HANA SID>
```

La salida se desglosa por volumen en las secciones siguientes:  

- Estado del vínculo
- Actividad de replicación actual
- Instantánea replicada más reciente 
- Tamaño de la instantánea más reciente
- Tiempo de retardo actual entre instantáneas (la última replicación de instantánea completada y ahora)  

El estado del vínculo se muestra como **Activo**, a menos que el vínculo entre las ubicaciones esté inactivo o que actualmente esté en curso un evento de conmutación por error. La actividad de replicación controla si algún dato se está replicando actualmente o si está inactivo, o si hay alguna otra actividad actualmente en el vínculo. La última instantánea replicada solo debe aparecer como `snapmirror…`. Luego se muestra el tamaño de la última instantánea. Por último, se muestra el tiempo de retardo. El tiempo de retardo representa el tiempo que va desde el momento de la replicación programada hasta que finaliza esta replicación. Un tiempo de retardo puede ser más de una hora para la replicación de datos, en especial si se trata de la replicación inicial, incluso si se inició la replicación. El tiempo de retardo seguirá aumentando hasta que finalice la replicación en curso.

El siguiente es un ejemplo de una salida:

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












