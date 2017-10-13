---
title: Procedimientos recomendados para la matriz virtual de StorSimple | Microsoft Docs
description: Describe los procedimientos recomendados para implementar y administrar la matriz virtual de StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2017
ms.author: alkohli
ms.openlocfilehash: 264764c5e9c32574d97beb2cc3c1bb1cfb555568
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-virtual-array-best-practices"></a>Procedimientos recomendados de la matriz virtual de StorSimple
## <a name="overview"></a>Información general
La matriz virtual de Microsoft Azure StorSimple es una solución de almacenamiento integrada que administra las tareas de almacenamiento de información entre un dispositivo virtual local que se ejecuta en un hipervisor y el almacenamiento de nube de Microsoft Azure. La matriz virtual de StorSimple es una alternativa eficaz y rentable a la matriz física de la serie 8000. La matriz virtual se puede ejecutar en la infraestructura existente de hipervisor, admite los protocolos iSCSI y SMB, y está especialmente indicada para escenarios de oficinas remotas y sucursales. Para más información sobre las soluciones de StorSimple, vaya a [Microsoft Azure StorSimple Overview](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx) (Información general de Microsoft Azure StorSimple).

En este artículo se tratan los procedimientos recomendados que se implementan durante la instalación inicial, la implementación y la administración de la matriz virtual de StorSimple. Estos procedimientos proporcionan directrices validadas para la instalación y administración de una matriz virtual. Este artículo va dirigido a los administradores de TI que implementan y administran las matrices virtuales en sus centros de datos.

Se recomienda una revisión periódica de los procedimientos recomendados, con el fin de asegurarse de que el dispositivo los cumple cuando se realizan cambios en la configuración o en el flujo de operaciones. Si surgieran problemas al implementar estos procedimientos recomendados un disco virtual, [póngase en contacto con Microsoft Support](storsimple-virtual-array-log-support-ticket.md) para obtener ayuda.

## <a name="configuration-best-practices"></a>Procedimientos recomendados de configuración
Estos procedimientos recomendaciones cubren las directrices que se deben seguir durante la instalación inicial y la implementación de las matrices virtuales. Entre estos procedimientos recomendados se incluyen los relacionados con el aprovisionamiento de la máquina virtual, la configuración de directivas de grupo, el ajuste de tamaño, la configuración de las redes, la configuración de las cuentas de almacenamiento y la creación de recursos compartidos y volúmenes para la matriz virtual. 

### <a name="provisioning"></a>Aprovisionamiento
La matriz virtual de StorSimple es una máquina virtual (VM) aprovisionada en el hipervisor (Hyper-V o VMware) de un servidor host. Al aprovisionar la máquina virtual, asegúrese de que el host puedo dedicar suficientes recursos. Para más información, vaya a [requisitos de recursos mínimos](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) para aprovisionar una matriz.

Implemente los siguientes procedimientos recomendados al aprovisionar la matriz virtual:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Tipo de máquina virtual** |**segunda generación** para su uso con Windows Server 2012, o versiones posteriores, y una imagen *.vhdx* . <br></br> **primera generación** para su uso con Windows Server 2008, o versiones posteriores, y una imagen *.vhd* . |Su utiliza una imagen *.vmdk* , use la versión 8-11 de la máquina virtual. |
| **Tipo de memoria** |Configurar como **memoria estática**. <br></br> No utilice la opción de **memoria dinámica** . | |
| **Tipo de disco de datos** |Aprovisiónelo como **expansión dinámica**.<br></br> **Tamaño fijo** requiere mucho tiempo. <br></br> No use la opción de **diferenciación** . |Utilice la opción de **aprovisionamiento fino** . |
| **Modificación del disco de datos** |No se permiten la expansión ni la reducción. Si se intenta, se pierden todos los datos locales del dispositivo. |No se permiten la expansión ni la reducción. Si se intenta, se pierden todos los datos locales del dispositivo. |

### <a name="sizing"></a>Ajuste de tamaño
Si se cambia el tamaño de la matriz virtual de StorSimple, puede tener en cuenta los siguientes factores:

