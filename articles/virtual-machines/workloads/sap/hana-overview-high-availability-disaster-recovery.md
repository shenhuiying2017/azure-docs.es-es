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
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: 4356e35609fa7a9727d8ad4f20fd18df01a05e84
ms.contentlocale: es-es
ms.lasthandoff: 08/30/2017

---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes) 

Los conceptos de alta disponibilidad y recuperación ante desastres son aspectos importantes de la ejecución de servidores importantes de SAP HANA en Azure (instancias grandes). Es importante trabajar con SAP, el integrador del sistema, o Microsoft para diseñar e implementar correctamente la estrategia correcta de alta disponibilidad y recuperación ante desastres. También resulta fundamental tener en cuenta los objetivos de punto de recuperación y de tiempo de recuperación, que son específicos del entorno.

Microsoft admite algunos de los métodos estándar de alta disponibilidad de SAP HANA con instancias grandes de HANA. Entre ellos se incluyen los siguientes:

- **Replicación de almacenamiento**: la capacidad que tiene el propio sistema de almacenamiento para replicar todos los datos a otra marca de instancias grandes de HANA en otra región de Azure. SAP HANA funciona de forma independiente con respecto a este método.
- **Replicación del sistema de HANA**: la replicación de todos los datos de SAP HANA a un sistema SAP HANA independiente. El objetivo de tiempo de recuperación se minimiza a través de la replicación de datos a intervalos regulares. SAP HANA admite los modos asincrónico, sincrónico en memoria y sincrónico (se recomiendan solo para sistemas de SAP HANA que estén en el mismo centro de datos o a menos de 100 km de este). En el diseño actual de las marcas de instancias grandes de HANA, la replicación del sistema de HANA solo puede usarse para lograr alta disponibilidad. En función del diseño de red actual, la replicación del sistema de HANA no se puede usar sin el componente de proxy inverso de terceros para configuraciones de recuperación ante desastres en otra región de Azure. 
- **Conmutación por error automática del host**: una solución de recuperación para SAP HANA que se usará como alternativa a la replicación del sistema de HANA. Cuando el nodo maestro deja de estar disponible, uno o varios nodos en espera de SAP HANA están configurados en el modo de escalado horizontal y SAP HANA automáticamente conmuta por error a un nodo en espera.

SAP HANA en Azure (instancias grandes) se ofrece en dos regiones de Azure en tres regiones geopolíticas distintas (EE. UU., Australia y Europa). Dos regiones distintas que hospedan marcas de instancias grandes de HANA se conectan con circuitos de red dedicados independientes que se usan para replicar instantáneas de almacenamiento para proporcionar métodos de recuperación ante desastres. La replicación no se establece de manera predeterminada. Se configura para los clientes que solicitaron la funcionalidad de recuperación ante desastres. La replicación de almacenamiento depende del uso de instantáneas de almacenamiento para instancias grandes de HANA. Tampoco es posible elegir una región de Azure como región de recuperación ante desastres, que se encuentra en otra área geopolítica. 

Los métodos y las combinaciones de alta disponibilidad y recuperación ante desastres que se admiten actualmente se pueden ver en esta tabla:

| Escenario compatible con instancias grandes de HANA | Opción de alta disponibilidad | Opción de recuperación ante desastres | Comentarios |
| --- | --- | --- | --- |
| Nodo único | No disponible | Configuración de recuperación ante desastres dedicada<br /> Configuración de recuperación ante desastres multipropósito | |
| Conmutación por error automática de host: N+m<br /> incluido 1+1 | Posible con modo en espera tomando el rol activo<br /> Control de HANA del cambio de rol | Configuración de recuperación ante desastres dedicada<br /> Configuración de recuperación ante desastres multipropósito<br /> Sincronización de recuperación ante desastres con replicación de almacenamiento | Conjuntos de volúmenes de HANA adjuntos a todos los nodos (n+m)<br /> El sitio de recuperación ante desastres debe tener el mismo número de nodos |
| Replicación del sistema de HANA | Posible con configuración principal/secundaria<br /> La configuración secundaria pasa al rol principal en caso de conmutación por error<br /> Replicación del sistema de HANA y conmutación por error de control del SO | Configuración de recuperación ante desastres dedicada<br /> Configuración de recuperación ante desastres multipropósito<br /> Sincronización de recuperación ante desastres con replicación de almacenamiento<br /> La recuperación ante desastres con replicación del sistema de HANA todavía no es posible sin componentes de terceros | Conjunto independiente de volúmenes de discos adjunto a cada nodo<br /> Solo los volúmenes de disco de la réplica secundaria en el sitio de producción se replican a una ubicación de recuperación ante desastres<br /> Un conjunto de volúmenes requerido en el sitio de recuperación ante desastres | 

Como configuración de recuperación ante desastres dedicada establecemos una configuración en la que no se usa la unidad de instancias grandes de HANA en el sitio de recuperación ante desastres para ejecutar ninguna otra carga de trabajo o ningún sistema que no sea de producción. La unidad es pasiva y se implementa solo en caso de pasar a la acción si se ejecuta una conmutación por error ante desastres. Hasta el momento no tenemos ningún cliente con este tipo de configuración.

Como configuración de recuperación ante desastres multipropósito establecemos una configuración en el sitio de recuperación ante desastres en el que la unidad de instancias grandes de HANA ejecuta una carga de trabajo no de producción. En el caso de desastres en que se apagaría un sistema no de producción, se iniciarían los conjuntos de volúmenes replicados en el almacenamiento (adicionales) y la instancia de HANA de producción. Hasta el momento, los clientes que usan la funcionalidad de recuperación ante desastres de instancias grandes de HANA usan esta alternativa de configuración. 


Para más información sobre la alta disponibilidad de SAP HANA, consulte los artículos siguientes sobre SAP: 

