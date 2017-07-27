---
title: "Replicación de máquinas virtuales de VMware y servidores físicos en Azure (portal clásico heredado) | Microsoft Docs"
description: "Describe cómo replicar máquinas virtuales locales y servidores físicos de Windows/Linux en Azure utilizando Azure Site Recovery en una implementación heredada en el portal clásico."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: f980fb52-8ec2-4dd9-85e2-8e52e449f1ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: d50a4bdbafccd645ca339b2dd1ab97456704e3ae
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery-using-the-classic-portal-legacy"></a>Replicación de máquinas virtuales de VMware y servidores físicos en Azure con Azure Site Recovery usando el portal clásico (heredado)
> [!div class="op_single_selector"]
> * [Portal de Azure](site-recovery-vmware-to-azure.md)
> * [Portal clásico](site-recovery-vmware-to-azure-classic.md)
> * [Portal clásico (heredado)](site-recovery-vmware-to-azure-classic-legacy.md)
>
>

Bienvenido a Azure Site Recovery. Este artículo describe una implementación heredada para replicar en Azure máquinas virtuales de VMware locales o servidores físicos de Windows/Linux con Azure Site Recovery en el portal clásico.

## <a name="overview"></a>Información general
Las organizaciones necesitan una estrategia de recuperación ante desastres y continuidad empresarial (BCDR) que determine cómo seguirán en funcionamiento y disponibles las aplicaciones, las cargas de trabajo y los datos durante los tiempos de inactividad planeados y no planeados, y cómo recuperar las condiciones de funcionamiento normales lo antes posible. Su estrategia de BCDR se centra en soluciones que mantengan los datos empresariales seguros y recuperables, y garanticen que las cargas de trabajo estarán disponibles continuamente en caso de desastre.

Site Recovery es un servicio de Azure que contribuye a su estrategia de BCDR mediante la coordinación de la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error a la ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal. Más información en [¿Qué es Site Recovery?](site-recovery-overview.md)

> [!WARNING]
> Este artículo contiene **instrucciones heredadas**. No lo utilice para las nuevas implementaciones. En su lugar, [siga estas instrucciones](site-recovery-vmware-to-azure.md) para implementar Site Recovery en Azure Portal o [siga estas instrucciones](site-recovery-vmware-to-azure-classic.md) para configurar la implementación mejorada en el portal clásico. Si ya ha realizado la implementación mediante el método descrito en este artículo, le recomendamos que migre a la versión mejorada en el portal clásico.
>
>

## <a name="migrate-to-the-enhanced-deployment"></a>Migración a la implementación mejorada
Esta sección solo es relevante si ya ha implementado Site Recovery siguiendo las instrucciones de este artículo.

Para migrar la implementación existente, necesitará:

1. Implementar nuevos componentes de Site Recovery en el sitio local.
2. Configurar las credenciales para la detección automática de las máquinas virtuales de VMware en el nuevo servidor de configuración.
3. Detectar los servidores de VMware con el nuevo servidor de configuración.
4. Crear un nuevo grupo de protección con el nuevo servidor de configuración.

Antes de comenzar:

* Se recomienda que configure una ventana de mantenimiento para la migración.
* La opción **Migrar máquinas** estará disponible solo si tiene grupos de protección existentes que se crearon durante una implementación heredada.
* Una vez completados los pasos de la migración, puede tardar 15 minutos o más para actualizar las credenciales y para detectar y actualizar las máquinas virtuales de modo que pueda agregarlas a un grupo de protección. Puede actualizar manualmente en lugar de esperar.

Realice la migración de la siguiente forma:

1. Lea acerca de la [implementación mejorada en el portal clásico](site-recovery-vmware-to-azure-classic.md). Revise la [arquitectura mejorada](site-recovery-vmware-to-azure-classic.md) y los [requisitos previos](site-recovery-vmware-to-azure-classic.md).
2. Desinstale Movility Service en las máquinas que está replicando actualmente. Cuando agregue las máquinas a un grupo de protección nuevo, se instalará en ellas una nueva versión del servicio.
3. Descargue una [clave de registro del almacén](site-recovery-vmware-to-azure-classic.md) y ejecute el [Asistente para la instalación unificada](site-recovery-vmware-to-azure-classic.md) para instalar el servidor de configuración, el servidor de procesos y los componentes de servidor de destino maestro. Más información sobre el [planeamiento de capacidad](site-recovery-vmware-to-azure-classic.md).
4. [Configure credenciales](site-recovery-vmware-to-azure-classic.md) que Site Recovery pueda utilizar para tener acceso al servidor de VMware para detectar automáticamente las máquinas virtuales de VMware. Más información acerca de los [permisos necesarios](site-recovery-vmware-to-azure-classic.md).
5. Agregue [servidores vCenter o hosts vSphere](site-recovery-vmware-to-azure-classic.md). Puede tardar 15 minutos para que aparezcan más servidores en el portal de Site Recovery.
6. Cree un [nuevo grupo de protección](site-recovery-vmware-to-azure-classic.md). El portal puede tardar hasta 15 minutos en actualizarse y que se detecten y aparezcan las máquinas virtuales. Si no quiere esperar, puede resaltar el nombre del servidor de administración (no haga clic en él) > **Actualizar**.
7. En el nuevo grupo de protección, haga clic en **Migrar máquinas**.

    ![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration1.png)
8. En **Seleccionar máquinas** , seleccione el grupo de protección desde el que quiere migrar y las máquinas que se migrarán.

    ![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration2.png)
9. En **Configurar ajustes de destino** , especifique si desea utilizar la misma configuración para todas las máquinas y seleccione el servidor de proceso y la cuenta de almacenamiento de Azure. Si no tiene un servidor de procesos independiente esta será la dirección IP de servidor del servidor de configuración.

    ![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration3.png)

    > [AZURE.NOTE] No se admite la [migración de cuentas de almacenamiento](../resource-group-move-resources.md) entre grupos de recursos de la misma suscripción o entre suscripciones para cuentas de almacenamiento usadas para implementar Site Recovery.

1. En **Especificar cuentas**, seleccione la cuenta que creó para que el servidor de procesos acceda a la máquina e inserte la nueva versión de Mobility Service.

   ![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration4.png)
2. Site Recovery migrará los datos replicados a la cuenta de almacenamiento de Azure que proporcionó. Opcionalmente, puede volver a utilizar la cuenta de almacenamiento que empleó en la implementación heredada.
3. Una vez finalizado el trabajo, se sincronizarán automáticamente las máquinas virtuales. Una vez completada la sincronización, puede eliminar las máquinas virtuales del grupo de protección heredado.
4. Una vez se han migrado todos los equipos, puede eliminar el grupo de protección heredado.
5. Recuerde especificar las propiedades de conmutación por error para las máquinas y la configuración de red de Azure una vez completada la sincronización.
6. Si ya dispone de planes de recuperación, puede migrarlos a la implementación mejorada mediante la opción **Migrar plan de recuperación** . Solo debe hacerlo después de que se hayan migrado todas las máquinas protegidas.

   ![Agregar cuenta](./media/site-recovery-vmware-to-azure-classic-legacy/legacy-migration5.png)

> [!NOTE]
> Cuando termine la migración continúe con el [artículo mejorado](site-recovery-vmware-to-azure-classic.md). El resto de este artículo heredado ya no es relevante y no necesita seguir los pasos descritos en él**.
>
>

## <a name="what-do-i-need"></a>¿Qué necesito?
En este diagrama se muestran los componentes de implementación.

![Almacén nuevo](./media/site-recovery-vmware-to-azure-classic-legacy/architecture.png)

Esto es lo que necesita:

| **Componente** | **Implementación** | **Detalles** |
| --- | --- | --- |
| **Servidor de configuración** |Una máquina virtual A3 estándar de Azure en la misma suscripción que Site Recovery. |El servidor de configuración coordina la comunicación entre máquinas protegidas, el servidor de procesos y los servidores de destino maestros en Azure. Configura la replicación y coordina la recuperación en Azure cuando se produce la conmutación por error. |
| **Servidor de destino principal** |Una máquina virtual de Azure, como un servidor de Windows basado en una imagen de galería de Windows Server 2012 R2 (para proteger las máquinas de Windows) o como un servidor Linux basado en una imagen de galería de OpenLogic CentOS 6.6 (para proteger las máquinas Linux).<br/><br/> Hay tres opciones de tamaño disponibles: A4 estándar, D14 estándar y DS4 estándar.<br/><br/> El servidor está conectado a la misma red de Azure que el servidor de configuración.<br/><br/> Configuración en el portal de Site Recovery |Recibe y retiene los datos replicados de las máquinas protegidas usando discos duros virtuales asociados creados en Almacenamiento de blobs de su cuenta de almacenamiento de Azure.<br/><br/> Seleccione DS4 estándar específicamente para configurar la protección para las cargas de trabajo que requieren alto rendimiento y baja latencia coherentes mediante la cuenta de almacenamiento premium. |
| **Servidor de proceso** |Un servidor virtual o físico local que ejecute Windows Server 2012 R2<br/><br/> Se recomienda que se ubique en la misa red y segmento LAN que los equipos que desea proteger, pero se puede ejecutar en una red diferente siempre que los equipos protegidos tengan visibilidad de red L3 en él.<br/><br/> Configuración y registro en el servidor de configuración en el portal de Site Recovery. |Las maquinas protegidas envían datos de replicación al servidor de procesos local. Dispone de una caché basada en disco para almacenar en caché los datos de replicación que recibe. Realiza una serie de acciones en los datos.<br/><br/> Optimiza los datos almacenándolos en la caché, comprimiéndolos y cifrándolos antes de enviarlos al servidor de destino maestro.<br/><br/> Controla la instalación de inserción de Mobility Service.<br/><br/> Realiza la detección automática de las máquinas virtuales de VMware. |
| **Máquinas locales** |Máquinas virtuales de VMware locales o servidores físicos ejecutándose en Windows o Linux. |Configurar los ajustes de replicación que se aplican uno o varias máquinas. Puede crear una conmutación por error en una máquina individual o, más generalmente, en varias máquinas virtuales que reúna en un plan de recuperación. |
| **Servicio de movilidad** |Instalado en cada máquina virtual o servidor físico que quiere proteger<br/><br/> Se puede instalar manualmente o insertar e instalar automáticamente mediante el servidor de procesos cuando se habilita la replicación para una máquina. |Mobility Service envía datos al servidor de procesos durante la replicación inicial (resincronización). Después de que la máquina está en un estado protegido (después de que finalice la resincronización) Mobility Service captura escrituras en disco en la memoria y las envía al servidor de procesos. La coherencia de las aplicaciones para servidores Windows se logra mediante VSS. |
| **Almacén de Azure Site Recovery** |Puede crear un almacén de Site Recovery con una suscripción de Azure y registrar servidores en el almacén. |El almacén coordina y organiza la réplica de datos, la conmutación por error y la recuperación entre el sitio local y Azure. |
| **Mecanismo de replicación** |**A través de Internet**: se comunican y replican datos de servidores locales protegidos y Azure usando un canal SSL/TLS seguro a través de Internet. Esta es la opción predeterminada.<br/><br/> **VPN/ExpressRoute**: se comunica y replica datos entre servidores locales y Azure a través de una conexión VPN. Tiene que configurar una VPN de sitio a sitio o una conexión ExpressRoute entre el sitio local y la red de Azure.<br/><br/> Seleccione cómo desea replicar durante la implementación de Site Recovery. No se puede cambiar el mecanismo después de configurarlo sin afectar a la replicación de las máquinas existentes. |Ninguna opción requiere que se abra ningún puerto de red entrante en las máquinas protegidas. Todas las comunicaciones de red se inician en el sitio local. |