* La reserva local para volúmenes o recursos compartidos. En el nivel local, aproximadamente un 12 % del espacio está reservado para cada volumen o recurso compartido en capas aprovisionados. Aproximadamente un 10 % del espacio también está reservado para un volumen anclado localmente para el sistema de archivos.
* Sobrecarga de instantáneas. Aproximadamente un 15 % del espacio del nivel local está reservado para las instantáneas.
* Necesidad de restauraciones. Si la restauración se realiza como una operación nueva, el ajuste de tamaño debe tener en cuenta el espacio necesario para la restauración. La restauración se realiza en un recurso compartido o volumen del mismo tamaño.
* Debe asignarse una parte del búfer para cualquier crecimiento inesperado.

Según los factores anteriores, los requisitos de ajuste de tamaño se pueden representar mediante la siguiente ecuación:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Los ejemplos siguientes ilustran cómo puede ajustar el tamaño de una matriz virtual en función de sus requisitos.

#### <a name="example-1"></a>Ejemplo 1:
En una matriz virtual, desea poder:

* Aprovisionar un volumen en capas o recurso compartido de 2 TB.
* Aprovisionar un volumen en capas o recurso compartido de 1 TB.
* Aprovisionar volumen anclado localmente o recurso compartido de 300 GB.

Para los volúmenes o recursos compartidos anteriores, vamos a calcular los requisitos de espacio del nivel local.

En primer lugar, para cada volumen en capas o recurso compartido, la reserva local equivaldría al 12 % del tamaño del volumen o recurso compartido. Para el volumen o el recurso compartido anclados localmente, la reserva local es del 10 % del tamaño del volumen o el recurso compartido anclados localmente (además del tamaño aprovisionado). En este ejemplo, necesita

* Una reserva local de 240 GB (para un volumen en capas o recurso compartido de 2 TB).
* Una reserva local de 120 GB (para un volumen en capas o recurso compartido de 1 TB).
* 330 GB para el volumen o el recurso compartido anclados localmente (agregando 10 % de la reserva local al tamaño aprovisionado de 300 GB)

El espacio total requerido hasta ahora en el nivel local es: 240 GB + 120 GB + 330 GB = 690 GB.

En segundo lugar, necesitamos al menos tanto espacio en el nivel local como la reserva individual más grande. Esta cantidad adicional se usa en caso de que sea preciso realizar una restauración a partir de una instantánea en la nube. En este ejemplo, la mayor reserva local es 330 GB (incluida la reserva para el sistema de archivos), por lo que se agregaría a los 690 GB: 690 GB + 330 GB = 1020 GB.
Si hemos realizado posteriores restauraciones adicionales, siempre podemos liberar el espacio de la operación de restauración anterior.

En tercer lugar, necesitamos un 15 % del total de su espacio local para almacenar las instantáneas locales, por lo que solo está disponible un 85 %. En este ejemplo, sería alrededor de 1020 GB = 0,85&ast;TB del disco de datos aprovisionado. Por lo tanto, el disco de datos aprovisionado sería (1020&ast;(1/0,85)) = 1200 GB = 1,20 TB ~ 1,25 TB (redondeo al cuartil más cercano)

Si tiene en cuenta un crecimiento inesperado y nuevas restauraciones, debe aprovisionar un disco local de alrededor de 1,25 - 1,5 TB.

> [!NOTE]
> También se recomienda el aprovisionamiento fino del disco local. Esta recomendación se debe a que el espacio de restauración solo se necesita cuando se desean restaurar datos con una antigüedad superior a cinco días. La recuperación a nivel de elemento le permite restaurar los datos de los últimos cinco días sin que se requiera el espacio adicional para la restauración.


#### <a name="example-2"></a>Ejemplo 2:
En una matriz virtual, desea poder:

* Aprovisionar un volumen en capas de 2 TB.
* Aprovisionar volumen anclado localmente de 300 GB.

En función del 12 % de reserva de espacio local para volúmenes o recursos compartidos en capas y el 10 % para volúmenes o recursos compartidos anclados localmente, necesitamos

* Una reserva local de 240 GB (para un volumen en capas o recurso compartido de 2 TB).
* 330 GB para el volumen o el recurso compartido anclados localmente (agregando 10 % de la reserva local al espacio aprovisionado de 300 GB)

El espacio total requerido en el nivel local es: 240 GB + 330 GB = 570 GB

El espacio local mínimo necesario para la restauración es de 330 GB.

Un 15 % del total del disco se utiliza para almacenar las instantáneas, por lo que solo 0,85 está disponible. Por tanto, el tamaño del disco es (900&ast;(1/0,85)) = 1,06 TB ~ 1,25 TB (redondeo al cuartil más cercano)