- [SAP HANA High-Availability Whitepaper](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html) (Documento técnico sobre la alta disponibilidad de SAP HANA)
- [SAP HANA Administration Guide](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf) (Guía de administración de SAP HANA)
- [Vídeo de SAP sobre la replicación del sistema de SAP HANA](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Nota de soporte técnico de SAP 1999880: preguntas más frecuentes sobre la replicación del sistema de SAP HANA](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [Nota de soporte técnico de SAP 2165547: copia de seguridad y restauración de SAP HANA en el entorno de replicación del sistema de SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [Nota de soporte técnico de SAP 1984882: uso de la replicación del sistema de SAP HANA para el cambio de hardware con el tiempo de inactividad mínimo o cero](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Consideraciones de red para recuperación ante desastres con instancias grandes de HANA

Para aprovechar la funcionalidad de recuperación ante desastres de instancias grandes de HANA, es preciso comenzar a diseñar la conectividad de red a las dos regiones de Azure distintas. Para ello, necesita un circuito ExpressRoute que se conecte desde el entorno local de su región de Azure principal y otro que se conecte desde el entorno local a su región de recuperación ante desastres. Esta medida bastaría para cubrir una situación en la que hay un problema en toda una región de Azure, incluida la ubicación del enrutador perimetral de Microsoft Enterprise (MSEE).

Como segunda medida, puede conectar todas las redes virtuales de Azure que se conectan a SAP HANA en Azure (instancias grandes) en una de las regiones al circuito ExpressRoute que conecta las instancias grandes de HANA en la otra región. Con esa "conexión cruzada", los servicios que se ejecutan en una red virtual de Azure en la región 1 se pueden conectar a unidades de instancia grande de HANA en la región 2 y viceversa. Así se soluciona el caso de que solo una de las ubicaciones de MSEE que conecta la ubicación local con Azure se quede fuera de servicio.

Los gráficos siguientes muestran este tipo de configuración resistente para la recuperación ante desastres:

![Configuración óptima para la recuperación ante desastres](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-for-using-hana-large-instances-storage-replication-for-disaster-recovery"></a>Otros requisitos para usar la replicación de almacenamiento de instancias grandes de HANA para la recuperación ante desastres

Los siguientes son los requisitos adicionales de una configuración de recuperación ante desastres con instancias grandes HANA:

- Las SKU de SAP HANA en Azure (instancias grandes) deben tener el mismo tamaño que las SKU de producción y estar implementadas en la región de recuperación ante desastres. En las implementaciones de cliente que existen hasta el momento, estas instancias se usan para ejecutar instancias de HANA que no son de producción. Nos referimos a ellas como Configuraciones de recuperación ante desastres multipropósito.   
- Debe solicitar almacenamiento adicional en el sitio de recuperación ante desastres para cada una de las SKU de SAP HANA en Azure (instancias grandes) que desea recuperar en el sitio de recuperación ante desastres, en caso de que sea necesario. Esta acción provoca la asignación de volúmenes de almacenamiento, que son el destino de la replicación de almacenamiento de la región de Azure de producción a la región de Azure de recuperación ante desastres.

Puede encontrar más detalles sobre la recuperación ante desastres en los últimos capítulos del documento.
 

## <a name="backup-and-restore"></a>Copia de seguridad y restauración

Uno de los aspectos más importantes para el funcionamiento de las bases de datos es asegurarse de que la base de datos se puede proteger de varios incidentes catastróficos. Estos eventos pueden deberse a cualquier cosa, desde desastres naturales a simples errores del usuario.

La copia de seguridad de una base de datos, con la capacidad de realizar la restauración a cualquier momento dado (como antes de que alguien eliminara datos críticos), permite la restauración a un estado que sea lo más parecido posible al que había antes de que se produjera la interrupción.

Para que el resultado sea óptimo, es preciso realizar dos tipos de copias de seguridad:

- Copias de seguridad de bases de datos: copias de datos completas, incrementales o diferenciales
- Copias de seguridad del registro de transacciones

Además de las copias de seguridad completas de la base de datos que se realizan a nivel de aplicación, se puede ser más exhaustivo, para lo que se deben realizar las copias de seguridad con instantáneas de almacenamiento. Si bien las instantáneas de almacenamiento no reemplazan las copias de seguridad del registro de transacciones. Las copias de seguridad del registro de transacciones siguen siendo importantes para restaurar la base de datos a un momento dado o para vaciar los registros de las transacciones ya confirmadas. Pero las instantáneas de almacenamiento pueden acelerar la recuperación al proporcionar una imagen de puesta al día de la base de datos de manera rápida. 

SAP HANA en Azure (instancias grandes) ofrece dos opciones de copia de seguridad y restauración:

- Opción personal (DIY, por las siglas de Do It Yourself). Una vez que se hace el cálculo para garantizar que existe espacio en disco suficiente, se deben crear copias de seguridad completas de las bases de datos y los registros a través de los métodos de copia de seguridad de disco, ya sea directamente en los volúmenes adjuntos a las unidades de instancia grande de HANA o en los recursos compartidos NFS configurados en una máquina virtual de Azure. En el último caso, los clientes configuran una máquina virtual Linux en Azure, adjuntan Azure Storage a la máquina virtual y comparten el almacenamiento a través de un servidor NFS configurado en esa máquina virtual. Si se realiza la copia de seguridad de los volúmenes adjuntos directamente a unidades de instancia grande de HANA, las copias de seguridad se deben copiar a una cuenta de Azure Storage (una vez que configure una máquina virtual de Azure que exporte los recursos compartidos FS basados en Azure Storage), o bien se debe usar un almacén de Azure Backup o almacenamiento en frío de Azure. Otra opción consiste en usar una herramienta de protección de datos de terceros para almacenar las copias de seguridad una vez que se copian en una cuenta Azure Storage. Esta opción de copia de seguridad DIY también podría ser necesaria para aquellos datos que tengan que almacenarse durante períodos más largos con fines de conformidad y auditoría. En todos los casos, las copias de seguridad se copian en recursos compartidos NFS que se representan a través de una máquina virtual y Azure Storage.
- Use la funcionalidad de copia de seguridad y restauración que proporciona la infraestructura subyacente de SAP HANA en Azure (instancias grandes). Esta opción cumple con la necesidad de copias de seguridad y restauraciones rápidas. En el resto de esta sección se aborda esta funcionalidad de copia de seguridad y restauración que se ofrece con instancias grandes de HANA y su relación con la funcionalidad de recuperación ante desastres que se ofrece en instancias grandes de HANA.

> [!NOTE]
> La tecnología de instantáneas que utiliza la infraestructura subyacente de instancias grandes de HANA depende de las instantáneas de SAP HANA. Por ahora, las instantáneas de SAP HANA no funcionan con los distintos inquilinos de los contenedores de base de datos multiinquilino de SAP HANA. Como resultado, este método de copia de seguridad no se puede usar cuando se implementaron varios inquilinos en contenedores de base de datos multiinquilino de SAP HANA. Sin embargo, si funciona cuando solo se implementó un inquilino.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Uso de instantáneas de almacenamiento de SAP HANA en Azure (instancias grandes)

La infraestructura de almacenamiento subyacente de SAP HANA en Azure (instancias grandes) admite la noción de una instantánea de almacenamiento de volúmenes. Se admiten tanto la copia de seguridad como la restauración de un volumen, pero es preciso tener en cuenta lo siguiente:

- En lugar de las copias de seguridad de bases de datos completas, se toman instantáneas del volumen de almacenamiento con frecuencia.
- Cuando se desencadena una instantánea sobre volúmenes /hana/data, hana/log y /hana/shared (incluye /usr/sap), la instantánea de almacenamiento inicia una instantánea de SAP HANA antes de ejecutar la instantánea de almacenamiento. Esta instantánea de SAP HANA es el punto de configuración de las posibles restauraciones del registro después de la recuperación de la instantánea de almacenamiento.
- Después del momento en que la instantánea de almacenamiento se ejecuta correctamente, se elimina la instantánea de SAP HANA.
- Las copias de seguridad del registro de transacciones se realizan con frecuencia y se almacenan en el volumen hana/logbackups o en Azure. El volumen /hana/logbackups que contiene las copias de seguridad del registro de transacciones se puede desencadenar para crear instantáneas por separado. En ese caso, no es necesario ejecutar ninguna instantánea de HANA.
- Si la base de datos se debe restaurar a un momento dado, se realiza una solicitud al soporte técnico de Microsoft Azure (interrupción de producción) o a SAP HANA en Azure Service Management para que realicen la restauración a una instantánea de almacenamiento concreta (por ejemplo, una restauración planeada de un sistema de espacio aislado a su estado original).
- La instantánea de SAP HANA que se incluye en la instantánea de almacenamiento es un punto de desplazamiento para aplicar las copias de seguridad del registro de transacciones que se han ejecutado y almacenado después de que se tomara la instantánea de almacenamiento.
- Estas copias de seguridad del registro de transacciones se realizan para restaurar la base de datos a un momento dado.

El cliente tiene la posibilidad de realizar instantáneas de almacenamiento que estén dirigidas a tres clases de volúmenes distintas:

- Instantánea combinada en /hana/data, /hana/log y /hana/shared (incluido /usr/sap) en conjunto. (Esta instantánea requiere la ejecución de una instantánea de SAP HANA)
- Instantánea independiente en /hana/logbackups
- Partición del SO (solo para el tipo I de las instancias grandes de HANA)


### <a name="storage-snapshot-considerations"></a>Consideraciones de las instantáneas de almacenamiento

>[!NOTE]
>Las instantáneas de almacenamiento consumen espacio de almacenamiento que se ha asignado a las unidades de instancia grande de HANA. Por tanto, es necesario considerar los aspectos siguientes en términos de programar las instantáneas de almacenamiento y mantener un número de las mismas. 

Mecánica concreta de las instantáneas de almacenamiento de SAP HANA en Azure (instancias grandes):

- Una instantánea de almacenamiento concreta (en el momento en que se toma) consume poco espacio de almacenamiento.
- Cuando el contenido de datos cambia y el contenido de los archivos de datos de SAP HANA cambia en el volumen de almacenamiento, la instantánea debe almacenar el contenido del bloque original.
- La instantánea de almacenamiento aumenta de tamaño. Cuanto más tiempo dure la instantánea, mayor será la instantánea de almacenamiento.
- Cuantos más cambios se realicen en el volumen de la base de datos de SAP HANA a lo largo de la vigencia de una instantánea de almacenamiento, mayor será el consumo de espacio de dicha instantánea.

SAP HANA en Azure (instancias grandes) incluye tamaños de volumen fijos para el volumen de datos y de registro de SAP HANA. Realizar instantáneas de esos volúmenes consume el espacio del volumen. Por tanto, es su responsabilidad programar las instantáneas de almacenamiento, supervisar el consumo de espacio de los volúmenes de almacenamiento y administrar la cantidad de instantáneas almacenadas. Solo el propio usuario puede decidir si finalmente deshabilitar las instantáneas de almacenamiento, porque es el usuario quien importa masas de datos o realiza otros cambios importantes en la base de datos de HANA. 


En las secciones siguientes se proporciona información para realizar estas instantáneas, donde también se incluyen recomendaciones generales:

- Aunque el hardware puede admitir 255 instantáneas por volumen, se recomienda encarecidamente no acercarse a este número.
- Antes de realizar instantáneas de almacenamiento, supervise y realice un seguimiento del espacio disponible.
- Reduzca el número de instantáneas del almacenamiento en función del espacio libre. Es posible que deba reducir el número de instantáneas que mantiene, o bien que tenga que ampliar los volúmenes (Puede solicitar más almacenamiento en unidades de un terabyte).
- Durante actividades como mover datos a SAP HANA con herramientas de migración de la plataforma SAP (R3load) o mediante la restauración de bases de datos de SAP HANA a partir de copias de seguridad, es necesario deshabilitar las instantáneas de almacenamiento en el volumen /hana/data. 
- Durante reorganizaciones mayores de tablas de SAP HANA, debe evitarse realizar instantáneas de mantenimiento, siempre que sea posible.
- Las instantáneas de almacenamiento son un requisito previo para usar las funcionalidades de recuperación ante desastres de SAP HANA en Azure (instancias grandes).

### <a name="setting-up-storage-snapshots"></a>Configuración de instantáneas del almacenamiento

Los pasos para configurar las instantáneas de almacenamiento con instancias grandes de HANA tienen el siguiente aspecto:
1. Asegúrese de que Perl está instalado en el sistema operativo Linux en el servidor de instancias grandes de HANA.
2. Modifique /etc/ssh/ssh\_config para agregar la línea _MACs hmac-sha1_.
3. Cree una cuenta de usuario de copia de seguridad de SAP HANA en el nodo maestro de cada instancia de SAP HANA que se ejecute (si procede).
4. El cliente HDB de SAP HANA debe instalarse en todos los servidores de instancias grandes de SAP HANA.
5. En el primer servidor de instancias grandes de SAP HANA de cada región, debe crearse una clave pública para acceder a la infraestructura de almacenamiento subyacente que controla la creación de instantáneas.
6. Copie los scripts y el archivo de configuración de [esta ubicación de GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts) a la ubicación de **hdbsql** de la instalación de SAP HANA.
7. Modifique el archivo HANABackupDetails.txt tanto como sea necesario para ajustarse a las especificaciones apropiadas del cliente.

### <a name="step-1-install-sap-hana-hdbclient"></a>Paso 1: Instalar HDBClient de SAP HANA

El sistema operativo Linux instalado en SAP HANA en Azure (instancias grandes) incluye las carpetas y los scripts necesarios para ejecutar las instantáneas de almacenamiento de SAP HANA para fines de recuperación ante desastres y de copia de seguridad. Busque las versiones más recientes en [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts). La versión más reciente de los scripts es 2.0.
Sin embargo, es responsabilidad suya instalar HDBcliente de SAP HANA en las unidades de instancia grande de HANA durante la instalación de SAP HANA. (Microsoft no instala HDBclient ni SAP HANA).

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

Para habilitar el acceso a las interfaces de instantánea de almacenamiento del inquilino de instancia grande de HANA, se debe establecer un inicio de sesión a través de la clave pública. En el primer servidor de SAP HANA en Azure (instancias grandes) del inquilino, cree una clave pública que se use para acceder a la infraestructura de almacenamiento, lo que permite crear instantáneas. La clave pública garantiza que no se necesita una contraseña para iniciar sesión en las interfaces de instantánea de almacenamiento y que las credenciales de contraseña no se mantienen. En Linux, en el servidor de instancias grandes de SAP HANA, ejecute el siguiente comando para generar la clave pública:
```
  ssh-keygen –t dsa –b 1024
```
La nueva ubicación es _/root/.ssh/id\_dsa.pub. No escriba una contraseña real; de lo contrario, se le pedirá que la escriba cada vez que inicie sesión. En su lugar, presione la tecla **Entrar** dos veces para quitar el requisito de especificación de contraseña al iniciar sesión.

Asegúrese de que la clave pública se corrigió según lo previsto cambiando las carpetas a /root/.ssh/ y, después, ejecutando el comando **ls**. Si la clave está presente, puede copiarla ejecutando el comando siguiente:

![Para copiar la clave pública, se ejecuta este comando](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

En este momento, póngase en contacto con SAP HANA en Azure Service Management y especifique la clave pública. El representante del servicio usa la clave pública para registrarla en la infraestructura de almacenamiento subyacente que se crea para el inquilino de instancia grande de HANA.

### <a name="step-4-create-an-sap-hana-user-account"></a>Paso 4: Crear una cuenta de usuario de SAP HANA

Para poder iniciar la creación de instantáneas de SAP HANA, es necesario crear un usuario en SAP HANA, que puede usarse en los scripts de instantánea de almacenamiento. Cree una cuenta de usuario de SAP HANA en SAP HANA Studio para estos fines. Esta cuenta debe tener los siguientes privilegios: _Backup Admin_ (Administración de copias de seguridad) y _Catalog Read_ (Lectura de catálogos). En este ejemplo, se crea el nombre de usuario SCADMIN. El nombre de la cuenta de usuario que se creó en HANA Studio distingue mayúsculas de minúsculas.  Asegúrese de hacer clic en "No" en la opción de requerir que el usuario cambie la contraseña en el inicio de sesión siguiente.

![Creación de un usuario en HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Paso 5: Autorizar la cuenta de usuario de SAPA HANA

En este paso se debe autorizar la cuenta de usuario de SAP HANA que creó, de manera que los scripts no tengan que enviar contraseñas en tiempo de ejecución. El comando de SAP HANA `hdbuserstore` permite crear una clave de usuario de SAP HANA, que se almacena en uno o varios nodos de SAP HANA. La clave de usuario permite que el usuario acceda a SAP HANA sin tener que administrar contraseñas mediante el proceso de scripting que se describe más adelante.

>[!IMPORTANT]
>Ejecute el siguiente comando como `root`. De lo contrario, el script no funcionará correctamente.

Escriba el comando `hdbuserstore` de esta forma:

![Ejecución del comando hdbuserstore](./media/hana-overview-high-availability-disaster-recovery/image4-hdbuserstore-command.png)

En el ejemplo siguiente, en el que el usuario es SCADMIN01, el nombre del host es lhanad01 y el número de instancia es 01, el comando es:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Si tiene una configuración de escala horizontal de SAP HANA, debe administrar todo el scripting desde un solo servidor. En este ejemplo, la clave de SAP HANA SCADMIN01 debe modificarse en cada host de forma que refleje que el host está relacionado con la clave. Es decir, se modificará la cuenta de copia de seguridad de SAP HANA con el número de instancia de la base de datos de HANA. La clave debe tener privilegios administrativos en el host al que se asigna a y el usuario de copia de seguridad para el escalado horizontal debe tener derechos de acceso a todas las instancias de SAP HANA. Si los tres nodos de escalado horizontal se llaman lhanad01, lhanad02 y lhanad03, la secuencia de comandos tendrá el aspecto siguiente:

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN02 lhanad02:30215 SCADMIN <password>
hdbuserstore set SCADMIN03 lhanad03:30315 SCADMIN <password>
```

### <a name="step-6-get-snapshot-scripts-configure-snapshots-and-test-configuration-and-connectivity"></a>Paso 6: Obtener scripts de instantánea, configurar instantáneas y probar la configuración y la conectividad

Descargue la versión más reciente de los scripts [aquí](https://github.com/Azure/hana-large-instances-self-service-scripts). Copie los scripts descargados y el archivo de texto en el directorio de trabajo de **hdbsql**. En el caso de las instalaciones actuales de HANA, este directorio es similar a /hana/shared/D01/exe/linuxx86\_64/hdb.
```
azure\_hana\_backup.pl
azure\_hana\_replication\_status.pl
azure\_hana\_snapshot\_details.pl
azure\_hana\_snapshot\_delelte.pl
testHANAConnection.pl
testStorageSnapshotConnection.pl
removeTestStorageSnapshot.pl
HANABackupCustomerDetails.txt
```

El propósito de los distintos scripts y archivos es similar al siguiente:

- **azure\_hana\_backup.pl**:este script se debe programar con cron para que las instantáneas de almacenamiento se ejecuten en los volúmenes data/log/shared de HANA, en el volumen /hana/logbackups de HANA o en el sistema operativo (en las SKU de tipo I de las instancias grandes de HANA)
- **azure\_hana\_replication\_status.pl**: este script está diseñado para proporcionar detalles básicos sobre el estado de la replicación desde el sitio de producción al sitio de recuperación ante desastres.  El script se usa para supervisar que se esté realizando la replicación y mostrar los tamaños de los elementos que se están replicando.  También brinda una guía de lo que se debe hacer si la replicación tarda demasiado o si el vínculo probablemente no esté activo.
- **azure\_hana\_snapshot\_details.pl**: el propósito de este script es proporcionar al cliente una lista de los detalles básicos sobre todas las instantáneas por volumen que existen en el entorno. Este script se puede ejecutar en el servidor principal o en una unidad de servidor en la ubicación de recuperación ante desastres. El script proporciona la información siguiente, desglosada según cada volumen que contiene instantáneas: el tamaño total de las instantáneas en un volumen y luego, de cada instantánea en ese volumen, los detalles del nombre de la instantánea, la hora de creación, el tamaño de la instantánea, la frecuencia de la instantánea y el identificador de la copia de seguridad de HANA que se asocia con esa instantánea (si corresponde).
- **azure\_hana\_snapshot\_delelte.pl**: este script está diseñado para eliminar una instantánea de almacenamiento o un conjunto de instantáneas, ya sea mediante el identificador de la copia de seguridad de SAP HANA como aparece en HANA Studio, o bien mediante el nombre mismo de la instantánea de almacenamiento.  Actualmente, el identificador de la copia de seguridad solo está asociado a las instantáneas creadas para los volúmenes data/log/shared de HANA.  En caso contrario, si se escribe el identificador de la instantánea, busca todas las instantáneas que coincidan con ese identificador.  
- **testHANAConnection.pl**: este script prueba la conexión con la instancia de SAP HANA y es necesario para configurar las instantáneas de almacenamiento.
- **testStorageSnapshotConnection.pl**: este script tiene dos propósitos. En primer lugar, garantizar que la unidad de instancia grande de HANA que ejecuta los scripts tiene acceso a la máquina virtual de almacenamiento asignada y, con eso, a la interfaz de la instantánea de almacenamiento de instancias grandes de HANA. El segundo propósito es crear una instantánea temporal para la instancia de HANA que se prueba. Este script se debe ejecutar para cada instancia de HANA en un servidor a fin de garantizar que los scripts de copia de seguridad funcionan según lo previsto.
- **removeTestStorageSnapshot.pl**: este script se usa para eliminar la instantánea de prueba que se creó con el script testStorageSnapshotConnection.pl. 
- **HANABackupCustomerDetails.txt**: es un archivo de configuración modificable que se debe modificar para adaptarlo a la configuración de SAP HANA.

 
El archivo HANABackupCustomerDetails.txt es el archivo de control y configuración del script que ejecuta las instantáneas de almacenamiento. El archivo se debe ajustar según sus propósitos y la configuración. Debe haber recibido el _nombre de la copia de seguridad del almacenamiento_ y la _dirección IP de almacenamiento_ de SAP HANA en Azure Service Management cuando se implementaron las instancias. No puede modificar la secuencia, el orden ni el espaciado de ninguna de las variables de este archivo. De lo contrario, los scripts no se ejecutan correctamente. Además, recibió la dirección IP del nodo de escalado vertical o del nodo maestro (si se trata de escalado horizontal) desde SAP HANA en Azure Service Management. Después de instalar SAP HANA, también se conoce el número de instancias de HANA y, luego, es necesario agregar un nombre de copia de seguridad en el archivo de configuración.

Para una implementación de escalado vertical u horizontal, el archivo de configuración tendrá un aspecto similar al siguiente una vez que escribe el nombre de la copia de seguridad del almacenamiento y la dirección IP del almacenamiento. Otros datos que debe rellenar en el archivo de configuración son la dirección IP del nodo único o del nodo maestro, el número de instancias de HANA y el nombre de la copia de seguridad que puede elegir:
```
#Provided by Microsoft Service Management
Storage Backup Name: client1hm3backup
Storage IP Address: 10.240.20.31
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 
Node 1 HANA instance number:
Node 1 HANA Backup Name:
```

>[!NOTE]
>Actualmente, solo se usa la información del nodo 1 en el script de instantánea de almacenamiento de HANA real. Se recomienda que pruebe el acceso en todos los nodos de HANA para que, si el nodo maestro de copia de seguridad cambia en algún momento, tenga seguro que cualquier otro pueda ocupar su lugar mediante la modificación de la información del nodo 1.

Después de ingresar todos los datos de configuración en el archivo HANABackupCustomerDetails.txt, es necesario comprobar si las configuraciones son correctas con respecto a los datos de la instancia de HANA mediante el script testHANAConnection.pl. Este script es independiente de una configuración de escalado vertical o escalado horizontal de SAP HANA.

```
testHANAConnection.pl
```

Si se trata de una configuración de escalado horizontal de SAP HANA, asegúrese de que la instancia de HANA principal tiene acceso a todos los servidores e instancias de HANA necesarios. En el script de prueba no hay parámetros, pero es preciso agregar completamente los datos en el archivo de configuración HANABackupCustomerDetails.txt para que el script se ejecute correctamente. Como solo se devuelven los códigos de error del comando shell, el script no puede comprobar los errores en todas las instancias. Aun así, el script proporciona algunos comentarios útiles que debe repasar.

Para ejecutar el script:
```
 ./testHANAConnection.pl
```
Si el script obtiene correctamente el estado de la instancia de HANA, se mostrará un mensaje que indica que la conexión con HANA se ha establecido con éxito.


El paso siguiente es comprobar la conectividad con el almacenamiento según los datos que proporciona en el archivo de configuración HANABackupCustomerDetails.txt y ejecutar una instantánea de prueba.  Antes de ejecutar el script azure\_hana\_backup.pl, se debe ejecutar esta prueba. Si un volumen no contiene instantáneas, no podrá determinar si el volumen está vacío o si se produce algún error de SSH al obtener la información de la instantánea. Por este motivo, el script ejecuta dos pasos:

- Comprueba que es posible que los scripts accedan a las interfaces y a la máquina virtual de almacenamiento de los inquilinos a fin de ejecutar las instantáneas.
- Crea una instantánea de prueba, o ficticia, para cada volumen por instancia de HANA.

Por este motivo, la instancia de HANA se incluye como argumento. Si se produce un error en la ejecución, no es posible realizar la comprobación de errores para la conexión del almacenamiento. Pero el script proporciona sugerencias útiles en esos casos.

El script se ejecuta como se indica a continuación:
```
 ./testStorageSnapshotConnection.pl <HANA SID>
```
Como paso siguiente, el script intentará iniciar sesión en el almacenamiento a través de la clave pública que se proporcionó antes en los pasos de configuración y los datos configurados en HANABackupCustomerDetails.txt. Si se realiza correctamente, se muestra el contenido siguiente:

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

Si se produce algún problema al conectarse con la consola de almacenamiento, el resultado sería similar al siguiente:

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

Después de iniciar sesión correctamente en las interfaces de la máquina virtual del almacenamiento, el script seguiría con la fase 2 y crearía una instantánea de prueba, tal como se muestra aquí para una configuración de escalado horizontal de tres nodos de SAP HANA:

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

Si la instantánea de prueba se ejecutó correctamente con el script, puede continuar con la configuración de las instantáneas de almacenamiento reales. Si no se realizó correctamente, se deben investigar los problemas antes de continuar. Se debe conservar la instantánea de prueba hasta que se hayan realizado las primeras instantáneas reales.



### <a name="step-7-perform-snapshots"></a>Paso 7: Realizar instantáneas

A medida que se completan todos los pasos de preparación, puede comenzar a establecer la configuración de instantánea de almacenamiento real. El script que se va a programar funciona con configuraciones de escalado vertical o escalado horizontal de SAP HANA. La idea es que la ejecución de los scripts se programe a través de cron. 

Hay tres tipos de copias de seguridad de instantáneas que se pueden crear:
- HANA: copia de seguridad de la instantánea combinada en la que la instantánea coordinada abarca los volúmenes que contienen /hana/data, /hana/log, /hana/shared (que también contiene /usr/sap. Es posible realizar una sola restauración de archivos a partir de esta instantánea.
- Registros: copia de seguridad de la instantánea del volumen /hana/logbackups. Ninguna instantánea de HANA se desencadena para ejecutar esta instantánea de almacenamiento. Este volumen de almacenamiento es el volumen pensado para contener las copias de seguridad del registro de transacciones de SAP HANA, las que se realizan con más frecuencia para restringir el crecimiento del registro y evitar una posible pérdida de datos. Es posible realizar una sola restauración de archivos a partir de esta instantánea. La frecuencia debe ser mayor a cinco minutos.
- Arranque: instantánea del volumen que contiene el LUN de arranque de la instancia grande de HANA. La copia de seguridad de esta instantánea solo es posible con las SKU de tipo I de las instancias grandes de HANA. No es posible realizar restauraciones de archivos únicas a partir de la instantánea del volumen que contiene el LUN de arranque.  


La sintaxis de llamada de estos tres tipos distintos de instantáneas sería la siguiente:
```
HANA backup covering /hana/data, /hana/log, /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <HANA SID> manual 30

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <HANA SID> manual 30

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot manual 30

```

Es necesario especificar los parámetros siguientes:

- El primer parámetro que se va a especificar caracteriza el tipo de la copia de seguridad de la instantánea. Los valores permitidos son "hana", "logs" y "boot"
- El segundo valor es el SID de HANA (como HM3). Este parámetro no es necesario para realizar una copia de seguridad del volumen de arranque.
- El tercer parámetro de una instantánea es la etiqueta de copia de seguridad para el tipo de instantánea. Se espera que sea la misma para las instantáneas programadas de una frecuencia o un tipo específico.
- El cuarto parámetro define la retención de las instantáneas de manera indirecta al definir el número de instantáneas con el mismo prefijo de instantánea (etiqueta) que se deben conservar. Este parámetro es importante para la ejecución programada a través de cron. 

En el caso del escalado horizontal, el script realiza algunas comprobaciones adicionales para asegurarse de que se pueda acceder a todos los servidores de HANA y todas las instancias de HANA devuelven el estado apropiado de las instancias antes de continuar con la creación de la instantánea de SAP HANA seguida de una instantánea de almacenamiento.

La ejecución del script azure\_hana\_backup.pl crea la instantánea de almacenamiento en estas tres fases:

- Ejecutar una instantánea de SAP HANA.
- Ejecutar una instantánea del almacenamiento.
- Quitar la instantánea de SAP HANA que se creó antes de la ejecución de la instantánea de almacenamiento.

Ejecute el script, para lo que debe llamarlo desde la carpeta del ejecutable de HDB en la que se copió. 

El período de retención se administra con el número de instantáneas que se envían como parámetro al ejecutar el script (como 30, que se muestra arriba). Por lo tanto, la cantidad de tiempo que cubren las instantáneas de almacenamiento es una función del período de ejecución y el número de instantáneas que se envían como parámetro al ejecutar el script. Si el número de instantáneas que se mantienen supera el número del parámetro de la llamada del script, la instantánea de almacenamiento más antigua de la misma etiqueta (en nuestro caso anterior, _manual_), se eliminarán antes de ejecutar una nueva instantánea. Esto significa que el número que proporcione como último parámetro de la llamada es el que puede usar para controlar el número de instantáneas que se conservan. Con eso también se puede controlar indirectamente el espacio en disco que se usa para las instantáneas. 

>En cuanto cambie la etiqueta, el recuento se reinicia. Esto significa que debe ser estricto en el etiquetado.

### <a name="snapshot-strategies"></a>Estrategias de instantánea
La frecuencia de las instantáneas para los distintos tipos depende de si usa o no la funcionalidad de recuperación ante desastres de instancia grande de HANA. Debido a que la funcionalidad de recuperación ante desastres de las instancias grandes de HANA se basa en la instantánea de almacenamiento, se podrían aplicar recomendaciones especiales en términos de frecuencia y períodos de ejecución de instantáneas de almacenamiento. En las consideraciones y recomendaciones siguientes, se supone que NO se usa la funcionalidad de recuperación ante desastres que ofrecen las instancias grandes de HANA. En su lugar, las instantáneas de almacenamiento se usan como medio para tener copias de seguridad y poder proporcionar la recuperación a un momento dato durante los últimos 30 días. Dadas las limitaciones del número de instantáneas y el espacio, los clientes deben realizar la implementación considerando estos tipos de requisitos:

- Requisitos de objetivo de tiempo de recuperación para la recuperación a un momento dado.
- Uso del espacio.
- Requisitos de objetivos de punto de recuperación y de tiempo de recuperación para una posible recuperación ante desastres
- Ejecución eventual de copias de seguridad completas de la base de datos de HANA en discos. Cada vez que se realiza una copia de seguridad completa de la base de datos en discos, o interfaz de _backint_, se producirá un error en la ejecución de las instantáneas del almacenamiento. Si planea ejecutar copias de seguridad de la base de datos completa sobre las instantáneas del almacenamiento, asegúrese de que la ejecución de las instantáneas del almacenamiento está deshabilitada durante este tiempo.
- El número de instantáneas por volumen está limitado a 255.


En el caso de los clientes que no usan la funcionalidad de recuperación ante desastres de las instancias grandes de HANA, el período de la instantánea es menos frecuente. En dichos casos, vemos clientes que realizan las instantáneas combinadas en /hana/data, /hana/log, /hana/shared (incluye /usr/sap) en un período de 12 o 24 horas y mantienen esas instantáneas de modo que abarquen todo un mes. Lo mismo ocurre con las instantáneas del volumen de copia de seguridad del registro. Mientras tanto, la ejecución de las copias de seguridad del registro de transacciones de SAP HANA contra el volumen de la copia de seguridad del registro se ejecuta en un período de cinco a quince minutos.

Se recomienda realizar instantáneas del almacenamiento programadas mediante cron y usar el mismo script para todas las copias de seguridad y las necesidades de recuperación ante desastres (mediante la modificación de las entradas del script para que se ajusten a los distintos tiempos de copia de seguridad solicitados). Todas estas instantáneas se programan de forma diferente en cron en función de su tiempo de ejecución: cada hora, cada 12 horas, a diario o semanalmente. 

Un ejemplo de programación Cron en /etc/crontab podría ser similar a:
```
00 1-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 46
10 00 * * *  ./azure_hana_backup.pl hana HM3 dailyhana 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log HM3 dailylogback 28
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```
En el ejemplo anterior, se trata de una instantánea combinada por hora que abarca los volúmenes que contienen las ubicaciones /hana/data, /hana/log, /hana/shared (incluye /usr/sap). Este tipo de instantánea se podría usar para una recuperación a un momento dado más rápida en un plazo de dos días. Además, en esos volúmenes hay una instantánea diaria. Por tanto, se tienen dos días de cobertura por instantáneas por hora más cuatro semanas de cobertura por instantáneas diarias. Además, se crea una copia de seguridad del volumen de la copia de seguridad del registro de transacciones una vez cada día. Estas copias de seguridad también se conservan durante cuatro semanas. Como se ve en la tercera línea de crontab, la copia de seguridad del registro de transacciones de HANA está programada para ejecutarse cada cinco minutos. También observe que los minutos iniciales de los distintos trabajos de cron que ejecutan las instantáneas de almacenamiento son escalonados, por lo que esas instantáneas no se ejecutan todas a la vez en un momento dado. 

En el ejemplo siguiente, se realizaría cada hora una instantánea combinada que abarque los volúmenes que contienen las ubicaciones /hana/data, /hana/log, /hana/shared (incluido /usr/sap). Estas instantáneas se conservarían durante dos días. Las instantáneas de los volúmenes de copia de seguridad del registro de transacciones se ejecutan cada cinco minutos y se conservan durante cuatro horas. Como antes, la copia de seguridad del registro de transacciones de HANA se programa para ejecutarla cada cinco minutos. La instantánea del volumen de la copia de seguridad del registro de transacciones se realiza con un retraso de dos minutos una vez que se inicia la copia de seguridad del registro de transacciones. En circunstancias normales, la copia de seguridad del registro de transacciones de SAP HANA debería completarse en esos dos minutos. Como antes, una instantánea de almacenamiento crea una copia de seguridad del volumen que contiene el LUN de arranque y se conserva durante cuatro semanas.

```
10 0-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log HM3 logback 48
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```

Ilustradas en un gráfico, las secuencias del ejemplo anterior (sin incluir el LUN de arranque) serían:

![Relación entre copias de seguridad e instantáneas](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot.PNG)

SAP HANA realiza escrituras normales contra el volumen /hana/log para documentar los cambios confirmados en la base de datos. De manera regular, SAP HANA escribe un punto de retorno en el volumen /hana/data. Tal como se especifica en crontab, una copia de seguridad del registro de transacciones de SAP HANA se ejecuta cada cinco minutos. También se ve una instantánea de SAP HANA que se ejecuta cada hora como resultado del desencadenamiento de una instantánea de almacenamiento combinada sobre el volumen /hana/data, /hana/log y /hana/shared. Una vez que la instantánea de HANA se realiza correctamente, se ejecuta la instantánea de almacenamiento combinada. Tal como se indica en crontab cada cinco minutos, la instantánea de almacenamiento en el volumen /hana/logbackup se ejecuta alrededor de dos minutos después de la copia de seguridad del registro de transacciones de HANA.


>[!IMPORTANT]
> El uso de instantáneas de almacenamiento para las copias de seguridad de SAP HANA solo resulta valioso cuando las instantáneas se realizan junto con las copias de seguridad del registro de transacciones de SAP HANA. Es preciso que dichas copias de seguridad del registro de transacciones puedan cubrir los períodos entre las instantáneas del almacenamiento. 

Si ha adquirido un compromiso a los usuarios de una recuperación a un momento dado de 30 días, necesita lo siguiente:

- Poder acceso a una instantánea de almacenamiento combinada en /hana/data, /hana/log y /hana/shared, que es el caso extremo de 30 días de antigüedad.
- Copias de seguridad del registro de transacciones contiguas que abarquen el tiempo entre cualquiera de las instantáneas del almacenamiento combinado. Por tanto, la instantánea de mayor antigüedad del volumen de copia de seguridad del registro de transacciones debe tener 30 días, a menos que haya copiado las copias de seguridad del registro de transacciones en otro recurso compartido NFS ubicado en Azure Storage. En tal caso, podría extraer copias de seguridad del registro de transacciones anteriores de ese recurso compartido NFS.

Para poder beneficiarse de las instantáneas de almacenamiento y la posible replicación de almacenamiento de las copias de seguridad del registro de transacciones, es necesario cambiar la ubicación donde SAP HANA escribe las copias de seguridad del registro de transacciones. Este cambio se puede hacer en HANA Studio, tal como se muestra a continuación. Si bien SAP HANA crea automáticamente copias de seguridad de segmentos de registros completos, se debe especificar un intervalo de copia de seguridad de registros para que sea determinista. Habitualmente se recomienda ejecutar las copias de seguridad de registros con un período determinista, especialmente cuando se usa la opción de recuperación ante desastres. En el caso siguiente, se tomó un intervalo de copia de seguridad de registros de 15 minutos.

![Programe los registros de copia de seguridad de SAP HANA en SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Puede elegir que las copias de seguridad se realicen con una frecuencia superior a 15 minutos. Especialmente junto con la recuperación ante desastres. Algunos clientes realizan copias de seguridad del registro de transacciones cada cinco minutos.  

Si nunca se realizó una copia de seguridad de la base de datos, el último paso es crear una copia de seguridad en archivo para crear una entrada de copia de seguridad individual que debe existir en el catálogo de copias de seguridad. En caso contrario, SAP HANA no podrá iniciar las copias de seguridad del registro especificadas.

![Realizar una copia de seguridad en archivo para crear una entrada de copia de seguridad individual](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Después de ejecutar correctamente las primeras instantáneas de almacenamiento, también puede eliminar la instantánea de prueba que se ejecutó en el paso 6. Para ello, se debe ejecutar el script removeTestStorageSnapshot.pl como se muestra a continuación:
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Supervisión del número y tamaño de instantáneas del volumen de disco

En un volumen de almacenamiento concreto se puede supervisar tanto el número de instantáneas como el consumo de almacenamiento de esas instantáneas. El comando `ls` no muestra los archivos o el directorio de instantáneas. Sin embargo, el comando `du` del sistema operativo Linux muestra detalles sobre esas instantáneas de almacenamiento debido a que se almacenan en los mismos volúmenes. El comando se puede usar con las opciones siguientes:

- `du –sh .snapshot` proporciona un total de todas las instantáneas en el directorio de instantáneas.
- `du –sh --max-depth=1` enumera todas las instantáneas que se guardan en la carpeta ".snapshot" y el tamaño de cada una de ellas.
- `du –hc` proporciona el tamaño total que usan todas las instantáneas.

Utilice estos comandos para asegurarse de que las instantáneas que tomadas y almacenadas no consumen todo el almacenamiento de los volúmenes.

>[!NOTE]
>Las instantáneas del LUN de arranque no son visibles con los comandos anteriores.

### <a name="getting-details-of-snapshots"></a>Detalles de las instantáneas
Para obtener más detalles sobre las instantáneas, también es posible usar el script azure\_hana\_snapshot\_details.pl. Este script se puede ejecutar en cualquier ubicación si hay un servidor activo en la ubicación de recuperación ante desastres. El script proporciona el siguiente desglose según cada volumen que contiene instantáneas: el tamaño total de las instantáneas en un volumen y luego, de cada instantánea en ese volumen, los detalles siguientes: el nombre de la instantánea, la hora de creación, el tamaño de la instantánea, la frecuencia de la instantánea y el identificador de la copia de seguridad de HANA que se asocia con esa instantánea (si corresponde). La sintaxis de ejecución del script es similar a:

```
./azure_hana_snapshot_details.pl 
```

Debido a que el script intenta recuperar el identificador de la copia de seguridad de HANA, necesita conectarse a la instancia de SAP HANA, lo que requiere que el archivo de configuración HANABackupCustomerDetails.txt esté establecido correctamente. Una salida de dos instantáneas de un volumen tendría un aspecto similar al siguiente:

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


### <a name="file-level-restore-from-storage-snapshot"></a>Restauración de nivel de archivo desde una instantánea de almacenamiento
Para los tipos de instantánea "hana" y "logs", es posible acceder a las instantáneas directamente en los volúmenes del directorio ".snapshot". Puede encontrar un subdirectorio para cada una de las instantáneas. Debería poder copiar cada archivo cubierto por la instantánea en el estado que tenía en el momento de la instantánea desde ese subdirectorio a la estructura de directorios real.

>[!NOTE]
>La restauración de archivos única no funciona para las instantáneas del LUN de arranque. El directorio ".snapshot" no se expone en el LUN de arranque. 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Reducción del número de instantáneas en un servidor

Como ya se ha explicado, es posible reducir el número de etiquetas concretas almacenadas. Los dos últimos parámetros del comando para iniciar una instantánea son una etiqueta y el número de instantáneas que se desean conservar.

```
./azure_hana_backup.pl hana HM3 hanadaily 30
```

En el ejemplo anterior, la etiqueta de la instantánea es _customer_ y el número de instantáneas con esta etiqueta que se conservarán es _30_. A medida que responda al consumo de espacio en el disco es posible que desee reducir el número de instantáneas que se almacenan. La manera más sencilla de reducir el número de instantáneas, por ejemplo a 15, es ejecutar el script con el último parámetro establecido en 15:

```
./azure_hana_backup.pl hana HM3 hanadaily 15
```

Como consecuencia de la ejecución del script con la configuración anterior, se reducen las instantáneas, con lo que el número de ellas, incluida la nueva instantánea del almacenamiento, es _15_. Se conservan las 15 instantáneas más recientes, mientras que se eliminarán las 15 instantáneas más antiguas.

 >[!NOTE]
 > Este script reduce el número de instantáneas solo si hay instantáneas que tienen más de una hora de antigüedad. Este script no elimina las instantáneas que tienen menos de una hora de antigüedad. Estas restricciones están relacionadas con la funcionalidad opcional de recuperación ante desastres que se ofrece.

Si ya no desea mantener un conjunto de instantáneas con una etiqueta de copia de seguridad específica (hanadaily en los ejemplos de sintaxis), es posible ejecutar el script con _0_ como el número de retención para quitar todas las instantáneas que coincidan con esa etiqueta. Sin embargo, la eliminación de todas las instantáneas puede afectar a las funcionalidades de recuperación ante desastres.

Otra posibilidad de eliminar instantáneas específicas es usar el script azure\_hana\_snapshot\_delelte.pl. Este script está diseñado para eliminar una instantánea o un conjunto de instantáneas, ya sea mediante el identificador de la copia de seguridad de HANA como aparece en HANA Studio, o bien mediante el nombre mismo de la instantánea.  Actualmente, el identificador de la copia de seguridad solo está asociado a las instantáneas creadas para el tipo de instantánea "hana". Las copias de seguridad de instantánea de tipo "logs" y "boot" no realizan una instantánea de SAP HANA. Por tanto, no se encontrarán identificadores de copia de seguridad para esas instantáneas. Si se escribe el nombre de la instantánea, busca todas las instantáneas en los distintos volúmenes que coincidan con el nombre de instantánea que se escribió. La sintaxis de llamada del script es:

```
./azure_hana_snapshot_delete.pl 

```

El script se debe ejecutar como usuario _raíz_

Si selecciona una instantánea, tiene la capacidad de eliminar individualmente cada instantánea. En primer lugar, se le preguntará el volumen que contiene la instantánea y, luego, el nombre de la instantánea real. La instantánea se eliminará si está en ese volumen y tiene más de una hora. Puede ejecutar el script azure_hana_snapshot_details para buscar los nombres de los volúmenes y los nombres de las instantáneas. 

>[!IMPORTANT]
>Si hay datos que solo existen en la instantánea que se elimina, ejecutar la eliminación significa que esos datos se perderán para siempre.

   

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>Recuperación a la instantánea más reciente de HANA

En el caso de que se encuentre ante un escenario de bajada de producción, el proceso de recuperación a partir de una instantánea del almacenamiento se puede iniciar como un incidente de cliente con Soporte técnico de Microsoft Azure. Por ejemplo, un escenario inesperado es una cuestión muy urgente si los datos se eliminaran en un sistema de producción y la única manera de volver a obtenerlos fuera a través de la restauración de la base de datos de producción.

En otro caso, una recuperación a un momento dado podría ser poco urgente y se podría planear con varios días de antelación. Para planearlo, se puede usar SAP HANA en Azure Service Management, en lugar de generar un problema de prioridad alta. Por ejemplo, podría estar planeando tratar de realizar una actualización del software SAP mediante la aplicación de un nuevo paquete de mejoras y, luego, necesitara revertir a una instantánea que represente el estado antes de la actualización de EHP.

Antes de emitir la solicitud, debe realizar una preparación. Después, el equipo de SAP HANA en Azure Service Management podrá encargarse de la solicitud y proporcionar los volúmenes restaurados. A partir de ese momento, es decisión suya restaurar la base de datos de HANA con las instantáneas. Aquí se muestra cómo preparar la solicitud:

>[!NOTE]
>La interfaz de usuario podría variar con respecto a las capturas de pantalla siguientes, en función de la versión de SAP HANA que use.

1. Decida qué instantánea desea restaurar. Salvo que se indique lo contrario, solo se restauraría el volumen hana/data. 

2. Cierre la instancia de HANA.

 ![Cerrar la instancia de HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Desmonte los volúmenes de datos de todos los nodos de la base de datos de HANA. Si los volúmenes de datos siguen montados en el sistema operativo, se produce un error en la restauración de la instantánea.
 ![Desmontar los volúmenes de datos de todos los nodos de la base de datos de HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Abra una solicitud de soporte técnico de Azure para enviar la orden de restauración de una instantánea concreta.

 - Durante la restauración: SAP HANA en Azure Service Management podría pedirle que asista a una llamada de conferencia para garantizar la coordinación, la comprobación y la confirmación para asegurarse de que se restaure la instantánea de almacenamiento correcta. 

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

4. Elija una copia de seguridad como base desde la que recuperar la base de datos. En el ejemplo, la instantánea de HANA que aparece en la captura de pantalla, se trata de la instantánea de HANA que se incluyó en la instantánea de almacenamiento. 

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

### <a name="monitoring-the-execution-of-snapshots"></a>Supervisión de la ejecución de instantáneas

Cuando se usan las instantáneas de almacenamiento de instancias grandes de HANA, también se debe supervisar la ejecución de esas instantáneas de almacenamiento. El script que ejecuta una instantánea del almacenamiento escribe la salida en un archivo y, luego, guarda en la misma ubicación que los scripts Perl. Se escribe un archivo independiente para cada instantánea de almacenamiento. La salida de cada archivo muestra claramente las distintas fases que ejecuta el script de la instantánea:

- Búsqueda de los volúmenes que necesitan crear una instantánea
- Búsqueda de las instantáneas tomadas de dichos volúmenes
- Eliminación de eventuales instantáneas existentes para ajustarse el número de instantáneas especificado
- Creación de una instantánea de SAP HANA
- Creación de la instantánea del almacenamiento en los volúmenes
- Eliminación de la instantánea de SAP HANA
- Cambio del nombre de la instantánea más reciente a **".0"**

La parte más importante del script cab se identifica como esta parte:
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
En este ejemplo puede ver la forma en que el script registra la creación de la instantánea de HANA. En el caso del escalado horizontal, este proceso se inicia en el nodo maestro. El nodo maestro inicia la creación sincrónica de las instantáneas de SAP HANA en cada uno de los nodos de trabajado. Después, se toma la instantánea del almacenamiento. Después de la ejecución correcta de las instantáneas del almacenamiento, se elimina la instantánea de HANA. Se inicia de nuevo desde el nodo maestro.


## <a name="disaster-recovery-principles"></a>Principios de la recuperación ante desastres
Con las instancias granes de HANA, se ofrece una funcionalidad de recuperación ante desastres entre las marcas de instancia grande de HANA en las distintas regiones de Azure. Esto significa que si implementa unidades de instancia grande de HANA en la región Oeste de EE. UU. de Azure, podría usar unidades de instancia grande de HANA en la región Este de EE. UU. como unidades de recuperación ante desastres. Como se mencionó anteriormente, la recuperación ante desastres no se configura automáticamente porque es necesario que se pague también por otra unidad de instancia grande de HANA en la región de la recuperación ante desastres. El programa de instalación de la recuperación ante desastres funciona tanto en configuraciones de escalado vertical como de escalado horizontal. 

En los escenarios implementados hasta ahora, los clientes usan la unidad de la región de recuperación ante desastres para ejecutar sistemas que no son de producción que usan una instancia instalada de HANA. La unidad de instancia grande de HANA debe tener la misma SKU que la que se usa con fines de producción. A continuación, es necesario solicitar algunos otros volúmenes de disco para usarlos como destinos de la replicación de almacenamiento desde el sistema de producción al sitio de recuperación ante desastres. Los volúmenes que realmente se replican al sitio de recuperación ante desastres cubren lo siguiente:

- /hana/data
- /hana/log
- /hana/shared
- /hana/log/backup (incluye /usr/sap)


La base de la funcionalidad de recuperación ante desastres que se ofrece es la funcionalidad de replicación de almacenamiento que se ofrece en la infraestructura de instancia grande de HANA. La funcionalidad que se usa en el almacenamiento no es un flujo constante de los cambios que se replican de manera asincrónica a medida que se realizan los cambios en el almacenamiento. En su lugar, se trata de un mecanismo que se basa en el hecho de que las instantáneas se crean de manera habitual en estos volúmenes. La diferencia entre una instantánea ya replicada y una instantánea nueva que todavía no se replica luego se transfiere al sitio de recuperación ante desastres a los volúmenes de disco de destino (que tienen el mismo tamaño que los volúmenes de producción). La primera vez, los datos completos de un volumen se deben transferir antes de que la cantidad de datos sea cada vez más pequeña con las diferencias entre las instantáneas. Como resultado, los volúmenes que se encuentran en el sitio de recuperación ante desastres contienen cada una de las instantáneas de volumen realizadas en el sitio de producción. A la larga, esto permite usar ese sistema de recuperación ante desastres para ir a un estado anterior con el fin de recuperar los datos perdidos sin tener que revertir el sistema de producción.

En casos donde se usa la replicación del sistema de HANA como funcionalidad de alta disponibilidad en el sitio de producción, solo se replican los volúmenes de la instancia de nivel 2 (o réplica). Esta configuración podría provocar un efecto que podría generar un retraso en la replicación del almacenamiento en el sitio de recuperación ante desastres si mantiene o da de baja la unidad del servidor de la réplica secundaria (nivel 2) o la instancia de SAP HANA en esta unidad. 

>[!IMPORTANT]
>Al igual que lo que ocurre con la replicación del sistema de HANA de varios niveles, apagar la unidad del servidor o la instancia de HANA de nivel 2 bloqueará la replicación en el sitio de recuperación ante desastres cuando se use la funcionalidad de recuperación ante desastres de instancia grande de HANA.


>[!NOTE]
>La funcionalidad de replicación del almacenamiento de instancia grande de HANA crea reflejos o réplicas de las instantáneas de almacenamiento. Por lo tanto, si no realiza instantáneas de almacenamiento como se indicó en la sección sobre copias de seguridad de este documento, no puede haber ninguna replicación en el sitio de recuperación ante desastres. La ejecución de la instantánea de almacenamiento es un requisito previo para la replicación del almacenamiento en el sitio de recuperación ante desastres.

Con el fin de minimizar el objetivo de punto de recuperación, los intervalos de replicación siguientes se establecen en el servicio de instancia grande de HANA:
- Los volúmenes que la instantánea combinada (tipo de instantánea = "hana") abarca se replican cada 15 minutos en los destinos de volúmenes de almacenamiento equivalentes que se encuentran en el sitio de recuperación ante desastres.
- El volumen de copia de seguridad del registro de transacciones (tipo de instantánea = "logs") se replica cada tres minutos en los destinos de volúmenes de almacenamiento equivalentes que se encuentran en el sitio de recuperación ante desastres.

Con el fin de minimizar el objetivo de punto de recuperación, se debe configurar lo siguiente
- Realice una instantánea de almacenamiento de tipo "hana" (consulte el paso 7: Realizar instantáneas) entre cada 30 minutos y una hora.
- Realice copias de seguridad del registro de transacciones de SAP HANA cada cinco minutos.
- Realice una instantánea de almacenamiento de tipo "logs" entre cada cinco o quince minutos. Con este período, debería poder alcanzar un RPO de alrededor de 15 a 25 minutos.

Con esta configuración, la secuencia de copias de seguridad del registro de transacciones, las transacciones de almacenamiento y la replicación del volumen de copia de seguridad del registro de transacciones de HANA y /hana/data, /hana/log, /hana/shared (incluye /usr/sap) podría verse como en este gráfico.

 ![Relación entre la instantánea de copia de seguridad del registro de transacciones y el reflejo de instantáneas en el eje de tiempo](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Para conseguir un objetivo de punto de recuperación incluso mejor en el caso de la recuperación ante desastres, puede copiar las copias de seguridad del registro de transacciones de HAN fuera de SAP HANA en Azure (instancias grandes) y en la otra región de Azure. Para alcanzar esta reducción adicional del objetivo de punto de recuperación, siga estos pasos:

1. Realizar una copia de seguridad del registro de transacciones de HANA tan frecuentemente como sea posible en /hana/logbackups.
2. Copie las copias de seguridad del registro de transacciones (con rsync) terminadas en las máquinas virtuales de Azure hospedadas en el recurso compartido NFS, que están en redes virtuales de Azure en la región de producción de Azure y en las regiones de recuperación ante desastres. Es necesario conectar ambas redes virtuales de Azure al circuito de ExpressRoute que conecta las instancias grandes de HANA de producción con Azure (consulte los gráficos que aparecen en la sección "Requisitos de la recuperación ante desastres" de este documento). 
3. Mantenga las copias de seguridad del registro de transacciones de esa región en la máquina virtual que está adjunta al almacenamiento exportado de NFS.
4. En caso de una conmutación por error ante desastres, complemente las copias de seguridad del registro de transacciones que encuentre en el volumen /hana/logbackups con copias de seguridad del registro de transacciones creadas más recientemente en el recurso compartido NFS del sitio de recuperación ante desastres. 
5. Ahora podría iniciar la restauración de una copia de seguridad del registro de transacciones para la copia de seguridad más reciente que se podría guardar en la región de recuperación ante desastres.

## <a name="disaster-recovery-fail-over-procedure"></a>Procedimiento de conmutación por error de recuperación ante desastres
En caso de que desee o necesite conmutar por error al sitio de recuperación ante desastres, observa un proceso en el que es necesario interactuar con SAP HANA en Azure Operations. Hasta ahora, los pasos del proceso serían los siguientes:

- Debido a que se ejecuta una instancia que no es de producción de HANA en la unidad de recuperación ante desastres de instancias grandes de HANA, es necesario apagar esta instancia. Se supone que hay instalada previamente una instancia de producción de HANA inactiva.
- Es necesario asegurarse de que ya no hay ningún proceso de SAP HANA en ejecución. Para ello, use el comando /usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList. El resultado debería mostrar el proceso hdbdaemon en estado detenido y ningún otro proceso de HANA en ejecución o iniciado.
- Ahora debe comprobar qué nombre de instantánea o identificador de copia de seguridad de SAP HANA desea que se restaure en el sitio de recuperación ante desastres. En casos reales de recuperación ante desastres, esta instantánea suele ser la instantánea más reciente. Sin embargo, es posible que se trate de una situación en la que sea necesario recuperar datos perdidos. Por lo tanto, podría ser necesario seleccionar una instantánea anterior.
- Debe ponerse en contacto con SAP HANA en Azure Service Management a través de una solicitud de soporte técnico de alta prioridad y solicitar la restauración de ese identificador de copia de seguridad de HANA o instantánea en el sitio de recuperación ante desastres. En lo que respecta a las operaciones, se realizarán los pasos siguientes:
    - Se detiene la replicación de instantáneas del volumen de producción a los volúmenes de recuperación ante desastres, si esto todavía no sucedió debido a la razón de interrupción del sitio de producción.
    - El identificador de copia de seguridad o la instantánea de almacenamiento que eligió se restaura en los volúmenes de recuperación ante desastres.
    - Después de la restauración, los volúmenes de recuperación ante desastres están disponibles para montarlos en las unidades de instancia grande de HANA en la región de recuperación ante desastres.
- El paso siguiente que debe realizar el cliente es montar los volúmenes de recuperación ante desastres en la unidad de instancia grande de HANA del sitio de recuperación ante desastres. 
- Luego puede iniciar la instancia de producción de SAP HANA hasta ahora inactiva.
- Si eligió copiar registros de la copia de seguridad del registro de transacciones de manera adicional para reducir el tiempo de RPO, es necesario combinar esas copias de seguridad del registro de transacciones en el directorio /hana/logbackups de recuperación ante desastres que se acaba de montar. No sobrescriba las copias de seguridad existentes, solo copie las nuevas que el almacenamiento todavía no replica.

La secuencia de pasos siguientes implica la recuperación de la instancia de producción de SAP HANA según la instantánea de almacenamiento restaurada y las copias de seguridad del registro de transacciones que están disponibles. Los pasos son:

Cambie la ubicación de la copia de seguridad a /hana/logbackups con SAP HANA Studio, tal como se muestra en ![Cambio de la ubicación de copia de seguridad para recuperación de DR](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

SAP HANA examina las ubicaciones del archivo de copia de seguridad y sugiere la copia de seguridad del registro de transacciones más reciente que se restaurará. Este examen puede tardar unos minutos hasta que aparezca una pantalla similar a la siguiente: ![Lista de copias de seguridad del registro de transacciones para recuperación de DR](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

En el paso siguiente se deben configurar algunos ajustes predeterminados a través de:

- Anular la selección de **Use Delta Backups** (Usar copias de seguridad diferenciales)
- Seleccionar **Initialize Log Area** (Inicializar el área de registro) como se muestra a continuación:

 ![Establecer Inicializar el área de registro](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

En la página siguiente, presione **Finalizar** como se muestra a continuación:

 ![Finalizar restauración de DR](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Debería aparecer una ventana de progreso, como la que se muestra aquí. Recuerde que el ejemplo se trata de una restauración de recuperación ante desastres de una configuración de SAP HANA de escalado horizontal de tres nodos.

 ![Progreso de la restauración](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

En los casos en que parezca que la restauración deja de responder en la "pantalla de finalización" y no aparezca la pantalla de progreso, compruebe que todas las instancias de SAP HANA de los nodos de trabajo estén en ejecución. Si es necesario, inicie manualmente las instancias de SAP HANA.


### <a name="fail-back-from-dr-to-production-site"></a>Conmutación por recuperación desde DR al sitio de producción
Se da por hecho que la conmutación por error en el sitio de recuperación ante desastres fue producto de problemas en la región de Azure de producción y no porque el usuario necesitara recuperar datos perdidos, lo que significa que ahora podría estar en el sitio de producción o un momento en el sitio de recuperación ante desastres. Una vez que se resuelven los problemas en el sitio de producción, es posible que el usuario quiera realizar conmutación por recuperación con el sitio de producción. Como no se pueden perder datos, el paso de vuelta al sitio de producción implica varios pasos y una cooperación estrecha con SAP HANA en Azure Operations. Es tarea del cliente desencadenar las operaciones para iniciar la sincronización de vuelta al sitio de producción una vez que se resuelvan los problemas.

Esta es la secuencia de pasos:

- SAP HANA en Azure Operations obtiene el desencadenador para sincronizar los volúmenes de almacenamiento de producción desde los volúmenes de almacenamiento de recuperación ante desastres, los que se representan ahora el estado de producción. En este estado, la unidad de instancia grande de HANA en el sitio de producción está apagada.
- SAP HANA en Azure Operations supervisa la replicación y garantiza que se logre una actualización antes de informarle al cliente
- Es necesario apagar las aplicaciones que usan la instancia de HANA de producción en el sitio de recuperación ante desastres. Luego, se debe realizar una copia de seguridad del registro de transacciones de HANA y detener la instancia de HANA que se ejecuta en las unidades de instancia grande de HANA en el sitio de recuperación ante desastres
- Una vez que se apaga la instancia de HANA que se ejecuta en la unidad de instancia grande de HANA del sitio de recuperación ante desastres, la operación debe volver a sincronizar manualmente los volúmenes de disco.
- SAP HANA en Azure Operations volverá a iniciar la unidad de instancia grande de HANA en el sitio de producción y se la entregará al usuario. Asegúrese de que la instancia de SAP HANA esté apagada en el momento en que se inicia la unidad de instancia grande de HANA.
- Ahora es necesario realizar los mismos pasos de la restauración de la base de datos que siguió para conmutar por error en el sitio de recuperación ante desastres anteriormente.

### <a name="monitoring-disaster-recovery-replication"></a>Supervisión de la replicación de recuperación ante desastres

Puede supervisar el estado del progreso de la replicación de almacenamiento mediante el script azure\_hana\_replication\_status.pl. Este script se debe ejecutar desde una unidad que esté en ejecución en la ubicación de recuperación ante desastres; de lo contrario, no funciona según lo previsto. El script funciona independientemente de si la replicación está activa. El script se puede ejecutar para cada unidad de instancia grande de HANA del inquilino en la ubicación de recuperación ante desastres. No se puede usar para obtener detalles sobre el volumen de arranque.

Llame al script de esta forma:
```
./replication_status.pl <HANA SID>
```

La salida se desglosa por volumen en las secciones siguientes:  

- Estado del vínculo
- Actividad de replicación actual
- Instantánea replicada más reciente 
- Tamaño de la instantánea más reciente
- Tiempo de retardo actual entre la última replicación de instantánea completada y ahora.  

El estado del vínculo se muestra como "Activo", a menos que el vínculo entre las ubicaciones esté inactivo o que actualmente esté en curso un evento de conmutación por error. La actividad de replicación controla si algún dato se está replicando actualmente, si está inactivo o si hay alguna otra actividad actualmente en el vínculo. La última instantánea replicada solo debe aparecer como "snapmirror…". Luego se muestra el tamaño de la última instantánea. Por último, se muestra el tiempo de retardo. El tiempo de retardo representa el tiempo que va desde el momento de la replicación programada hasta que finaliza esta replicación. Un tiempo de retardo puede ser más de una hora para la replicación de datos, en especial si se trata de la primera replicación inicial, incluso si se inició la replicación. El tiempo de retardo seguirá aumentando hasta que finalice la replicación en curso.

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