## <a name="capacity-planning"></a>planeamiento de capacidad
Las áreas principales que tiene que tener en cuenta:

* **Entorno de origen**: la infraestructura, la configuración de la máquina de origen y los requisitos de VMware.
* **Servidores de componentes**: el servidor de procesos, el servidor de configuración y el servidor de destino principal.

### <a name="considerations-for-the-source-environment"></a>Consideraciones para el entorno de origen
* **Tamaño máximo de disco**: el tamaño máximo actual del disco que se puede adjuntar a una máquina virtual es 1 TB. Por lo tanto, el tamaño máximo de un disco de origen que se puede replicar también se limita a 1 TB.
* **Tamaño máximo por origen**: el tamaño máximo de una máquina de origen único es 31 TB (con 31 discos) y con una instancia de D14 aprovisionada para el servidor de destino principal.
* **Número de orígenes por servidor de destino principal**: se pueden proteger varias máquinas de origen con un único servidor de destino principal. No obstante, no se puede proteger una única máquina de origen en varios servidores de destino principales porque cuando se replican los discos, se crea un disco duro virtual que refleja el tamaño del disco en el almacenamiento de blobs de Azure y se adjunta como un disco de datos al servidor de destino principal.  
* **Frecuencia de cambio diaria máxima por origen**: hay tres factores que hay que tener en cuenta al considerar la frecuencia de cambio recomendada por origen. Para las consideraciones basadas en destino, se necesitan dos E/S por segundo en el disco de destino para cada operación en el origen. Esto se debe a que se producirá una lectura de datos anteriores y una escritura de datos nuevos en el disco de destino.
  * **Frecuencia de cambio diaria admitida por el servidor de procesos**: una máquina de origen no puede abarcar varios servidores de procesos. Un servidor de un solo proceso puede admitir hasta 1 TB de frecuencia de cambio diaria. Por lo tanto, 1 TB es la frecuencia de cambio de datos diaria máxima admitida para una máquina de origen.
  * **Rendimiento máximo compatible con el disco de destino**: la renovación máxima por disco de origen no puede ser superior a 144 GB/día (con tamaño de escritura de 8K). Consulte la tabla en la sección de destino principal para el rendimiento y la E/S por segundo del destino para distintos tamaños de escritura. Este número debe dividirse entre dos porque cada E/S por segundo de origen genera 2 E/S por segundo en el disco de destino. Conozca los [objetivos de escalabilidad y rendimiento de Azure](../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) al configurar el destino para las cuentas de Premium Storage.
  * **Rendimiento máximo admitido por la cuenta de almacenamiento**: un origen no puede abarcar varias cuentas de almacenamiento. Dado que una cuenta de almacenamiento tiene un máximo de 20.000 solicitudes por segundo y que cada E/S por segundo de origen genera 2 E/S por segundo en el servidor de destino principal, se recomienda mantener el número de E/S por segundo en el origen a 10.000. Conozca los [objetivos de escalabilidad y rendimiento de Azure](../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) al configurar el origen para las cuentas de Premium Storage.

### <a name="considerations-for-component-servers"></a>Consideraciones para servidores de componentes
En la tabla 1 se resumen los tamaños de máquina virtual para la configuración y los servidores de destino principales.

| **Componente** | **Instancias de Azure implementadas** | **Núcleos** | **Memoria** | **Discos máx.** | **Tamaño del disco** |
| --- | --- | --- | --- | --- | --- |
| Servidor de configuración |A3 estándar |4 |7 GB |8 |1023 GB |
| Servidor de destino principal |A4 estándar |8 |14 GB |16 |1023 GB |
| D14 estándar |16 |112 GB |32 |1023 GB | |
| DS4 estándar |8 |28 GB |16 |1023 GB | |

**Tabla 1**

#### <a name="process-server-considerations"></a>Consideraciones acerca del servidor de procesos
Por lo general, el tamaño del servidor de procesos depende de la frecuencia de cambios diaria a través de todas las cargas de trabajo protegidas.

* Necesita cálculo suficiente para realizar tareas tales como el cifrado y la compresión en línea.
* El servidor de proceso utiliza la caché basada en disco. Asegúrese de que el espacio de memoria caché recomendado y el rendimiento de disco están disponibles para facilitar los cambios de datos almacenados en el caso de interrupción o cuello de botella de red.
* Asegúrese de que hay ancho de banda suficiente para que el servidor de proceso pueda cargar los datos en el servidor de destino principal para proporcionar una protección continua de los datos.

En la tabla 2 se proporciona un resumen de las instrucciones del servidor de proceso.

| **Frecuencia de cambio de datos** | **CPU** | **Memoria** | **Tamaño del disco de caché** | **Rendimiento del disco de caché** | **Entrada/salida de ancho de banda** |
| --- | --- | --- | --- | --- | --- |
| < 300 GB |4 vCPU (2 sockets * 2 núcleos @ 2,5 GHz) |4 GB |600 GB |7 a 10 MB por segundo |30 Mbps/21 Mbps |
| 300 a 600 GB |8 vCPU (2 sockets * 4 núcleos @ 2,5 GHz) |6 GB |600 GB |11 a 15 MB por segundo |60 Mbps/42 Mbps |
| 600 GB a 1 TB |12 vCPU (2 sockets * 6 núcleos @ 2,5 GHz) |8 GB |600 GB |16 a 20 MB por segundo |100 Mbps/70 Mbps |
| > 1 TB |Implementar otro servidor de procesos | | | | |

**Tabla 2**

Donde:

* Entrada es el ancho de banda de descarga (intranet entre el servidor de origen y de procesos).
* Salida es el ancho de banda de carga (Internet entre el servidor de procesos y el servidor de destino principal). Los números de salida suponen una compresión promedio de servidor de procesos del 30%.
* Para la caché de disco, se recomienda un disco de sistema operativo independiente de 128 GB mínimo para todos los servidores de procesos.
* Para el rendimiento de disco de memoria caché, el almacenamiento siguiente se usó para pruebas comparativas: 8 unidades SAS de 10 K RPM con configuración RAID 10.

#### <a name="configuration-server-considerations"></a>Consideraciones del servidor de configuración
Cada servidor de configuración puede admitir hasta 100 máquinas de origen 3 y 4 volúmenes. Si la implementación es mayor se recomienda implementar otro servidor de configuración. Consulte la tabla 1 para conocer las propiedades de la máquina virtual predeterminadas del servidor de configuración.

#### <a name="master-target-server-and-storage-account-considerations"></a>Consideraciones de la cuenta de almacenamiento y el servidor de destino principal
El almacenamiento para cada servidor de destino principal incluye un disco del sistema operativo, un volumen de retención y discos de datos. La unidad de retención mantiene el diario de cambios del disco para la duración de la ventana de retención definida en el portal de Site Recovery.  Consulte la tabla 1 para conocer las propiedades de la máquina virtual del servidor de destino principal. En la tabla 3 se muestra cómo se utilizan los discos de A4.

| **Instancia** | **Disco del sistema operativo** | **Retención** | **Discos de datos** |
| --- | --- | --- | --- |
| **Retención** |**Discos de datos** | | |
| A4 estándar |1 disco (1 * 1023 GB) |1 disco (1 * 1023 GB) |15 discos (15 * 1023 GB) |
| D14 estándar |1 disco (1 * 1023 GB) |1 disco (1 * 1023 GB) |31 discos (15 * 1023 GB) |
| DS4 estándar |1 disco (1 * 1023 GB) |1 disco (1 * 1023 GB) |15 discos (15 * 1023 GB) |

**Tabla 3**

La planificación de la capacidad del servidor de destino principal depende de:

* Las limitaciones y el rendimiento de almacenamiento de Azure
  * El número máximo de discos muy usados para una máquina virtual de nivel estándar, es de aproximadamente 40 (20.000/500 IOPS por disco) en una única cuenta de almacenamiento. Conozca los [objetivos de escalabilidad de las cuentas de almacenamiento estándar](../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) y de las [cuentas de Premium Storage](../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks).
* Frecuencia de cambio de datos
* Almacenamiento de volúmenes de retención.

Observe lo siguiente:

* Un origen no puede abarcar varias cuentas de almacenamiento. Esto se aplica al disco de datos va a las cuentas de almacenamiento seleccionadas al configurar la protección. El sistema operativo y los discos de retención normalmente van a la cuenta de almacenamiento implementada automáticamente.
* El volumen de almacenamiento de retención necesario depende de la frecuencia de cambios diaria y el número de días de retención. El almacenamiento de retención requerido por el servidor de destino principal = renovación total del origen al día * número de días de retención.
* Cada servidor de destino principal tiene solo un volumen de retención. El volumen de retención se comparte entre los discos conectados al servidor de destino principal. Por ejemplo:
  * Si hay una máquina de origen con 5 discos y cada disco genera 120 E/S por segundo (tamaño de 8 KB) en el origen, esto se traduce en 240 E/S por segundo por disco (dos operaciones en el disco de destino por cada E/S de origen). 240 E/S por segundo se encuentran dentro de Azure por límite de E/S por segundo de disco de 500.
  * En el volumen de retención, esto se convierte en 120 * 5 = 600 E/S por segundo y esto puede convertirse en un cuello de botella. En este escenario, una buena estrategia sería agregar más discos al volumen de retención y distribuirlos, como una configuración de bandas RAID. Esto mejorará el rendimiento porque las E/S por segundo se distribuyen en varias unidades. El número de unidades que se agregarán al volumen de retención será el siguiente:
    * E/S por segundo total del entorno de origen / 500
    * Renovación total por día desde el entorno de origen (sin comprimir) / 287 GB. 287 GB es el rendimiento máximo compatible con un disco de destino por día. Esta métrica variará según el tamaño de escritura con un factor de 8K, porque en este caso, 8K es el tamaño de escritura asumido. Por ejemplo, si el tamaño de escritura es 4K, el rendimiento será 287/2. Y, si el tamaño de escritura es 16K, el rendimiento será 287*2.
* El número de cuentas de almacenamiento necesarias = E/S por segundo de origen total/10.000.