Si tiene en cuenta un crecimiento inesperado, puede aprovisionar un disco local de 1,25-1,5 TB.

### <a name="group-policy"></a>Directiva de grupo
Directiva de grupo es una infraestructura que permite implementar configuraciones específicas para usuarios y equipos. La configuración de Directiva de grupo se encuentra en objetos de Directiva de grupo (GPO), que están vinculados a los siguientes contenedores de Servicios de dominio de Active Directory (AD DS): sitios, dominios o unidades organizativas (OU). 

Si la matriz virtual está unido a un dominio, los GPO se pueden aplicar a él. Estos GPO pueden instalar aplicaciones, como un software antivirus, que pueden afectar negativamente al funcionamiento de la matriz virtual de StorSimple.

Por consiguiente, es recomendable que:

* Se asegure de que su matriz virtual está en su propia unidad organizativa (UO) de Active Directory.
* Se asegure de que no se aplican objetos de directiva de grupo (GPO) a su matriz virtual. Pueda bloquear la herencia para asegurarse de que la matriz virtual (nodo secundario) no herede automáticamente los GPO del nodo primario. Para más información, vaya a [Bloquear herencia](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Redes
La configuración de red de cualquier matriz virtual se realiza a través de la interfaz de usuario web local. Una interfaz de red virtual se habilita a través del hipervisor en el que se ha aprovisionado la matriz virtual. Utilice la página [Configuración de red](storsimple-virtual-array-deploy3-fs-setup.md) para configurar la dirección IP, subred y puerta de enlace de la interfaz de red virtual.  También puede configurar el servidor DNS principal y secundario, la configuración horaria y la configuración de proxy opcional del dispositivo. La mayor parte de la configuración de red se realiza una sola vez. Revise los [requisitos de red de StorSimple](storsimple-ova-system-requirements.md#networking-requirements) antes de implementar la matriz virtual.

Al implementar la matriz virtual, es aconsejable seguir estos procedimientos recomendados:

* Asegúrese de que la red en que se ha implementado la matriz virtual tiene capacidad para dedicar 5 Mbps de ancho de banda de Internet (o más).
  
  * La necesidad del ancho de banda de Internet varía en función de las características de la carga de trabajo y de la tasa de cambio de datos.
  * El cambio de datos que se puede controlar es directamente proporcional a su ancho de banda de Internet. Por ejemplo, cuando se realiza una copia de seguridad, un ancho de banda de 5 Mbps puede acomodar un cambio de datos de alrededor de 18 GB en 8 horas. Con cuatro veces más ancho de banda (20 Mbps) puede controlar el cambio de cuatro veces más datos (72 GB).
* Asegúrese de que la conectividad con Internet está siempre disponible. Las conexiones de Internet esporádicas o poco confiables a los dispositivos pueden producir una pérdida de acceso a los datos en la nube y podrían tener como resultado una configuración no admitida.
* Si planea implementar el dispositivo como un servidor de iSCSI:
  
  * Es aconsejable que deshabilite la opción **Obtener una dirección IP automáticamente** (DHCP).
  * Configure las direcciones IP estáticas. Debe configurar un servidor DNS principal y uno secundario.
  * Si define varias interfaces de red en una matriz virtual, solo la primera interfaz de red (de forma predeterminada, esta interfaz es **Ethernet**) puede acceder a la nube. Para controlar el tipo de tráfico, puede crear varias interfaces de red virtual en la matriz virtual (configurada como un servidor de iSCSI) y conectar estas interfaces a distintas subredes.
* Para limitar solo el ancho de banda de la nube (el que usa la matriz virtual), configure la limitación en el enrutador o el firewall. Si define la limitación en el hipervisor, limitará todos los protocolos, incluidos iSCSI y SMB, en lugar de simplemente el ancho de banda de la nube.
* Asegúrese de que está habilitada la sincronización de hora de los hipervisores. Si se usa Hyper-V, seleccione una matriz virtual en el Administrador de Hyper-V, vaya a **Configuración&gt; Servicios de integración** y asegúrese de que **Sincronización de hora** esté marcada.

### <a name="storage-accounts"></a>Cuentas de almacenamiento
La matriz virtual de StorSimple puede asociarse con una cuenta de almacenamiento individual. Esta cuenta de almacenamiento puede ser una cuenta de almacenamiento generada automáticamente, una cuenta de la misma suscripción que el servicio o una cuenta de almacenamiento relacionada con otra suscripción. Para más información, consulte cómo [administrar cuentas de almacenamiento para una matriz virtual](storsimple-virtual-array-manage-storage-accounts.md).

Utilice las siguientes recomendaciones para las cuentas de almacenamiento asociadas con la matriz virtual.

* Al vincular varias matrices virtuales con una sola cuenta de almacenamiento, tenga en cuenta la capacidad máxima (64 TB) de una matriz virtual y el tamaño máximo (500 TB) de una cuenta de almacenamiento. Esto limita el número de matrices virtuales de tamaño completo que se pueden asociar con dicha cuenta de almacenamiento a unos 7.
* Al crear una nueva cuenta de almacenamiento:
  
  * Se recomienda que crearlo en la región más cercana a la oficina remota o sucursal en la que está implementada la matriz virtual de StorSimple para minimizar las latencias.
  * Tenga en cuenta que las cuentas de almacenamiento no se pueden mover entre diferentes regiones. Tampoco es posible mover servicios entre suscripciones.
  * Utilice una cuenta de almacenamiento que implementa redundancia entre los centros de datos. El almacenamiento con redundancia geográfica (GRS), el almacenamiento con redundancia de zona (ZRS) y el almacenamiento con redundancia local (LRS) se admiten para su uso con cualquier matriz virtual. Para más información acerca de los distintos tipos de cuentas de almacenamiento, vaya a [Replicación de almacenamiento de Azure](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Recursos compartidos y volúmenes
En una matriz virtual de StorSimple se pueden aprovisionar recursos compartidos cuando está configurada como un servidor de archivos y volúmenes cuando está configurada como un servidor de iSCSI. Los procedimientos recomendados para la creación de volúmenes y recursos compartidos están relacionados con el tamaño y el tipo configurado.

#### <a name="volumeshare-size"></a>Tamaño de volumen o recurso compartido
En una matriz virtual se pueden aprovisionar recursos compartidos cuando está configurada como un servidor de archivos y volúmenes cuando está configurada como un servidor de iSCSI. Los procedimientos recomendados para la creación de volúmenes y recursos compartidos están relacionados con el tamaño y el tipo configurado. 

Tenga en cuenta los siguientes procedimientos recomendadas al aprovisionar recursos compartidos o volúmenes en el dispositivo virtual.

* Los tamaños de archivo en relación al tamaño aprovisionado de un recurso compartido en capas pueden afectar al rendimiento de los niveles. Si trabaja con archivos de gran tamaño, la salida en niveles se realizará más lentamente. Cuando trabaje con esta clase de archivos, es recomendable que el archivo de mayor tamaño no ocupe más del 3 % del tamaño recurso compartido.
* En la matriz virtual se pueden crear un máximo de 16 volúmenes o recursos compartidos. Para los límites de tamaño de los recursos compartidos y volúmenes en capas y anclados localmente, siempre haga referencia a los [límites de StorSimple Virtual Array](storsimple-ova-limits.md).
* Al crear un volumen, tenga en cuenta tanto el consumo de datos esperado como el crecimiento futuro. El volumen no se puede expandir más adelante.
* Una vez creado el volumen, no se puede reducir el tamaño del volumen de StorSimple.
* Si escribe en un volumen en capas en StorSimple, cuando los datos del volumen alcanzan un umbral concreto (en relación con el espacio local reservado para el volumen), se limitará la operación de E/S. Si se sigue escribiendo en este volumen, la E/S se ralentiza considerablemente. Aunque se puede escribir en un volumen en capas más allá de su capacidad aprovisionada (no impedimos activamente que el usuario escriba más allá de la capacidad aprovisionada), verá una notificación de alerta en el sentido de que haya una suscripción excesiva. Una vez que vea la alerta, es imperativo que tome medidas de subsanación como eliminar el volumen (en la actualidad no se admite la expansión de volúmenes).
* Para casos de uso de recuperación ante desastres, cuando el número de recursos compartidos o volúmenes permitidos es 16 y el número máximo de volúmenes o recursos compartidos que se pueden procesar en paralelo también es 16, el número de recursos compartidos o volúmenes no tiene una incidencia en el RTO y los RPO.

#### <a name="volumeshare-type"></a>Tipo de volumen o recurso compartido
StorSimple admite dos tipos de volúmenes o recursos compartidos en función del uso: anclados localmente y en capas. Los volúmenes o recursos compartidos anclados localmente tienen aprovisionamiento grueso, mientras que los volúmenes o recursos compartidos en capas tienen aprovisionamiento fino. No se puede convertir un volumen o recurso compartido anclado localmente en uno en capas o *viceversa* una vez creado.

Al configurar volúmenes o recursos compartidos de StoreSimple, se recomienda implementar los siguientes procedimientos recomendados:

* Identifique el tipo de volumen en función de las cargas de trabajo que tiene intención de implementar antes de crear un volumen. Use volúmenes anclados localmente para cargas de trabajo que requieren tanto garantías locales de datos (incluso durante una interrupción de la nube) como latencias de nube bajas. Una vez que se crea un volumen en una matriz virtual, el tipo de volumen no se puede cambiar de anclado localmente a en capas, o *viceversa*. Como ejemplo, cree volúmenes anclados localmente al implementar cargas de trabajo SQL o cargas de trabajo que hospeden máquinas virtuales (VM); use volúmenes en capas para cargas de trabajo del recurso compartido de archivos.
* Si trata con tamaños de archivo grandes, active la opción para los datos de archivo que se usen con menos frecuencia. Cuando se habilita esta opción para agilizar la transferencia de datos a la nube, se utiliza un tamaño de fragmento de desduplicación mayor, de 512 KB.

#### <a name="volume-format"></a>Formato de volumen
Después de crear volúmenes de StorSimple en un servidor de iSCSI, es preciso que inicialice, monte y dé formato a dichos volúmenes. Esta operación se realiza en el host conectado a su dispositivo de StorSimple. Cuando se montan y se da formato a volúmenes en el host de StorSimple, se recomienda seguir los procedimientos recomendados.

* Dé un formato rápido a todos los volúmenes de StorSimple.
* Al dar formato a un volumen de StorSimple, utilice un tamaño de unidad de asignación (AUS) de 64 KB (el valor predeterminado es 4 KB). La AUS de 64 KB se basa en pruebas realizadas internamente para cargas de trabajo comunes de StorSimple y otras cargas de trabajo.
* Si usa la matriz virtual de StorSimple configurada como un servidor de iSCSI, no utilice volúmenes distribuidos o discos dinámicos, ya que estos no son compatibles con StorSimple.

#### <a name="share-access"></a>Acceso de recurso compartido
Al crear recursos compartidos en un servidor de archivos de la matriz virtual, siga estas directrices:

* Al crear un recurso compartido, asigne un grupo de usuarios como administrador de recursos compartidos, en lugar de usuario individual.
* Puede administrar los permisos NTFS después de que se cree el recurso compartido. Para ello, debe editar los recursos compartidos desde el Explorador de Windows.

#### <a name="volume-access"></a>Acceso de volumen
Al configurar los volúmenes iSCSI de una matriz virtual de StorSimple, es importante controlar el acceso siempre que sea necesario. Para determinar qué servidores de host pueden acceder a los volúmenes, cree y asocie los registros de control de acceso (ACR) con volúmenes de StorSimple.

Utilice los siguientes procedimientos recomendados al configurar ACR para volúmenes de StorSimple:

* Asocie siempre al menos un ACR a un volumen.

* Si asigna más de un ACR a un volumen, asegúrese de que este no está expuesto de tal forma que pueda acceder a él más de un host no en clúster concurrentemente. Si ha asignado varios ACR a un volumen, aparece un mensaje de advertencia que le indicará que debe revisar la configuración.

### <a name="data-security-and-encryption"></a>Seguridad y cifrado de datos
La matriz virtual de StorSimple tiene características de seguridad y cifrado de datos que garantizar la confidencialidad e integridad de los datos. Al usar estas características, se recomienda seguir estos procedimientos recomendados: 

* Defina una clave de cifrado de almacenamiento en la nube para generar un cifrado AES-256 antes de que los datos se envíen desde la matriz virtual a la nube. Esta clave no se requiere si los datos están cifrados. La clave se puede generar y mantener segura mediante un sistema de administración de claves como [Almacén de claves de Azure](../key-vault/key-vault-whatis.md).
* Al configurar la cuenta de almacenamiento mediante el servicio StorSimple Manager, asegúrese de que habilita el modo SSL crear un canal seguro para la comunicación de red entre el dispositivo de StorSimple y la nube.
* Regenere las claves de las cuentas de almacenamiento (para lo que debe acceder al servicio Almacenamiento de Azure) periódicamente para representar todos los cambios en el acceso basándose en la lista de administradores modificada.
* Los datos de la matriz virtual se comprimen y se desduplican antes de enviarlos a Azure. No se recomienda utilizar el servicio de rol de desduplicación de datos en el host de Windows Server.

## <a name="operational-best-practices"></a>Procedimientos recomendados operativos
Los procedimientos recomendados operativos son directrices que se deben seguir durante la operación o la administración diaria de la matriz virtual. Estos procedimientos cubren tareas de administración específicas, como realizar copias de seguridad, restaurar a partir de un conjunto de copia de seguridad, realizar una conmutación por error, desactivar y eliminar la matriz, supervisar el estado y uso del sistema, y ejecutar la detección de virus en una matriz virtual.

### <a name="backups"></a>Copias de seguridad
Las copias de seguridad de los datos de la matriz virtual se realizan en la nube de dos maneras: una copia de seguridad diaria automática predeterminada de todo el dispositivo que comience a las 22:30, o bien a través de una copia de seguridad manual a petición. De manera predeterminada, el dispositivo crea automáticamente instantáneas diarias en la nube de todos los datos que residen en él. Para más información, vaya a [Crear una copia de seguridad de la matriz virtual de StorSimple](storsimple-virtual-array-backup.md).

No se pueden cambiar la frecuencia y retención asociadas con las copias de seguridad predeterminadas, pero se puede configurar la hora en la que se inician las copias de seguridad diarias a diario. Al configurar la hora de inicio de las copias de seguridad automáticas, se recomienda:

* Programar las copias de seguridad para que se realicen en horas de poca actividad. La hora de inicio de la copia de seguridad no debe coincidir con un momento de numerosas E/S del host.
* Inicie una copia de seguridad manual a petición cuando planee realizar una conmutación por error de un dispositivo o antes de la ventana de mantenimiento, con el fin de proteger los datos en la matriz virtual.

### <a name="restore"></a>Restauración
Se puede realizar una restauración desde un conjunto de copia de seguridad de dos maneras: restaurar a otro volumen o recurso compartido, o bien realizar una recuperación a nivel de elemento (disponible solo en una matriz virtual configurada como un servidor de archivos). La recuperación a nivel de elemento permite realizar una recuperación de archivos y carpetas pormenorizada desde una copia de seguridad en la nube de todos los recursos compartidos del dispositivo de StorSimple. Para más información, vaya a [Restaurar desde una copia de seguridad de la matriz virtual de StorSimple](storsimple-virtual-array-clone.md).

Al realizar una restauración, tenga en cuenta las siguientes directrices:

* La matriz virtual de StorSimple no admite la restauración en contexto. Sin embargo, se puede logra mediante un proceso de dos pasos: cree espacio en la matriz virtual y, luego, realice la restauración a otro volumen o recurso compartido.
* Si la restauración se realiza desde un volumen local, tenga en cuenta la operación tardará mucho en completarse. Aunque el volumen puede conectarse rápidamente, los datos siguen hidratándose en segundo plano.
* El tipo de volumen permanece igual durante todo el proceso de restauración. Un volumen en capas se restaura en otro volumen en capas y un volumen anclado localmente en otro volumen anclado localmente.
* Si se produce un error en el trabajo de restauración al intentar restaurar un volumen o un recurso compartido desde un conjunto de copia de seguridad, todavía se puede crear en el portal un recurso compartido o un volumen de destino. Es importante eliminar cualquier volumen o recurso compartido de destino sin usar del portal para minimizar los posibles problemas que pueda provocar este elemento en el futuro.

### <a name="failover-and-disaster-recovery"></a>Conmutación por error y recuperación ante desastres
La conmutación por error de un dispositivo permite migrar datos desde un dispositivo de *origen* del centro de datos a otro dispositivo de *destino* situado en la misma ubicación geográfica o en otra diferente. La conmutación por error del dispositivo es para todo el dispositivo. Durante la conmutación por error, los datos de la nube para el dispositivo de origen cambian la propiedad a la del dispositivo de destino.

En el caso de una matriz virtual de StorSimple, la conmutación por error solo se puede realizar a otra matriz virtual administrada por el mismo servicio StorSimple Manager. No se permite una conmutación por error a un dispositivo de la serie 8000 serie o a una matriz administrada por otro servicio StorSimple Manager (que el del dispositivo de origen). Para más información sobre las consideraciones de la conmutación por error, consulte los [requisitos previos para la conmutación por error de un dispositivo](storsimple-virtual-array-failover-dr.md).

Al realizar una conmutación por error para una matriz virtual, tenga en cuenta lo siguiente:

* Para una conmutación por error planeada, se recomienda realizar el procedimiento de desconexión de todos los volúmenes o recursos compartidos antes de iniciar la conmutación por error. Siga las instrucciones específicas del sistema operativo para desconectar primero los volúmenes o recursos compartidos del host y, luego, desconectarlos del dispositivo virtual.
* Para la recuperación ante desastres (DR) de un servidor de archivos se recomienda unir el dispositivo de destino al mismo dominio que el de origen, con el fin de que los permisos del recurso compartido se resuelvan automáticamente. En esta versión, se admite solo la conmutación por error en un dispositivo de destino del mismo dominio.
* Una vez que se completa la recuperación ante desastres, el dispositivo de origen se elimina automáticamente. Aunque el dispositivo ya no está disponible, la máquina virtual que se ha aprovisionado en el sistema host sigue consumiendo recursos. Para evitar que se acumulen cargos se recomienda eliminar esta máquina virtual del sistema de host.
* Tenga en cuenta que aunque la conmutación por error no se realice correctamente, **los datos siempre están seguros en la nube**. Considere los tres escenarios siguientes en los que la conmutación por error no se completó correctamente:
  
  * Se produjo un error en las fases iniciales de la conmutación por error, como cuando se realizan las comprobaciones previas de la recuperación ante desastres. En esta situación, aún puede usarse el dispositivo de destino. Puede volver a intentar la conmutación por error en el mismo dispositivo de destino.
  * Se produjo un error durante el proceso de conmutación por error real. En este caso, el dispositivo de destino se marca como no usable. Debe aprovisionar y configurar otra matriz virtual de destino y usarla para la conmutación por error.
  * La conmutación por error se completó después de que el dispositivo de origen se eliminara, pero el dispositivo de destino tiene problemas y no se puede acceder a los datos. Los datos siguen estando seguros en la nube y se pueden recuperar fácilmente mediante la creación de otra matriz virtual y, luego, mediante su uso como un dispositivo de destino para la recuperación ante desastres.

### <a name="deactivate"></a>Desactivación
La desactivación de una matriz virtual de StorSimple interrumpe la conexión entre el dispositivo y el servicio StorSimple Manager correspondiente. La desactivación es una operación **permanente** y no se puede deshacer. Un dispositivo desactivado no se puede registrar en el servicio StorSimple Manager de nuevo. Para más información, vaya a [Desactivación y eliminación de un dispositivo de StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Cuando desactive una matriz virtual, tenga en cuenta los siguientes procedimientos recomendados:

* Antes de desactivar cualquier dispositivo virtual tome una instantánea en la nube de todos los datos. Al desactivar una matriz virtual, se pierden todos los datos del dispositivo local. Si se toma una instantánea en la nube, los datos se podrán recuperar más adelante.
* Antes de desactivar una matriz virtual de StorSimple, asegúrese de detener o eliminar los clientes y hosts que dependen de dicho dispositivo.
* Elimine los dispositivos desactivados que no vaya a usar, con el fin de que no se acumulen cargos.

### <a name="monitoring"></a>Supervisión
Para asegurarse de que la matriz virtual de StorSimple está siempre en buen estado, es preciso que supervisar la matriz y se asegure de que recibe información del sistema, incluidas las alertas. Para supervisar el estado general de la matriz virtual, implemente los siguientes procedimientos recomendados:

* Configure la supervisión para realizar un seguimiento del uso del disco de datos de su matriz virtual, así como del disco del sistema operativo. Si se ejecuta Hyper-V, puede utilizar una combinación de System Center Virtual Machine Manager (SCVMM) y System Center Operations Manager (SCOM) para supervisar los hosts de virtualización.
* Configure las notificaciones de correo electrónico en la matriz virtual para que se envíen ante determinados niveles de uso.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Aplicaciones de detección de virus y búsqueda en índices
Una matriz virtual de StorSimple puede clasifica automáticamente los datos de la capa local en la nube de Microsoft Azure. Cuando se utiliza una aplicación detección de virus o búsqueda en índices para analizar los datos almacenados en StorSimple, es preciso tener cuidado de que no se puede acceder a los datos de nube ni extraerlos al nivel local.

Se recomienda implementar los siguientes procedimientos recomendados al configurar la detección de virus y la búsqueda en índices en la matriz virtual:

* Deshabilite todas las operaciones de detección configuradas automáticamente.
* En el caso de los volúmenes en capas, configure la aplicación de detección de virus y búsqueda en índices para realizar un análisis incremental. Así se explorarían solo los nuevos datos que probablemente residan en el nivel local. A los datos que están en capas en la nube no se acceden durante una operación incremental.
* Asegúrese de que están configurados los filtros de búsqueda y las opciones correctos, de modo que solo se examinen los tipos de archivo deseados. Por ejemplo, los archivos de imagen (JPEG, GIF y TIFF) y lo dibujos de ingeniería no deben analizarse durante la regeneración de un índice completo o incremental.

Si utiliza el proceso de indexación de Windows, siga estas directrices:

* No use el indexador de Windows para los volúmenes en capas, ya que recupera grandes cantidades de datos (TB) de la nube si el índice debe volver a generarse con frecuencia. Si se vuelve a genera el índice, se recuperarían todos los tipos de archivo para indexar su contenido.
* Use el proceso de indexación de Windows para volúmenes anclados localmente, ya que dicho proceso solo accedería a los datos de los niveles locales para generar el índice (no se accederá a los datos de la nube).

### <a name="byte-range-locking"></a>Bloqueo de intervalo de bytes
Las aplicaciones pueden bloquear en los archivos un intervalo de bytes especificado. Si el bloqueo del intervalo de bytes está habilitado en las aplicaciones que escriben en StorSimple, los niveles no funcionarán en su matriz virtual. Para que los niveles funcionen, es preciso desbloquear todas las áreas de los archivos a los que se accede. El bloqueo del intervalo de bytes no admite los volúmenes en capas de una matriz virtual.

Entre las medidas recomendadas para solucionar el bloqueo de intervalo de bytes se incluyen las siguientes:

* Desactive el bloqueo del intervalo de bytes en la lógica de la aplicación.
* Use volúmenes anclados localmente (en lugar de volúmenes en capas) para los datos asociados con esta aplicación. Los volúmenes anclados localmente no se organizan en capas en la nube.
* Si se usan volúmenes anclados localmente con el bloqueo de intervalo de bytes habilitado, el volumen puede conectarse antes de que finalice la restauración. En estos casos, se debe esperar hasta que se complete la restauración.

## <a name="multiple-arrays"></a>Varias matrices
Es posible que haya que implementar varias matrices virtuales en una cuenta para disponer de un creciente espacio de trabajo de datos que puedan diseminarse por la nube y, por tanto, afectar al rendimiento del dispositivo. En estos casos, se recomienda escalar los dispositivos a medida que el espacio de trabajo crece. Esto requiere que se agreguen uno o varios dispositivos al centro de datos local. Al agregar los dispositivos, puede:

* Dividir el conjunto de datos actual.
* Implementar nuevas cargas de trabajo en los nuevos dispositivos.
* Si se implementan varias matrices virtuales, se recomienda que, desde la perspectiva del equilibrio de carga, distribuya la matriz entre varios hosts de hipervisor.
* Se pueden implementar varias matrices virtuales (cuando se configuran un servidor de archivos o un servidor de iSCSI) en un espacio de nombres de un sistema de archivos distribuido. Para conocer los pasos detallados, vaya a la [Guía de la solución del espacio de nombres de un sistema de archivos distribuido con implementación de almacenamiento en la nube híbrido](https://www.microsoft.com/download/details.aspx?id=45507). En la actualidad no se recomienda usar la replicación del sistema de archivos distribuido con la matriz virtual. 

## <a name="see-also"></a>Otras referencias
Aprenda a [administrar una matriz virtual de StorSimple](storsimple-virtual-array-manager-service-administration.md) mediante el servicio StorSimple Manager.

