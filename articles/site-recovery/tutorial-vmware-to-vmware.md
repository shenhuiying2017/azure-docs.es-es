---
title: "Configuración de recuperación ante desastres de máquinas virtuales de VMware o de servidores físicos en un sitio secundario con Azure Site Recovery | Microsoft Docs"
description: "Obtenga información sobre cómo configurar la recuperación ante desastres de máquinas virtuales de VMware o servidores físicos Windows y Linux en un sitio secundario con Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauarvd
editor: 
ms.assetid: 68616d15-398c-4f40-8323-17b6ae1e65c0
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 9de0cb3d22a4e500b66640f70f936ae955bbadb8
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Configuración de la recuperación ante desastres de máquinas virtuales de VMware o de servidores físicos locales en un sitio secundario

InMage Scout en [Azure Site Recovery](site-recovery-overview.md) proporciona características de replicación en tiempo real entre los sitios locales de VMware. InMage Scout se incluye en las suscripciones al servicio Azure Site Recovery. 


## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

- [Revise](site-recovery-support-matrix-to-sec-site.md) los requisitos de compatibilidad de todos los componentes.
- Asegúrese de que las máquinas que desea replicar cumplen con la [compatibilidad con máquinas replicadas](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).


## <a name="create-a-vault"></a>Creación de un almacén

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="choose-a-protection-goal"></a>Elección de un objetivo de protección

Seleccione qué quiere replicar y en dónde hacerlo.

1. Haga clic en **Site Recovery** > **Preparar infraestructura** > **Objetivo de protección**.
2. Seleccione **Para sitio de recuperación** > **Sí, con VMware vSphere Hypervisor**. y, a continuación, haga clic en **Aceptar**.
3. En **Configuración de Scout**, descargue el software de disponibilidad general de InMage Scout 8.0.1 y la clave de registro. Los archivos de instalación de todos los componentes están incluidos en el archivo ZIP que se descargó.