## <a name="before-you-start"></a>Antes de comenzar
| **Componente** | **Requisitos** | **Detalles** |
| --- | --- | --- |
| **Cuenta de Azure** |Necesitará una cuenta de [Microsoft Azure](https://azure.microsoft.com/) . Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). | |
| **Almacenamiento de Azure** |Necesitará una cuenta de almacenamiento de Azure para almacenar los datos replicados<br/><br/> La cuenta debe ser una [cuenta de almacenamiento estándar](../storage/storage-redundancy.md#geo-redundant-storage) con redundancia geográfica o una [cuenta de Premium Storage](../storage/storage-premium-storage.md).<br/><br/> Debe estar en la misma región que el servicio Azure Site Recovery y debe estar asociado a la misma suscripción. No se admite el traslado de cuentas de almacenamiento creadas con el [nuevo Portal de Azure](../storage/storage-create-storage-account.md) entre grupos de recursos.<br/><br/> Para más información, consulte [Introducción a Microsoft Azure Storage](../storage/storage-introduction.md) | |
| **Red virtual de Azure** |Necesitará una red virtual en la que se implementarán el servidor de configuración y el servidor de destino principal. Debe estar en la misma región y suscripción que el almacén de Azure Site Recovery. Si desea replicar datos a través de una conexión VPN o ExpressRoute, la red virtual de Azure debe estar conectada a su red local a través de una conexión de ExpressRoute o una VPN de sitio a sitio. | |
| **Recursos de Azure** |Asegúrese de que tiene recursos de Azure suficientes para implementar todos los componentes. Más información en [Límites de suscripción de Azure](../azure-subscription-service-limits.md). | |
| **Máquinas virtuales de Azure** |Las máquinas virtuales que quiere proteger deben cumplir los [requisitos previos de Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).<br/><br/> **Recuento de disco**: se admite un máximo de 31 discos en un único servidor protegido<br/><br/> **Tamaños de disco**: la capacidad del disco individual no debe superar los 1023 GB<br/><br/> **Agrupación en clústeres**: no se admiten los servidores en clúster<br/><br/> **Arranque**: no se admite el arranque de Unified Extensible Firmware Interface (UEFI)/Extensible Firmware Interface (EFI)<br/><br/> **Volúmenes**: no se admiten los volúmenes cifrados de Bitlocker<br/><br/> **Nombres de servidor**: los nombres deben contener entre 1 y 63 caracteres (letras, números y guiones). El nombre debe comenzar con una letra o un número y terminar con una letra o un número. Después de proteger un equipo, puede modificar el nombre de Azure. | |
| **Servidor de configuración** |Se crea en su suscripción una máquina virtual A3 estándar basada en una imagen de la galería de Windows Server 2012 R2 de Azure Site Recovery para el servidor de configuración. Se crea como la primera instancia de un nuevo servicio en la nube. Si selecciona Internet pública como el tipo de conectividad para el servidor de configuración, el servicio en la nube se creará con una dirección IP pública reservada.<br/><br/> La ruta de instalación solo debe tener caracteres de inglés. | |
| **Servidor de destino principal** |Máquina virtual de Azure estándar A4, D14 o DS4.<br/><br/> La ruta de instalación solo debe tener caracteres de inglés. Por ejemplo, la ruta debe ser **/usr/local/ASR** para un servidor de destino principal que ejecuta Linux. | |
| **Servidor de proceso** |Puede implementar el servidor de proceso en la máquina virtual o física que ejecuta Windows Server 2012 R2 con las actualizaciones más recientes. Instálelo en la unidad C:/.<br/><br/> Se recomienda que ubique el servidor en la misma red y subred que los equipos que desea proteger.<br/><br/> Instale VMware vSphere CLI 5.5.0 en el servidor de procesos. El componente de CLI de VMware vSphere es necesario en el servidor de procesos para detectar máquinas virtuales administradas por un servidor vCenter o máquinas virtuales que se ejecutan en un host ESXi.<br/><br/> La ruta de instalación solo debe tener caracteres de inglés.<br/><br/> No se admite el sistema de archivos ReFS. | |
| **VMware** |Un servidor VMWare vCenter que administra los hipervisores de VMware vSphere. Debe ejecutar vCenter versión 5.1 o 5.5 con las actualizaciones más recientes.<br/><br/> Uno o varios de los hipervisores de vSphere contienen las máquinas virtuales de VMWare que desea proteger. El hipervisor debe estar ejecutando ESX/ESXi versión 5.1 o 5.5 con las actualizaciones más recientes.<br/><br/> Las máquinas virtuales de VMware deben tener herramientas de VMware instaladas y en ejecución. | |
| **Máquinas de Windows** |Los servidores físicos protegidos o las máquinas virtuales VMWare con Windows tienen una serie de requisitos.<br/><br/> Un sistema operativo de 64 bits compatible: **Windows Server 2012 R2**, **Windows Server 2012** o **Windows Server 2008 R2 con SP1 como mínimo**.<br/><br/> El nombre de host, los puntos de montaje, los nombres de dispositivo y la ruta de acceso de sistema de Windows (p. ej.: C:\Windows) deben estar en inglés únicamente.<br/><br/> El sistema operativo debe instalarse en la unidad C:\.<br/><br/> Solo se admiten discos básicos. No se admiten discos dinámicos.<br/><br/> Las reglas de firewall en los equipos protegidos deben permitirles llegar a la configuración y a los servidores de destino principales de Azure.p><p>Tendrá que proporcionar una cuenta de administrador (debe ser un administrador local en el equipo de Windows) para la instalación de inserción de Mobility Service en los servidores de Windows. Si la cuenta proporcionada no es una cuenta de dominio, deberá deshabilitar el control de acceso de usuarios remotos en el equipo local. Para ello, agregue la entrada del Registro LocalAccountTokenFilterPolicy DWORD con un valor de 1 en HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Para agregar la entrada del Registro de un CLI, abra cmd o powershell y escriba **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. [Más información](https://msdn.microsoft.com/library/aa826699.aspx) sobre el control de acceso.<br/><br/> Después de la conmutación por error, si desea conectarse a máquinas virtuales de Microsoft Azure con Escritorio remoto, asegúrese de que Escritorio remoto está habilitado en el equipo local. Si no se conecta a través de VPN, las reglas de firewall deben permitir las conexiones de escritorio remoto a través de Internet. | |
| **Equipos Linux** |Un sistema operativo de 64 bits admitido: **Centos 6.4, 6.5, 6.6**; **Oracle Enterprise Linux 6.4, 6.5 que ejecute el kernel compatible de Red Hat o Unbreakable Enterprise Kernel versión 3 (UEK3)**, **SUSE Linux Enterprise Server 11 SP3**.<br/><br/> Las reglas de firewall en los equipos protegidos deben permitirles llegar a la configuración y a los servidores de destino principales de Azure.<br/><br/> Los archivos /etc/hosts de los equipos protegidos deben contener entradas que asignen el nombre de host local a las direcciones IP asociadas con todas las NIC <br/><br/> Si desea conectarse a la máquina virtual de Azure con Linux después de la conmutación por error mediante un cliente Secure Shell (ssh), asegúrese de que el servicio de Secure Shell en la máquina protegida esté configurado para iniciarse automáticamente en el arranque del sistema y que las reglas de firewall permitan un ssh conexión a ella.<br/><br/> El nombre del host, los puntos de montaje, los nombres de dispositivo, las rutas de acceso de sistema de Linux y los nombres de archivo (por ejemplo, /etc/, /usr) deben estar en inglés únicamente.<br/><br/> La protección puede habilitarse para los equipos locales con el almacenamiento siguiente:-<br>Sistema de archivos: EXT3, ETX4, ReiserFS, XFS<br>Software de múltiples rutas / asignador de dispositivos (múltiples rutas)<br>Administrador de volúmenes: LVM2<br>No se admiten servidores físicos con almacenamiento de controlador HP CCISS. | |
| **Terceros** |Algunos componentes de implementación de este escenario dependen de software de terceros para poder funcionar correctamente. Para obtener una lista completa, consulte [Avisos e información de software de terceros](#third-party) | |

### <a name="network-connectivity"></a>Conectividad de red
Tiene dos opciones para configurar la conectividad de red entre el sitio local y la red virtual de Azure en la que se implementan los componentes de infraestructura (servidor de configuración, servidores de destino maestros). Deberá decidir qué opción de conectividad de red usar para poder implementar su servidor de configuración. Debe elegir esta opción en el momento de la implementación. No puede cambiarse más adelante.

**Internet:** la comunicación y la replicación de datos entre los servidores locales (servidor de procesos, máquinas protegidas) y los servidores de los componentes de infraestructura de Azure (servidor de configuración, servidor de destino principal) se realizan mediante una conexión SSL/TLS segura entre el entorno local y los puntos de conexión públicos de los servidores de configuración y de destino maestro. (La única excepción es la conexión entre el servidor de procesos y el servidor de destino maestro en el puerto TCP 9080 que es sin cifrar. Solo se intercambia en esta conexión la información de control relacionada con el protocolo de replicación usado para configurar la replicación.)

![Diagrama de implementación Internet](./media/site-recovery-vmware-to-azure-classic-legacy/internet-deployment.png)

**VPN:**la comunicación y la replicación de datos entre los servidores locales (servidor de procesos, máquinas protegidas) y los servidores de componentes de infraestructura de Azure (servidor de configuración, servidor de destino maestro) se realizan mediante una conexión VPN entre su red local y la red virtual de Azure en la que están implementados el servidor de configuración y los servidores de destino maestros. Asegúrese de que su red local esté conectada a la red virtual de Azure mediante una conexión de ExpressRoute o una conexión VPN de sitio a sitio.

![Diagrama de implementación VPN](./media/site-recovery-vmware-to-azure-classic-legacy/vpn-deployment.png)

## <a name="step-1-create-a-vault"></a>Paso 1: Creación de un almacén
1. Inicie sesión en el [Portal de administración](https://portal.azure.com).
2. Expanda **Data Services** > **Recovery Services** y haga clic en **Almacén de Site Recovery**.
3. Haga clic en **Crear nuevo** > **Creación rápida**.
4. En **Nombre**, escriba un nombre descriptivo para identificar el almacén.
5. En **Región**, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Haga clic en **Crear almacén**.

    ![Almacén nuevo](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-create-vault.png)

Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como **Activo** en la página principal de **Recovery Services**.

## <a name="step-2-deploy-a-configuration-server"></a>Paso 2: Implementación de un servidor de configuración
### <a name="configure-server-settings"></a>Configuración del servidor
1. En la página **Servicios de recuperación** , haga clic en el almacén para abrir la página Inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

    ![Icono de inicio rápido](./media/site-recovery-vmware-to-azure-classic-legacy/quick-start-icon.png)
2. En la lista desplegable, seleccione **Entre un sitio local con VMware/servidores físicos y Azure**.
3. En **Preparar los recursos de destino (Azure)**, haga clic en **Implementar el servidor de configuración**.

    ![Implementar el servidor de configuración](./media/site-recovery-vmware-to-azure-classic-legacy/deploy-cs2.png)
4. En **Nuevos detalles del servidor de configuración** , especifique:

   * Un nombre del servidor de configuración y las credenciales para conectarse a él.
   * En la lista desplegable de tipos de conectividad de red, seleccione **Internet pública** o **VPN**. Tenga en cuenta que una vez que se aplica esta configuración no se puede modificar.
   * Seleccione la red en la que se debe encontrar el servidor. Si utiliza VPN compruebe que la red de Azure está conectada a su red local tal como se espera.
   * Especifique la dirección IP interna y la subred que se asignarán al servidor. Tenga en cuenta que las cuatro primeras direcciones IP en las subredes están reservadas para uso interno de Azure. Utilice cualquier dirección IP disponible.

     ![Implementar el servidor de configuración](./media/site-recovery-vmware-to-azure-classic-legacy/cs-details.png)
5. Al hacer clic en **Aceptar** se crea en su suscripción una máquina virtual de A3 estándar basada en una imagen de la galería de Windows Server 2012 R2 de Azure Site Recovery para el servidor de configuración. Se crea como la primera instancia de un nuevo servicio en la nube. Si ha seleccionado para conectarse a través de Internet, el servicio en la nube se crea con una dirección IP pública reservada. Puede supervisar el progreso en la pestaña **Trabajos** .

    ![Supervisión de progreso](./media/site-recovery-vmware-to-azure-classic-legacy/monitor-cs.png)
6. Si se conecta a través de Internet, después de implementar el servidor de configuración, tome nota de la dirección IP pública asignada a él en la página **Máquinas virtuales** del Portal de Azure. Después en la pestaña **Extremos** , tome nota del puerto HTTPS asignado al puerto privado 443. Necesitará esta información más adelante al registrar el destino principal y los servidores de proceso con el servidor de configuración. El servidor de configuración se implementa con estos extremos:

   * HTTP: un puerto público que se usa para coordinar la comunicación entre los servidores de componentes y Azure a través de Internet. El puerto privado 443 se usa para coordinar la comunicación entre los servidores de componentes y Azure a través de la VPN.
   * Personalizado: un puerto público se utiliza para la comunicación de la herramienta de conmutación por recuperación a través de la conexión de Internet. El puerto privado 9443 se usa para la herramienta de conmutación por recuperación a través de una VPN.
   * PowerShell: puerto privado 5986
   * Escritorio remoto: puerto 3389 privado

   ![Extremos de VM](./media/site-recovery-vmware-to-azure-classic-legacy/vm-endpoints.png)

   > [!WARNING]
   > No eliminar o cambiar el número de puerto público o privado de cualquiera de los puntos de conexión creados durante la implementación del servidor de configuración.
   >
   >

El servidor de configuración se implementa en un servicio en la nube de Azure creado automáticamente con una dirección IP reservada. Se necesita la dirección reservada para garantizar que la dirección IP del servicio en la nube del servidor de configuración es la misma al arrancar las máquinas virtuales (incluido el servidor de configuración) en el servicio en la nube. Es necesario anular manualmente la reserva de la dirección IP pública reservada cuando se retira el servidor de configuración o permanecerá reservada. Hay un límite predeterminado de 20 direcciones IP públicas reservadas por suscripción. [Más información](../virtual-network/virtual-networks-reserved-private-ip.md) sobre las direcciones IP reservadas.

### <a name="register-the-configuration-server-in-the-vault"></a>Registro del servidor de configuración en el almacén
1. En la página **Inicio rápido**, haga clic en **Preparar recursos de destino** > **Descargar una clave de registro**. El archivo de clave se genera automáticamente. Es válido durante 5 días a partir del momento en que se genera. Copie el archivo en el servidor de configuración.
2. En **Máquinas virtuales** seleccione el servidor de configuración en la lista de máquinas virtuales. Abra la pestaña **Panel** y haga clic en **Conectar**. **Abra** el archivo RDP descargado para iniciar sesión en el servidor de configuración con Escritorio remoto. Si utiliza VPN, utilice la dirección IP interna (la dirección que especificó cuando implementó el servidor de configuración) para una conexión de escritorio remoto desde el sitio local. Al iniciar sesión por primera vez, se ejecuta automáticamente el Asistente para la configuración del servidor de configuración de Azure Site Recovery.

    ![Registro](./media/site-recovery-vmware-to-azure-classic-legacy/splash.png)
3. En **Instalación de Software de terceros**, haga clic en **Acepto** para descargar e instalar MySQL.

    ![Instalación de MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-eula.png)
4. En **Detalles de MySQL Server** , cree credenciales para iniciar sesión en la instancia del servidor MySQL.

    ![Credenciales de MySQL](./media/site-recovery-vmware-to-azure-classic-legacy/sql-password.png)
5. En **Configuración de Internet** , especifique cómo el servidor de configuración se conectará a Internet. Observe lo siguiente:

   * Si desea utilizar un proxy personalizado, debe configurarlo antes de instalar el proveedor.
   * Si hace clic en **Siguiente** , se ejecutará una prueba para comprobar la conexión del proxy.
   * Si utiliza a un proxy personalizado o el proxy predeterminado requiere autenticación, tendrá que especificar los detalles del proxy, incluida la dirección, el puerto y las credenciales.
   * Las siguientes direcciones URL deben ser accesibles a través del proxy:
     * *.hypervrecoverymanager.windowsazure.com
     * *.accesscontrol.windows.net
     * *.backup.windowsazure.com
     * *.blob.core.windows.net
     * *.store.core.windows.net
   * Si dispone de reglas de firewall basadas en direcciones IP, asegúrese de que están configuradas para permitir la comunicación desde el servidor de configuración hasta las direcciones IP descritas en [Intervalos de direcciones IP de los centros de datos de Azure](https://msdn.microsoft.com/library/azure/dn175718.aspx) y el protocolo HTTPS (443). Tendrá que incluir en una lista blanca los intervalos de direcciones IP de la región de Azure que va a usar y los del Oeste de EE. UU.

     ![Registro de proxy](./media/site-recovery-vmware-to-azure-classic-legacy/register-proxy.png)
6. En **Configuración de localización de mensajes de error de proveedores** , especifique en qué idioma desea que aparezcan los mensajes de error.

    ![Registro de mensajes de error](./media/site-recovery-vmware-to-azure-classic-legacy/register-locale.png)
7. En la página **Registro de Azure Site Recovery** , busque el archivo de clave que copió en el servidor.

    ![Registro de archivo de clave](./media/site-recovery-vmware-to-azure-classic-legacy/register-vault.png)
8. En la página de finalización del asistente, seleccione estas opciones:

   * Seleccione **Iniciar el cuadro de diálogo Administración de cuentas** para especificar que se debe abrir el cuadro de diálogo Administrar cuentas después de finalizar el asistente.
   * Seleccione **Crear un icono de escritorio para Cspsconfigtool** para agregar un acceso directo en el servidor de configuración para que pueda abrir el cuadro de diálogo **Administrar cuentas** en cualquier momento sin necesidad de volver a ejecutar el asistente.

     ![Registro completado](./media/site-recovery-vmware-to-azure-classic-legacy/register-final.png)
9. Haga clic en **Finalizar** para completar el asistente. Se genera una frase de contraseña. Cópiela en una ubicación segura. La necesitará para autenticarse y registrar los servidores de destino principales y de proceso con el servidor de configuración. También se utiliza para garantizar la integridad del canal en las comunicaciones del servidor de configuración. Puede volver a generar la frase de contraseña pero, a continuación, deberá volver a registrar el destino principal y los servidores de proceso mediante la nueva frase de contraseña.

    ![Frase de contraseña](./media/site-recovery-vmware-to-azure-classic-legacy/passphrase.png)

Después de registrar el servidor de configuración, se mostrará en la página **Servidores de configuración** del almacén.

### <a name="set-up-and-manage-accounts"></a>Configuración y administración de cuentas
Durante la implementación , Site Recovery solicita credenciales para las siguientes acciones:

* Una cuenta de VMware para que Site Recovery pueda detectar automáticamente las máquinas virtuales en los servidores vCenter o los hosts de vSphere.
* Al agregar equipos para la protección, para que Site Recovery pueda instalar Mobility Service en ellos.

Después de que se haya registrado el servidor de configuración, puede abrir el cuadro de diálogo **Administrar cuentas** para agregar y administrar las cuentas que deben usarse para estas acciones. Hay un par de formas de hacerlo:

* Abra el acceso directo que se utilizó para crear el cuadro de diálogo en la última página del programa de instalación para el servidor de configuración (cspsconfigtool).
* Abra el cuadro de diálogo al finalizar la instalación del servidor de configuración.

1. En **Administrar cuentas** click **Agregar cuenta**. También puede modificar y eliminar las cuentas existentes.

    ![Administrar cuentas](./media/site-recovery-vmware-to-azure-classic-legacy/manage-account.png)
2. En **Detalles de la cuenta** , especifique un nombre de cuenta para usarlo en Azure y credenciales (nombre de usuario/dominio).

    ![Administrar cuentas](./media/site-recovery-vmware-to-azure-classic-legacy/account-details.png)

### <a name="connect-to-the-configuration-server"></a>Conexión al servidor de configuración
Hay dos maneras de conectarse al servidor de configuración:

* Mediante una conexión VPN de sitio a sitio o ExpressRoute
* A través de Internet

Observe lo siguiente:

* Una conexión a Internet utiliza los extremos de la máquina virtual junto con la dirección IP virtual pública del servidor.
* Una conexión VPN utiliza la dirección IP interna del servidor junto con los puertos privados del extremo.
* Es una decisión única decidir si desea conectarse (control y replicación de datos) de los servidores locales a los distintos servidores de componente (servidor de configuración, servidor de destino principal) que se ejecutan en Azure a través de una conexión VPN o de Internet. No puede cambiar esta configuración posteriormente. Si lo hace, necesitará volver a implementar el escenario y volver a proteger sus equipos.  

## <a name="step-3-deploy-the-master-target-server"></a>Paso 3: Implementación del servidor de destino principal
1. Haga clic en **Preparar recursos de destino (Azure)** > **Implementar el servidor de destino maestro**.
2. Especifique las credenciales y los detalles del servidor de destino principal. El servidor se implementará en la misma red de Azure que el servidor de configuración. Al hacer clic para completar una máquina virtual de Azure, se creará con una imagen de galería de Windows o Linux.

    ![Configuración de servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/target-details.png)

Tenga en cuenta que las cuatro primeras direcciones IP en las subredes están reservadas para uso interno de Azure. Especifique cualquier dirección IP disponible.

> [!NOTE]
> Seleccione DS4 estándar al configurar la protección para cargas de trabajo que requieren sistemáticamente alto rendimiento de E/S y latencia baja para hospedar cargas de trabajo intensivas de E/S mediante [cuentas de almacenamiento premium](../storage/storage-premium-storage.md).
>
>

1. Se crea una máquina virtual de servidor de destino maestro de Windows con estos puntos de conexión. Tenga en cuenta que los puntos de conexión públicos se crean solo si la conexión se realiza a través de Internet.

   * Personalizado: puerto público utilizado por el servidor de procesos para enviar datos de replicación a través de Internet. El puerto privado 9443 lo utiliza el servidor de procesos para enviar datos de replicación al servidor de destino principal a través de VPN.
   * Personalizado1: puerto público utilizado por el servidor de procesos para enviar metadatos a través de Internet. El puerto privado 9080 lo utiliza el servidor de procesos para enviar metadatos al servidor de destino maestro a través de VPN.
   * PowerShell: puerto privado 5986
   * Escritorio remoto: puerto 3389 privado
2. Se crea una máquina virtual de servidor de destino maestro de Linux con estos puntos de conexión. Tenga en cuenta que los puntos de conexión públicos se crean solo si la conexión se realiza a través de Internet.

   * Personalizado: puerto público utilizado por el servidor de procesos para enviar datos de replicación a través de Internet. El puerto privado 9443 lo utiliza el servidor de procesos para enviar datos de replicación al servidor de destino principal a través de VPN.
   * Personalizado1: puerto público utilizado por el servidor de procesos para enviar metadatos a través de Internet. El puerto privado 9080 lo utiliza el servidor de procesos para enviar metadatos al servidor de destino maestro a través de VPN.
   * SSH: puerto privado 22

     > [!WARNING]
     > No elimine ni cambie el número de puerto público o privado de cualquiera de los extremos creados durante la implementación del servidor de destino principal.
     >
     >
3. En **Máquinas virtuales** , espere a que se inicie la máquina virtual.

   * Si es un servidor de Windows, tome nota los detalles del escritorio remotos.
   * Si se trata de un servidor Linux y se está conectando a través de VPN, anote la dirección IP interna de la máquina virtual. Si se conecta a través de Internet, anote la dirección IP pública.
4. Inicie sesión en el servidor para completar la instalación y registrarlo con el servidor de configuración.
5. Si está ejecutando Windows:

   1. Inicie una conexión de escritorio remoto con la máquina virtual. La primera vez que inicie sesión en un script se ejecutará en una ventana de PowerShell. No la cierre. Cuando haya terminado, se abre automáticamente la herramienta de configuración de agente de host para registrar el servidor.
   2. En **Configuración de agente de host** especifique la dirección IP interna del servidor de configuración y el puerto 443. Puede utilizar la dirección interna y el puerto privado 443, incluso si no se conecta en VPN porque la máquina virtual está conectada a la misma red de Azure que el servidor de configuración. Deje **Usar HTTPS** habilitado. Escriba la frase de contraseña del servidor de configuración que anotó anteriormente. Haga clic en **Aceptar** para registrar el servidor. Puede omitir las opciones de NAT. No se usan.
   3. Si su requisito de unidad de retención estimada es superior a 1 TB, puede configurar el volumen de retención (R:) mediante un disco virtual y [espacios de almacenamiento](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)

   ![Servidor de destino principal de Windows](./media/site-recovery-vmware-to-azure-classic-legacy/target-register.png)
6. Si ejecuta Linux:

   1. Asegúrese de que ha instalado los servicios de integración de Linux (LIS) más recientes antes de instalar el software de servidor de destino maestro. Encontrará la versión más reciente de LIS junto con instrucciones sobre cómo instalar [aquí](https://www.microsoft.com/download/details.aspx?id=46842). Reinicie el equipo después de instalar LIS.
   2. En **Preparar los recursos de destino (Azure)**, haga clic en **Descargar e instalar software adicional (solo para el servidor de destino Linux Master)**. Copie el archivo tar descargado en la máquina virtual mediante un cliente sftp. También puede iniciar sesión en el servidor de destino maestro de Linux implementado y usar *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* para descargar el archivo.
   3. Inicie sesión en el servidor mediante un cliente de Secure Shell. Si está conectado a la red de Azure a través de VPN, utilice la dirección IP interna. En caso contrario, utilice la dirección IP externa y el extremo público SSH.
   4. Extraiga los archivos del instalador comprimidos con gzip ejecutando: **tar –xvzf Microsoft-ASR_UA_8.4.0.0_RHEL6-64***
      ![servidor de destino maestro de Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-tar.png)
   5. Asegúrese de que se encuentra en el directorio en el que extrajo el contenido del archivo tar.
   6. Copie la frase de contraseña del servidor de configuración en un archivo local con el comando **echo *`<passphrase>`* >passphrase.txt**.
   7. Ejecute el comando "**sudo ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i *`<Configuration server internal IP address>`* -p 443 -s y -c https -P passphrase.txt**".

      ![Registrar servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/linux-mt-install.png)
7. Espere unos minutos (de 10 a 15) y en la página compruebe que el servidor de destino maestro se muestra como registrado en la pestaña **Servidores** > **Servidores de configuración****Detalles del servidor**. Si ejecuta Linux y no se ha registrado, vuelva a ejecutar la herramienta de configuración de host en /usr/local/ASR/Vx/bin/hostconfigcli. Deberá establecer los permisos de acceso mediante la ejecución de chmod como raíz.

    ![Verificar servidor de destino](./media/site-recovery-vmware-to-azure-classic-legacy/target-server-list.png)

> [!NOTE]
> Pueden pasar hasta 15 minutos después de completar el registro hasta que el servidor de destino maestro se muestre en el portal. Para que se actualice inmediatamente, haga clic en **Actualizar** on the **Servidores de configuración** .
>
>

## <a name="step-4-deploy-the-on-premises-process-server"></a>Paso 4: Implementación de un servidor de proceso local
Antes de empezar se recomienda que configure una dirección IP estática en el servidor de procesos para garantizar la persistencia en los reinicios.

1. Haga clic en Inicio rápido > **Instalar el servidor de proceso local** > **Descargar e instalar el servidor de proceso**.

    ![Instalar servidor de procesos](./media/site-recovery-vmware-to-azure-classic-legacy/ps-deploy.png)
2. Copie el archivo comprimido descargado en el servidor en el que va a instalar al servidor de proceso. El archivo comprimido contiene dos archivos de instalación:

   * Microsoft-ASR_CX_TP_8.4.0.0_Windows*
   * Microsoft-ASR_CX_8.4.0.0_Windows*
3. Descomprima el archivo y copie los archivos de instalación en una ubicación en el servidor.
4. Ejecute el archivo de instalación **Microsoft-ASR_CX_TP_8.4.0.0_Windows*** y siga las instrucciones. Se instalan los componentes de terceros necesarios para la implementación.
5. A continuación, ejecute **Microsoft-ASR_CX_8.4.0.0_Windows***.
6. En la página **Modo servidor**, seleccione **Servidor de proceso**.
7. En la página **Detalles del entorno** , haga lo siguiente:

    - Si desea proteger las máquinas virtuales de VMware, haga clic en **Sí**
    - Si solo desea proteger servidores físicos y, por lo tanto, no necesita vCLI VMware instalado en el servidor de procesos. Haga clic en **No** y continúe.

1. Cuando se instala VMware vCLI, tenga en cuenta lo siguiente:

   * **Se admite solo VMware vSphere CLI 5.5.0**. El servidor de proceso no funciona con otras versiones o actualizaciones de vSphere CLI.
   * Descargue vSphere CLI 5.5.0 desde [aquí.](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
   * Si instaló vSphere CLI justo antes de iniciar la instalación del servidor de proceso y el programa de instalación no lo encuentra, espere hasta cinco minutos antes de volver a intentar la instalación. Esto garantiza que todas las variables de entorno necesarias para la detección de vSphere CLI se han inicializado correctamente.
2. En **Selección de NIC para el servidor de proceso** , seleccione el adaptador de red que debe usar el servidor de proceso.

   ![Seleccionar adaptador](./media/site-recovery-vmware-to-azure-classic-legacy/ps-nic.png)
3. En **Detalles del servidor de configuración**:

   * Para la dirección IP y el puerto, si se conecta a través de VPN, especifique la dirección IP interna del servidor de configuración y 443 para el puerto. De lo contrario, especifique la dirección IP virtual pública y el extremo HTTP público asignado.
   * Escriba la frase de contraseña del servidor de configuración.
   * Desactive **Comprobar firma de software del servicio de movilidad** si desea deshabilitar la comprobación cuando utiliza la inserción automática para instalar el servicio. La comprobación de la firma necesita conectividad a Internet en el servidor de proceso.
   * Haga clic en **Next**.

   ![Registrar servidor de configuración](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cs.png)
4. En **Seleccionar unidad de instalación** , seleccione una unidad de caché. El servidor de proceso necesita una unidad de memoria caché con al menos 600 GB de espacio libre. Luego haga clic en **Instalar**.

   ![Registrar servidor de configuración](./media/site-recovery-vmware-to-azure-classic-legacy/ps-cache.png)
5. Tenga en cuenta que debe reiniciar el equipo para completar la instalación. En **Configuration Server** > **Detalles del servidor** , compruebe que el servidor de proceso aparece y que está correctamente registrado en el almacén.

> [!NOTE]
> Pueden pasar 15 minutos después de completar el registro para que el servidor de proceso se muestre en el servidor de configuración. Para actualizar inmediatamente, actualice al servidor de configuración haciendo clic en el botón Actualizar en la parte inferior de la página del servidor de configuración.
>
>

![Validar servidor de procesos](./media/site-recovery-vmware-to-azure-classic-legacy/ps-register.png)

Si no deshabilita la comprobación de firmas para Mobility Service al registrar el servidor de proceso, puede hacerlo más adelante de la forma siguiente:

1. Inicie sesión en el servidor de proceso como administrador y abra el archivo C:\pushinstallsvc\pushinstaller.conf para modificarlo. En la sección **[PushInstaller.transport]** agregue esta línea: **SignatureVerificationChecks=”0”**. Guarde y cierre el archivo.
2. Reinicie el servicio InMage PushInstall.

## <a name="step-5-update-site-recovery-components"></a>Paso 5: Actualización de los componentes de Site Recovery
Los componentes de Site Recovery se actualizan periódicamente. Cuando haya disponibles nuevas actualizaciones se deben instalar en el orden siguiente:

1. Servidor de configuración
2. Servidor de proceso
3. Servidor de destino principal
4. Herramienta de conmutación por recuperación (vContinuum)

### <a name="obtain-and-install-the-updates"></a>Obtención e instalación de las actualizaciones
1. Puede obtener actualizaciones para los servidores de configuración, procesos y destino maestro en el **panel**de Site Recovery. Para la instalación de Linux, extraiga los archivos del instalador comprimidos con gzip y ejecute el comando “sudo ./install” para instalar la actualización.
2. [Descargue](http://go.microsoft.com/fwlink/?LinkID=533813) la actualización más reciente para la herramienta de conmutación por recuperación (vContinuum).
3. Si está ejecutando las máquinas virtuales o los servidores físicos que ya tienen instalado Mobility Service, puede obtener actualizaciones para el servicio como sigue:

   * **Opción 1**: Descarga de actualizaciones
     * [Windows Server 64 (solo 64 bits)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
     * [CentOS 6.4, 6.5, 6.6 (solo 64 bits)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
     * [Oracle Enterprise Linux 6.4, 6.5 (solo 64 bits)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
     * [SUSE Linux Enterprise Server SP3 (solo 64 bits)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
     * Después de actualizar el servidor de procesos, puede obtener la versión actualizada de Mobility Service en la carpeta C:\pushinstallsvc\repository en el servidor de procesos.
   * **Opción 2**: Si tiene una máquina con una versión anterior de Mobility Service instalada, también puede actualizarlo automáticamente en la máquina desde el portal de administración.

     1. Asegúrese de que el servidor de procesos se actualiza.
     2. Asegúrese de que la máquina protegida cumple con los [requisitos previos](#install-the-mobility-service-automatically) para insertar automáticamente Mobility Service con el fin de que la actualización funcione según lo previsto.
     3. Seleccione el grupo de protección, resalte la máquina protegida y haga clic en **Actualizar Servicio de movilidad**. Este botón solo está disponible si hay una versión más reciente de Mobility Service.

         ![Seleccionar servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/update-mobility.png)

En Seleccione cuentas especifique la cuenta de administrador que se usará para actualizar el servicio de movilidad en el servidor protegido. Haga clic en Aceptar y espere a que se complete el trabajo desencadenado.

## <a name="step-6-add-vcenter-servers-or-vsphere-hosts"></a>Paso 6: Incorporación de los servidores vCenter o los hosts vSphere
1. Haga clic en **Servidores** > **Servidores de configuración** > Servidor de configuración > **Agregar servidor vCenter** para agregar un servidor vCenter o un host vSphere.

    ![Seleccionar servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter.png)
2. Especifique los detalles para el servidor o host y seleccione el servidor de procesos que se usará para detectarlo.

   * Si el servidor vCenter no se está ejecutando en el puerto 443 predeterminado, especifique el número de puerto en el que se ejecuta el servidor vCenter.
   * El servidor de procesos tiene que estar en la misma red que el servidor vCenter o el host vSphere y debe tener instalado VMware vSphere CLI 5.5.0.

     ![Configuración de servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter4.png)
3. Una vez terminada la detección del servidor de vCenter, se mostrará en los detalles de configuración del servidor.

    ![Configuración de servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/add-vcenter2.png)
4. Si utiliza una cuenta sin privilegios de administrador para agregar el servidor o el host, asegúrese de que la cuenta tenga los privilegios siguientes:

   * Las cuentas de vCenter deben tener habilitados el centro de datos, el almacén de datos, la carpeta, el host, la red, recursos, vistas de almacenamiento, la máquina virtual y privilegios del conmutador distribuido de vSphere habilitados.
   * Las cuentas de host de vSphere deben tener habilitados el centro de datos, el almacén de datos, la carpeta, el host, la red, recursos, la máquina virtual y los privilegios del conmutador distribuido de vSphere.

## <a name="step-7-create-a-protection-group"></a>Paso 7: Crear un grupo de protección
1. Abra **Elementos protegidos** > **Grupo de protección** > **Crear grupo de protección**.

    ![Crear un grupo de protección](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg1.png)
2. En la página **Especificar la configuración del grupo de protección** , especifique un nombre para el grupo y seleccione el servidor de configuración en el que desea crear el grupo.

    ![Configuración del grupo de protección](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg2.png)
3. En la página **Especificar valores de replicación** , configure las opciones de replicación que se usarán en todos los equipos del grupo.

    ![Replicación del grupo de protección](./media/site-recovery-vmware-to-azure-classic-legacy/create-pg3.png)
4. Configuración:

   * **Coherencia de múltiples máquinas virtuales**: si lo activa, se crean puntos de recuperación coherentes con las aplicaciones compartidas en los equipos del grupo de protección. Esta configuración es muy importante cuando todos los equipos del grupo de protección ejecutan la misma carga de trabajo. Se recuperarán todos los equipos en el mismo punto de datos. Solo disponible para servidores Windows Server.
   * **Umbral de RPO**: se generan alertas cuando el RPO de replicación de protección de datos continua supera el valor de umbral RPO configurado.
   * **Retención de punto de recuperación**: especifica el período de retención. Los equipos protegidos se pueden recuperar en cualquier punto dentro de este período.
   * **Frecuencia de la instantánea de coherencia de la aplicación**: especifica la frecuencia con la que se crearán los puntos de recuperación que contengan las instantáneas coherentes con la aplicación.

Puede supervisar el grupo de protección, pues se crean en la página **Elementos protegidos** .

## <a name="step-8-set-up-machines-you-want-to-protect"></a>Paso 8: Configurar equipos que desea proteger
Necesitará instalar Mobility Service en máquinas virtuales y servidores físicos que desea proteger. Puede hacerlo de dos maneras:

* Insertar e instalar automáticamente el servicio en cada máquina desde el servidor de proceso.
* Instalar el servicio manualmente.

### <a name="install-the-mobility-service-automatically"></a>Instalación automática de Mobility Service
Al agregar equipos a un grupo de protección, el servicio de movilidad se inserta automáticamente y el servidor de proceso lo instala en cada equipo.

**Instalación de inserción automática de Mobility Service en servidores Windows:**

1. Instale las actualizaciones más recientes para el servidor de procesos, como se describe en [Paso 5: Instalar actualizaciones más recientes](#step-5-install-latest-updates), y asegúrese de que el servidor de procesos está disponible.
2. Asegúrese de que no hay conectividad de red entre el equipo de origen y el servidor de procesos, y que se puede tener acceso a la máquina de origen desde el servidor de procesos.  
3. Configure el firewall de Windows para permitir **Compartir archivos e impresoras** y el **Instrumental de administración de Windows**. En Configuración de Firewall de Windows, seleccione la opción "Permitir una aplicación o una característica a través del firewall" y seleccione las aplicaciones tal como se muestra en la figura siguiente. Para los equipos que pertenecen a un dominio puede configurar la directiva de firewall con un GPO.

    ![Configuración de firewall](./media/site-recovery-vmware-to-azure-classic-legacy/push-firewall.png)
4. La cuenta utilizada para realizar la instalación de inserción debe encontrarse en el grupo de administradores en el equipo que desea proteger. Estas credenciales se utilizan únicamente para la instalación de inserción de Mobility Service y podrá proporcionarlas al agregar un equipo a un grupo de protección.
5. Si la cuenta de administrador no es una cuenta de dominio, deberá deshabilitar el control de acceso de usuarios remotos en el equipo local. Para ello, agregue la entrada del Registro LocalAccountTokenFilterPolicy DWORD con un valor de 1 en HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System. Para agregar la entrada del Registro de un CLI, abra cmd o powershell y escriba **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

**Instalación de inserción automática de Mobility Service en servidores Linux:**

1. Instale las actualizaciones más recientes para el servidor de procesos, como se describe en [Paso 5: Instalar actualizaciones más recientes](#step-5-install-latest-updates), y asegúrese de que el servidor de procesos está disponible.
2. Asegúrese de que no hay conectividad de red entre el equipo de origen y el servidor de procesos, y que se puede tener acceso a la máquina de origen desde el servidor de procesos.  
3. Asegúrese de que la cuenta es un usuario raíz en el servidor Linux de origen.
4. Asegúrese de que el archivo /etc/hosts del servidor Linux de origen contiene entradas que asignan el nombre de host local a las direcciones IP asociadas con todas las tarjetas NIC.
5. Instale los paquetes openssh, openssh-server, openssl más recientes en el equipo que desea proteger.
6. Asegúrese de que SSH está habilitado y ejecutándose en el puerto 22.
7. Habilite la autenticación de la contraseña y del subsistema SFTP en el archivo sshd_config:

   * a) Inicie sesión como root.
   * b) En el archivo /etc/ssh/sshd_config, busque la línea que comienza con **PasswordAuthentication**.
   * c) Quite el comentario de la línea y cambie el valor de “no” a “yes”.

       ![Movilidad de Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push.png)
   * d) Busque la línea que comienza con Subsistema y quite el comentario.

       ![Movilidad de inserción de Linux](./media/site-recovery-vmware-to-azure-classic-legacy/linux-push2.png)    
8. Asegúrese de que se admite la variante de Linux del equipo de origen.

### <a name="install-the-mobility-service-manually"></a>Instalación manual de Mobility Service
Los paquetes de software que se usan para instalar Mobility Service están en el servidor de procesos en C:\pushinstallsvc\repository. Inicie sesión en el servidor de procesos y copie el paquete de instalación correspondiente a la máquina de origen basándose en la tabla siguiente:-

| Sistema operativo de origen | Paquete de Mobility Service en el servidor de procesos |
| --- | --- |
| Windows Server 64 (solo 64 bits) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe` |
| CentOS 6.4, 6.5, 6.6 (solo 64 bits) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz` |
| SUSE Linux Enterprise Server 11 SP3 (solo 64 bits) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz` |
| Oracle Enterprise Linux 6.4, 6.5 (solo 64 bits) |`C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz` |

**Para instalar Mobility Service manualmente en un servidor Windows**, haga lo siguiente:

1. Copie el paquete **Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe** de la ruta de acceso al directorio del servidor de procesos indicada en la tabla anterior al equipo de origen.
2. Instale Mobility Service mediante la ejecución del archivo ejecutable en el equipo de origen.
3. Siga las instrucciones del instalador.
4. Seleccione **Mobility Service** como el rol y haga clic en **Siguiente**.

    ![Instalar Mobility Service](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install.png)
5. Deje el directorio de instalación como la ruta de instalación predeterminada y haga clic en **Instalar**.
6. En **Configuración de agente de host** , especifique la dirección IP y el puerto HTTPS del servidor de configuración.

   * Si se conecta a través de Internet, especifique la dirección IP virtual pública y el extremo HTTPS público como el puerto.
   * Si se conecta a través de VPN, especifique la dirección IP interna y 443 para el puerto. Deje la opción **Usar HTTPS** marcada.

     ![Instalar Mobility Service](./media/site-recovery-vmware-to-azure-classic-legacy/ms-install2.png)
7. Especifique la frase de contraseña del servidor de configuración y haga clic en **Aceptar** para registrar Mobility Service con el servidor de configuración.

**Para ejecutar desde la línea de comandos:**

1. Copie la frase de contraseña desde el CX en el archivo "C:\connection.passphrase" en el servidor y ejecute este comando. En nuestro ejemplo CX i 104.40.75.37 y el puerto HTTPS es 62519:

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Instalación de Mobility Service manualmente en un servidor Linux**:

1. Copie el archivo tar adecuado basado en la tabla anterior, desde el servidor de proceso a la máquina de origen.
2. Abra un programa de shell y extraiga el archivo tar comprimido en una ruta de acceso local mediante la ejecución de `tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. Cree un archivo passphrase.txt en el directorio local en que extrajo el contenido del archivo tar; para ello, escriba *`echo <passphrase> >passphrase.txt`* desde shell.
4. Instale Mobility Service; para ello, escriba *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Especifique la dirección IP y el puerto:

   * Si se conecta al servidor de configuración a través de Internet, especifique la dirección IP pública virtual del servidor de configuración y el extremo HTTPS público en `<IP address>` y `<port>`.
   * Si se conecta a través de VPN, especifique la dirección IP interna y 443.

**Para ejecutar desde la línea de comandos**:

1. Copie la frase de contraseña desde el CX en el archivo "passphrase.txt" en el servidor y ejecute este comando. En nuestro ejemplo CX i 104.40.75.37 y el puerto HTTPS es 62519:

Para instalar en un servidor de producción:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

Para instalar en el servidor de destino:

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

> [!NOTE]
> Al agregar equipos a un grupo de protección que ya están ejecutando una versión adecuada de Mobility Service, la instalación de inserción se omite.
>
>

## <a name="step-9-enable-protection"></a>Paso 9: Habilitar protección
Para habilitar la protección, agregue máquinas virtuales y servidores físicos a un grupo de protección. Antes de comenzar, tenga en cuenta que:

* Las máquinas virtuales se detectan cada 15 minutos y pueden tardar hasta 15 minutos en aparecer en Azure Site Recovery después de la detección.
* Los cambios de entorno en la máquina virtual (como la instalación de herramientas de VMware) también pueden tardar hasta 15 minutos en actualizarse en Site Recovery.
* Puede consultar la última detección en el campo **ÚLTIMO CONTACTO A LAS** para el servidor vCenter/host ESXi en la página **Servidores de configuración**.
* Si tiene un grupo de protección ya creado y agrega un servidor vCenter o un host ESXi después de eso, se tarda 15 minutos en actualizar el portal de Azure Site Recovery y en mostrar las máquinas virtuales en el cuadro de diálogo **Agregar máquinas al grupo de protección** .
* Si desea continuar inmediatamente con la incorporación de equipos al grupo de protección, sin esperar la detección programada, resalte el servidor de configuración (no haga clic en él) y haga clic en el botón **Actualizar** .
* Al agregar máquinas virtuales o equipos físicos a un grupo de protección, el servidor de procesos inserta automáticamente e instala Mobility Service en el servidor de origen si aún no está instalado.
* Asegúrese de que ha configurado los equipos protegidos tal como se ha descrito en el paso anterior para que el mecanismo de inserción automática funcione.

Agregue las máquinas como sigue:

1. Haga clic en **Elementos protegidos** > **Grupos de protección** > **Máquinas** > **Agregar máquinas**. Como procedimiento recomendado, se recomienda que los grupos de protección deben reflejar las cargas de trabajo para que puedan agregar los equipos que ejecutan una aplicación específica al mismo grupo.
2. En **Seleccionar máquinas virtuales**, si protege servidores físicos, en el Asistente para **agregar máquinas físicas**, proporcione la dirección IP y el nombre descriptivo. A continuación, seleccione la familia de sistemas operativos.

    ![Agregar servidor V-Center](./media/site-recovery-vmware-to-azure-classic-legacy/physical-protect.png)
3. En **Seleccionar máquinas virtuales** , si protege máquinas virtuales de VMware, seleccione un servidor vCenter que administre las máquinas virtuales (o el host EXSi en el que se ejecutan) y después seleccione las máquinas.

    ![Agregar servidor V-Center](./media/site-recovery-vmware-to-azure-classic-legacy/select-vms.png)    
4. En **Especificar recursos de destino** , seleccione los servidores de destino maestros y el almacenamiento que se va a usar para la replicación y seleccione si se debe usar la configuración para todas las cargas de trabajo. Seleccione [cuenta de almacenamiento Premium](../storage/storage-premium-storage.md) al configurar la protección para las cargas de trabajo que requieren un alto rendimiento de E/S y baja latencia uniformes para hospedar cargas de trabajo intensivas de E/S. Si quiere usar una cuenta de almacenamiento premium para los discos de cargas de trabajo, necesitará utilizar el destino principal de la serie DS. No puede usar discos de almacenamiento premium con máquinas virtuales que no sean de serie DS.

   > [!NOTE]
   > No se admite el traslado de cuentas de almacenamiento creadas con el [nuevo Portal de Azure](../storage/storage-create-storage-account.md) entre grupos de recursos.
   >
   >

    ![Servidor vCenter](./media/site-recovery-vmware-to-azure-classic-legacy/machine-resources.png)
5. En **Especificar cuentas** , seleccione la cuenta que desea usar para instalar Mobility Service en equipos protegidos. Las credenciales de cuenta son necesarias para la instalación automática de Mobility Service. Si no puede seleccionar una cuenta, asegúrese configurar una tal y como se describe en el paso 2. Tenga en cuenta que Azure no puede tener acceso a esta cuenta. En Windows Server, la cuenta debe tener privilegios de administrador en el servidor de origen. Para Linux, la cuenta debe ser raíz.

    ![Credenciales de Linux](./media/site-recovery-vmware-to-azure-classic-legacy/mobility-account.png)
6. Haga clic en la marca de verificación para terminar de agregar equipos al grupo de protección y para iniciar la replicación inicial en cada equipo. Puede supervisar el estado en la página **Trabajos** .

    ![Agregar servidor V-Center](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs2.png)
7. Además, puede supervisar el estado de protección; para ello, haga clic en **Elementos protegidos** > Nombre del grupo de protección > **Máquinas virtuales**. Cuando se completa la replicación inicial y las máquinas están sincronizando datos, mostrarán el estado **Protegido** .

    ![Trabajos de máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/pg-jobs.png)

### <a name="set-protected-machine-properties"></a>Establecimiento de propiedades del equipo protegido
1. Cuando los equipos ya tienen el estado **Protegido** , puede configurar sus propiedades de conmutación por error. En los detalles del grupo de protección, seleccione el equipo y abra la pestaña **Configurar** .
2. Puede modificar el nombre que se asignará al equipo de Azure después de la conmutación por error así como el tamaño de la máquina virtual de Azure. También puede seleccionar la red de Azure a la que se conectará el equipo después de la conmutación por error.

   > [!NOTE]
   > La [Migración de redes](../resource-group-move-resources.md) entre grupos de recursos dentro de la misma suscripción o entre suscripciones no se admite en el caso de redes usadas para implementar Site Recovery.
   >
   >

    ![Establecer propiedades de máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/vm-props.png)

Observe lo siguiente:

* El nombre del equipo de Azure debe cumplir los requisitos de Azure.
* De forma predeterminada, las máquinas virtuales replicadas en Azure no están conectadas a una red de Azure. Si desea que se comuniquen las máquinas virtuales, asegúrese de establecer la misma red de Azure para ellas.
* Si cambia el tamaño de un volumen en un servidor físico o máquina virtual de VMware, entrará en estado crítico. Si necesita modificar el tamaño, haga lo siguiente:

  * a) Cambie la configuración del tamaño.
  * b) En la pestaña **Máquinas virtuales**, seleccione la máquina virtual y haga clic en **Quitar**.
  * c) En **Quitar máquina virtual**, seleccione la opción **Deshabilitar protección (úselo para la obtención de detalles y modificación del tamaño del volumen)**. Esta opción deshabilita la protección, pero conserva los puntos de recuperación en Azure.

      ![Establecer propiedades de máquina virtual](./media/site-recovery-vmware-to-azure-classic-legacy/remove-vm.png)
  * d) Vuelva a habilitar la protección de una máquina virtual. Cuando se vuelve a habilitar la protección, se transferirán los datos para el volumen cuyo tamaño ha cambiado a Azure.

## <a name="step-10-run-a-failover"></a>Paso 10: Ejecutar la conmutación por error
Actualmente solo se pueden ejecutar conmutaciones por error no previstas de servidores físicos y máquinas virtuales de VMware protegidos. Tenga en cuenta lo siguiente:

* Antes de iniciar una conmutación por error, asegúrese de que los servidores de configuración y de destino principal funcionan y están en buen estado. De lo contrario, se producirá un error de conmutación por error.
* Las máquinas de origen no se apagan como parte de una conmutación por error no planeada. Realizar una conmutación por error no planeada detiene la replicación de datos para los servidores protegidos. Deberá eliminar las máquinas del grupo de protección y agregarlas de nuevo para comenzar a proteger los equipos de nuevo una vez completada la conmutación por error no planeada.
* Si desea conmutar por error sin perder datos, asegúrese de que están desactivadas las máquinas virtuales del sitio primario antes de iniciar la conmutación por error.

1. En la página **Planes de recuperación** , agregue un plan de recuperación. Especifique los detalles del plan y seleccione **Azure** como destino.

    ![Configurar plan de recuperación](./media/site-recovery-vmware-to-azure-classic-legacy/rplan1.png)
2. En **Seleccionar máquina virtual** seleccione un grupo de protección y después seleccione los equipos del grupo que se van a agregar al plan de recuperación. [Obtenga más información](site-recovery-create-recovery-plans.md) sobre los planes de recuperación.

    ![Agregar máquinas virtuales](./media/site-recovery-vmware-to-azure-classic-legacy/rplan2.png)
3. Si es necesario, puede personalizar el plan para crear grupos y organizar el orden en el los equipos del plan de recuperación se van a conmutar por error. También puede agregar avisos para las acciones manuales y los scripts. Los scripts para recuperar en Azure pueden agregarse mediante [Runbooks de automatización de Azure](site-recovery-runbook-automation.md).
4. En la página **Planes de recuperación**, seleccione el plan y haga clic en **Conmutación por error no planeada**.
5. En **Confirmar conmutación por error** , compruebe la dirección de la conmutación por error (en Azure) y seleccione el punto de recuperación en el que se va a realizar la conmutación.
6. Espere a que el trabajo de conmutación por error se complete y, a continuación, compruebe que la conmutación por error funciona de la forma prevista y que las máquinas virtuales replicadas se inician correctamente en Azure.

## <a name="step-11-fail-back-failed-over-machines-from-azure"></a>Paso 11: Conmutación por recuperación a través de máquinas de Azure
[Obtenga más información](site-recovery-failback-azure-to-vmware-classic-legacy.md) sobre cómo devolver las máquinas de conmutación por recuperación que se ejecutan en Azure al entorno local.

## <a name="manage-your-process-servers"></a>Administración de servidores de procesos
El servidor de proceso envía los datos de replicación al servidor de destino principal en Azure y detecta nuevas máquinas virtuales de VMware agregadas a un servidor vCenter. En las siguientes circunstancias, puede cambiar el servidor de proceso en su implementación:

* Si el servidor de proceso actual deja de funcionar
* Si su objetivo de punto de recuperación (RPO) aumenta hasta un nivel inaceptable para su organización.

Si es necesario puede mover la replicación de algunas o todas las máquinas virtuales de VMware locales y los servidores físicos a un servidor de proceso diferente. Por ejemplo:

* **Error**: si en un servidor de procesos se produce un error o no está disponible, puede mover la replicación del equipo protegido a otro servidor de procesos. Los metadatos de la máquina de origen y la máquina de réplica se moverán al nuevo servidor de proceso y los datos se volverán a sincronizar. El nuevo servidor de proceso se conectará automáticamente al servidor vCenter para realizar la detección automática. Puede supervisar el estado de los servidores de procesos en el panel de Site Recovery.
* **Equilibrio de carga para ajustar el RPO**: para conseguir un equilibrio de carga mejorado, puede seleccionar un servidor de procesos diferente en el portal de Site Recovery y mover la replicación de uno o más equipos a este para equilibrar la carga manualmente. En este caso los metadatos de los equipos de origen y réplica seleccionados se mueven al nuevo servidor de procesos. El servidor de procesos original permanece conectado al servidor vCenter.

### <a name="monitor-the-process-server"></a>Supervisión del servidor de procesos
Si un servidor de procesos está en un estado crítico, se mostrará una advertencia de estado en el panel de Site Recovery. Puede hacer clic en el estado para abrir la pestaña Eventos y después desglosar trabajos específicos en la pestaña Trabajos.

### <a name="modify-the-process-server-used-for-replication"></a>Modificación del servidor de procesos utilizado para la replicación
1. Abra **Servidores** > **Servidores de configuración** > Servidor de configuración > **Detalles del servidor**.
2. Haga clic en **Servidores de procesos** > **Cambiar servidor de procesos** junto al servidor que quiere modificar.

    ![Cambiar servidor de proceso 1](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps1.png)
3. En **Cambiar servidor de procesos** > **Servidor de procesos de destino** , seleccione el nuevo servidor que quiere usar y luego seleccione las máquinas virtuales que quiere replicar en el nuevo servidor. Haga clic en el icono de información junto al nombre del servidor para más información sobre espacio libre y memoria utilizada. El espacio medio que será necesario para replicar cada máquina virtual seleccionada en el nuevo servidor de procesos se muestra para ayudarle a tomar decisiones de carga.

    ![Cambiar servidor de proceso 2](./media/site-recovery-vmware-to-azure-classic-legacy/change-ps2.png)
4. Haga clic en la marca de verificación para empezar a replicar en un nuevo servidor de procesos. Tenga en cuenta que si quita todas las máquinas virtuales de un servidor de procesos que era fundamental, ya no debe mostrarse una advertencia crítica en el panel.

## <a name="third-party-software-notices-and-information"></a>Avisos e información de software de terceros
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