## <a name="download-and-install-component-updates"></a>Descarga e instalación de actualizaciones de componentes

 Revise e instale las [actualizaciones](#updates) más recientes. Las actualizaciones se deben instalar en los servidores en este orden:

1. Servidor RX (si corresponde)
2. Servidores de configuración
3. Servidores de proceso
4. Servidores de destino maestros
5. Servidores de vContinuum
6. Servidor de origen (tanto servidor Windows como Linux)

Instale las actualizaciones de la siguiente manera:

> [!NOTE]
>La versión de actualización del archivo de todos los componentes de Scout no puede ser la misma que la del archivo .zip de la actualización. La versión anterior indica que no hay ningún cambio en el componente desde la actualización anterior a esta.

Descargue el archivo .zip de la [actualización](https://aka.ms/asr-scout-update6). El archivo contiene los siguientes componentes: 
  - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
  - CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
  - UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
  - UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  - vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
  - UA actualización 4 bits para RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
1. Extraiga los archivos .zip.
2. **Servidor RX**: copie **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** en el servidor RX y extráigalo. En la carpeta extraída, ejecute **/Install**.
3. **Servidor de configuración o servidor de procesos**: copie **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** en el servidor de configuración y el servidor de procesos. Haga doble clic para ejecutarlo.<br>
4. **Servidor de destino maestro de Windows**: para actualizar el agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** en el servidor. Haga doble clic en él para ejecutarlo. La misma actualización del agente unificado también se aplica al servidor de origen. Si el origen no se ha actualizado a Update 4, debe actualizar el agente unificado.
  La actualización no necesita aplicarse en el destino maestro preparado con **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** puesto que este es el nuevo instalador de disponibilidad general con todos los cambios más recientes.
5. **Servidor vContinuum**: copie **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** en el servidor.  Asegúrese de que ha cerrado el asistente de vContinuum. Haga doble clic en el archivo para ejecutarlo.
    La actualización no tiene que aplicarse en el destino maestro preparado con **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** puesto que este es el nuevo instalador de GA con todos los cambios más recientes.
6. **Servidor de destino maestro Linux**: para actualizar el agente unificado, copie **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** en el servidor de destino maestro y extráigalo. En la carpeta extraída, ejecute **/Install**.
7. **Servidor de origen de Windows**: para actualizar el agente unificado, copie **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** en el servidor. Haga doble clic en el archivo para ejecutarlo. 
    No es necesario instalar el agente de Update 5 en el servidor de origen si ya se ha actualizado a Update 4, o si se ha instalado el agente de origen con el instalador de base más reciente **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
8. **Servidor de origen Linux**: para actualizar el agente unificado, copie la versión correspondiente del archivo del agente unificado en el servidor Linux y extráigalo. En la carpeta extraída, ejecute **/Install**.  Ejemplo: para el servidor RHEL 6.7 de 64 bits, copie **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** en el servidor y extráigalo. En la carpeta extraída, ejecute **/Install**.

## <a name="enable-replication"></a>Habilitar replicación

1. Configure la replicación entre los sitios de origen y de destino de VMware.
2. Consulte los documentos siguientes para obtener más información sobre la instalación, la protección y la recuperación:

   * [Notas de la versión](https://aka.ms/asr-scout-release-notes)
   * [Matriz de compatibilidad](https://aka.ms/asr-scout-cm)
   * [Guía de usuario](https://aka.ms/asr-scout-user-guide)
   * [Guía de usuario de RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Guía de instalación rápida](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Actualizaciones

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 Update 6 
Actualización: 12 de octubre de 2017

Descargue la [actualización 6 de Scout](https://aka.ms/asr-scout-update6).

La actualización 6 de Scout es una actualización acumulativa. Contiene todas las correcciones desde Update 1 hasta Update 5, además de las correcciones nuevas y mejoras que se describen a continuación. 

#### <a name="new-platform-support"></a>Nueva compatibilidad con plataformas
* Se ha agregado compatibilidad con Windows Server 2016 de origen
* Se ha agregado compatibilidad con los siguientes sistemas operativos Linux:
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6.9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Se ha agregado compatibilidad con VMware Center 6.5.

> [!NOTE]
> * Se ha actualizado el instalador de base del agente unificado para Windows para admitir Windows Server 2016. El nuevo instalador **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** está empaquetado con el paquete de base de Scout GA (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). El mismo instalador se usará para todas las versiones compatibles de Windows. 
> * El instalador de destino maestro y vContinuum de Windows de base se ha actualizado para admitir Windows Server 2016. El nuevo instalador **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** está empaquetado con el paquete de base de disponibilidad general de Scout (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). El mismo instalador se utilizará para implementar el destino maestro de Windows 2016 y el destino maestro de Windows 2012 R2.
> * Descargue el paquete de GA del portal, como se describe en el artículo sobre la [creación de un almacén](#create-a-vault).
>

#### <a name="bug-fixes-and-enhancements"></a>Mejoras y correcciones de errores
- Se ha producido un error de protección de conmutación por recuperación para la máquina virtual Linux con la lista de discos que se van a replicar está vacía al final de la configuración.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 Update 5
La actualización 5 de Scout es una actualización acumulativa. Contiene todas las correcciones desde Update 1 hasta Update 4 y las correcciones nuevas que se describen a continuación.
- Las correcciones de Site Recovery Scout Update 4 a Update 5 son específicamente para los componentes de vContinuum y destino maestro.
- Si los servidores de origen, el destino maestro, la configuración, el proceso y los servidores RX ya ejecutan Update 4, aplíquela solo en el servidor de destino maestro. 

#### <a name="new-platform-support"></a>Nueva compatibilidad con plataformas
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)
* **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** de SLES 11 SP4 de 64 bits se incluye con el paquete base de Scout GA (**InMage_Scout_Standard_8.0.1 GA.zip**). Descargue el paquete de GA del portal, como se describe en el artículo sobre la [creación de un almacén](#create-a-vault).


#### <a name="bug-fixes-and-enhancements"></a>Mejoras y correcciones de errores

* Correcciones para aumentar la confiabilidad de la compatibilidad con clústeres de Windows:
    * Corregido: algunos de los discos de clúster de MSCS P2V se convierten en RAW después de la recuperación.
    * Corregido: se produce un error en la recuperación de un clúster de MSCS P2V debido a una falta de coincidencia en el orden del disco.
    * Corregido: error durante la operación de clúster de MSCS para agregar discos debido a una falta de coincidencia en el tamaño del disco.
    * Corregido: error en la comprobación de la preparación de la asignación del clúster de MSCS de origen con LUN de RDM en la comprobación del tamaño.
    * Corregido: error en la protección del clúster de nodo único debido a un problema de falta de coincidencia de SCSI. 
    * Corregido: error al volver a proteger el servidor de clúster de Windows P2V si hay discos del clúster de destino. 
    
* Corregido: durante la protección de conmutación por recuperación, si el servidor de destino maestro seleccionado no está en el mismo servidor ESXi que el de la máquina de origen protegida (durante la protección directa), vContinuum elige el servidor de destino maestro equivocado durante la recuperación de la conmutación por recuperación y, posteriormente, se produce un error en la operación de recuperación.

> [!NOTE]
> * Las correcciones de los clústeres P2V solo son aplicables a los clústeres de MSCS físicos que se acaban de proteger con Site Recovery Scout Update 5. Para instalar las correcciones de los clústeres en clústeres de MSCS P2V protegidos con actualizaciones anteriores, siga los pasos de actualización mencionados en la sección 12 de las [notas de la versión de Site Recovery Scout](https://aka.ms/asr-scout-release-notes).
> * Si en el momento de volver a protegerlos, el mismo conjunto de discos está activo en cada uno de los nodos de clúster que cuando los protegió inicialmente, por lo que la nueva protección de un clúster de MSCS físico solo puede reusar discos de destino existentes. En caso contrario, use los pasos manuales de la sección 12 de las [notas de la versión de Site Recovery Scout](https://aka.ms/asr-scout-release-notes) para mover los discos del lado de destino a la ruta de acceso correcta al almacén de datos, para volver a usarlos cuando se vuelva a realizar la protección. Si vuelve a proteger el clúster de MSCS en modo P2V sin seguir los pasos de actualización, crea un disco en el servidor ESXi de destino. Deberá eliminar manualmente los discos antiguos del almacén de datos.
> * Cuando un servidor SLES11 o SLES11 de origen (con cualquier Service Pack) se reinicia correctamente, debe marcar manualmente los pares de replicación de disco **raíz** para volver a sincronizarlos. No hay ninguna notificación en la interfaz de CX. Si no marca el disco raíz para resincronización, es posible que note problemas con la integridad de datos.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1, actualización 4
La actualización 4 de Scout es una actualización acumulativa. Incluye todas las correcciones desde Update 1 hasta Update 3 y las correcciones nuevas que se describen a continuación.

#### <a name="new-platform-support"></a>Nueva compatibilidad con plataformas

* Se ha agregado compatibilidad con vCenter/vSphere 6.0, 6.1 y 6.2.
* Se agregó compatibilidad con estos sistemas operativos Linux:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 y 7.2
  * CentOS 7.0, 7.1 y 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** de RHEL/CentOS 7 de 64 bits se incluye con el paquete base de Scout GA **InMage_Scout_Standard_8.0.1 GA.zip**. Descargue el paquete de Scout GA del portal, como se describe en el artículo sobre la [creación de un almacén](#create-a-vault).

#### <a name="bug-fixes-and-enhancements"></a>Mejoras y correcciones de errores

* Mejora en la administración del apagado para los siguientes clones y sistemas operativos Linux para evitar problemas no deseados de resincronización:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Para Linux, todos los permisos de acceso a carpetas del directorio de instalación del agente unificado ahora están restringidos exclusivamente al usuario local.
* En Windows, una corrección para un problema de agotamiento del tiempo de espera al emitir marcadores comunes de coherencia distribuida en aplicaciones distribuidas de alta carga, como los clústeres de SQL Server y SharePoint.
* Una corrección relacionada con el registro en el instalador de la base del servidor de configuración.
* Se agregó un vínculo de descarga a VMware vCLI 6.0 al instalador de la base de destino maestro de Windows.
* Se agregaron registros y comprobaciones adicionales para los cambios en la configuración de red durante maniobras de conmutación por error y recuperación ante desastres.
* Corrección de un problema que ocasionaba que la información de retención no se informaba al servidor de configuración.  
* En el caso de los clústeres físicos, la corrección de un problema que provocaba un error del redimensionamiento del volumen en el asistente de vContinuum al reducir el volumen de origen.
* Corrección de un problema de protección de clúster con el error: "Failed to find the disk signature" (No se pudo encontrar la firma de disco), cuando el disco del clúster es un disco de PRDM.
* Corrección de un bloqueo del servidor de transporte cxps provocado por una excepción fuera de intervalo.
* Ahora se puede modificar el tamaño de las columnas Nombre del servidor y Dirección IP en la página **Instalación de inserción** del asistente de vContinuum.
* Mejoras de la API de RX:
  * Ahora están disponibles los cinco puntos de coherencia comunes más recientes (solo las etiquetas garantizadas).
  * Se muestran los detalles de espacio libre y capacidad para todos los dispositivos protegidos.
  * El estado del controlador de Scout en el servidor de origen está disponible.

> [!NOTE]
> * El paquete base **InMage_Scout_Standard_8.0.1_GA.zip** incluye:
    * Un instalador de base del servidor de configuración actualizado (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
    * Un instalador de base de destino maestro de Windows (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
    * Para todas las instalaciones nuevas, use el servidor de configuración nuevo y los bits de GA del destino maestro de Windows.
> * Update 4 se puede aplicar directamente en 8.0.1 GA.
> * Las actualizaciones del servidor de configuración y RX no se pueden revertir una vez aplicadas.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 actualización 3

Todas las actualizaciones de Site Recovery son acumulativas. Update 3 contiene todas las correcciones de Update 1 y Update 2. La actualización 3 se puede aplicar directamente en 8.0.1 GA. Las actualizaciones del servidor de configuración y RX no se pueden revertir una vez aplicadas.

#### <a name="bug-fixes-and-enhancements"></a>Mejoras y correcciones de errores
Update 3 corrige los problemas siguientes:

* El servidor de configuración y RX no se registran en el almacén cuando están detrás del proxy.
* El número de horas en que no se alcanzaba el objetivo de punto de recuperación (RPO) no se actualiza en el informe de mantenimiento.
* El servidor de configuración no se sincroniza con RX cuando los detalles del hardware ESX o de la red contienen cualquier carácter UTF-8.
* Los controladores de dominio de Windows Server 2008 R2 no se inician después de la recuperación.
* La sincronización sin conexión no funciona según lo previsto.
* Después de la conmutación por error de la máquina virtual, la eliminación depares de replicación no avanza durante largo tiempo en la consola del servidor de configuración. Los usuarios no pueden completar las operaciones de conmutación por recuperación o reanudación.
* Las operaciones de instantáneas globales que realiza el trabajo de coherencia se han optimizado para ayudar a reducir las desconexiones de la aplicación como clientes de SQL Server.
* Se mejoró el rendimiento de la herramienta de coherencia (VACP.exe). Se redujo el uso de memoria requerido para crear instantáneas en Windows.
* El servicio de instalación de inserción se bloquea cuando la contraseña tiene más de 16 caracteres.
* vContinuum no comprueba las credenciales de vCenter ni solicita nuevas cuando estas se modifican.
* En Linux, el administrador de caché (cachemgr) de destino maestro no descarga los archivos del servidor de proceso. Esto genera una limitación de pares de replicación.
* Cuando el orden del disco físico del clúster de conmutación por error (MSCS) no es el mismo en todos los nodos, la replicación no se establece para algunos de los volúmenes del clúster. Tenga en cuenta que el clúster debe protegerse de nuevo para aprovechar esta corrección.  
* La funcionalidad de SMTP no funciona como se esperaba después de actualizar RX de Scout 7.1 a Scout 8.0.1.
* Se han agregado más estadísticas en el registro de la operación de reversión para medir el tiempo que ha tardado en completarse.
* Se ha agregado compatibilidad para los sistemas operativos Linux en el servidor de origen:
  * Red Hat Enterprise Linux (RHEL) 6 actualización 7
  * CentOS 6 actualización 7
* Las consolas RX y del servidor de configuración ahora muestran notificaciones para el par, el que va en modo de mapa de bits.
* Las siguientes revisiones de seguridad se han agregado a RX:
    * Omisión de la autorización mediante la alteración de parámetros: se ha restringido el acceso a usuarios no aplicables.
    * Falsificación de solicitud entre sitios: se implementó el concepto de token que se genera aleatoriamente para cada página. Esto significa que solo hay una instancia de inicio de sesión único para el mismo usuario y que la actualización de la página no funciona. En lugar de eso, redirige al panel.
    * Carga de archivos malintencionados: los archivos están restringidos a extensiones específicas: z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml y zip.
    * Scripting entre sitios persistente: se agregaron validaciones de entrada.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 actualización 2 (actualización del 3 de diciembre de 2015)

Las revisiones de la actualización 2 incluyen:

* **Servidor de configuración**: problemas que impedían que la característica de disponibilidad gratuita durante 31 días funcionase según lo esperado al registrar el servidor de configuración en Site Recovery.
* **Agente unificado**: corrige un problema en la actualización 1, cuyo resultado era que la actualización no se instalaba en el servidor de destino maestro durante la actualización desde la versión 8.0 a 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 actualización 1
La actualización 1 incluye las siguientes revisiones de errores y características nuevas:

* 31 días de protección gratuita por instancia de servidor. Esto le permite probar la funcionalidad o configurar una prueba de concepto.
* Todas las operaciones en el servidor, incluida la conmutación por error y la conmutación por recuperación, son gratis los primeros 31 días. El tiempo se inicia cuando se protege por primera vez un servidor con Site Recovery Scout. Desde el 32º día, a cada servidor protegido se le cobra la tarifa de instancia estándar de la protección de Site Recovery en un sitio propiedad de un cliente.
* En cualquier momento, el número de servidores protegidos que se están cargando actualmente está disponible en el **Panel** del almacén.
* Se agregó compatibilidad con la interfaz de la línea de comandos (vCLI) de vSphere 5.5 Update 2.
* Se agregó compatibilidad con los sistemas operativos Linux en el servidor de origen:
    * RHEL 6 Update 6
    * RHEL 5 Update 11
    * CentOS 6 Update 6
    * CentOS 5 Update 11
* Correcciones de errores para resolver los problemas siguientes:
  * Error en el registro del almacén para el servidor de configuración o el servidor RX.
  * Los volúmenes de clúster no aparecen del modo esperado cuando las máquinas virtuales agrupadas en clústeres se vuelven a proteger al reanudarse.
  * La conmutación por recuperación falla cuando el servidor de destino maestro se hospeda en un servidor ESXi diferente de las máquinas virtuales de producción locales.
  * Los permisos de archivo de configuración se cambian al actualizar a la versión 8.0.1. Este cambio afecta la protección y las operaciones.
  * El umbral de resincronización no se aplica del modo esperado, lo que genera un comportamiento de replicación incoherente.
  * La configuración de RPO no aparece correctamente en la consola del servidor de configuración. El valor de datos sin comprimir muestra incorrectamente el valor comprimido.
  * La operación de eliminación no efectúa la eliminación como se esperaba en el asistente de vContinuum y la replicación no se elimina de la consola del servidor de configuración.
  * En el asistente de vContinuum, el disco deja automáticamente de estar seleccionado al hacer clic en **Detalles** en la vista de disco durante la protección de las máquinas virtuales MSCS.
  * En el escenario físico a virtual (P2V), algunos servicios HP requeridos (como CIMnotify y CqMgHost) no se mueven a manual en la recuperación de la máquina virtual. Este problema genera que se produzca un tiempo de arranque adicional.
  * Se produce un error en la protección de la máquina virtual Linux cuando hay más de 26 discos en el servidor de destino maestro.

