---
title: "Planeamiento de una implementación de Azure File Sync (versión preliminar) |Microsoft Docs"
description: "Conozca los aspectos que debe tener en cuenta al planear una implementación de Azure Files."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: d626f71aa21cea562ef6c9554c05e6de027e7f4d
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Planeamiento de una implementación de Azure File Sync (versión preliminar)
Azure File Sync (versión preliminar) permite centralizar los recursos compartidos de archivos de su organización en Azure Files sin renunciar a la flexibilidad, el rendimiento y la compatibilidad de un servidor de archivos local. Para ello la transformación de los servidores Windows Server en una caché rápida de los recursos compartidos de Azure Files. Puede usar cualquier protocolo disponible en Windows Server para tener acceso a los datos localmente (incluidos SMB, NFS y FTPS) y puede tener tantas cachés según sea necesario en todo el mundo.

En esta guía se describe lo que debe tener en cuenta al implementar Azure File Sync. Se recomienda que lea la guía [Planeamiento de una implementación de Azure Files](storage-files-planning.md). 

## <a name="understanding-azure-file-sync-terminology"></a>Comprensión de la terminología de Azure File Sync
Antes de entrar en detalles sobre Azure File Sync, es importante comprender la terminología.

### <a name="storage-sync-service"></a>Servicio de sincronización de almacenamiento
El servicio de sincronización de almacenamiento es el recurso de Azure de nivel superior que viene representado por Azure File Sync. El recurso del servicio de sincronización de almacenamiento es un homólogo del recurso de la cuenta de Storage, y se puede implementar igualmente en grupos de recursos de Azure. Se requiere un recurso de nivel superior distinto del recurso de la cuenta de Storage dado que el servicio de sincronización de almacenamiento puede crear relaciones de sincronización con varias cuentas de almacenamiento mediante varios grupos de sincronización. Una suscripción puede tener implementados varios recursos del servicio de sincronización de almacenamiento.

### <a name="sync-group"></a>Grupo de sincronización
Un grupo de sincronización define la topología de sincronización de un conjunto de archivos. Los puntos de conexión dentro de un grupo de sincronización se mantienen sincronizadas entre sí. Si, por ejemplo, si tienen dos conjuntos distintos de archivos que desea administrar con AFS, podría crear dos grupos de sincronización y agregar a cada uno puntos de conexión diferentes. Un servicio de sincronización de almacenamiento puede hospedar tantos grupos de sincronización como sean necesarios.  

### <a name="registered-server"></a>Servidor registrado
El objeto Servidor registrado representa una relación de confianza entre el servidor (o clúster) y el servicio de sincronización de almacenamiento. Puede registrar tantos servidores como desee en una instancia del servicio de sincronización de almacenamiento. Sin embargo, un servidor (o clúster) solo se puede registrar con un servicio de sincronización de almacenamiento en un momento dado.

### <a name="azure-file-sync-agent"></a>Agente de Azure File Sync
El agente de Azure File Sync es un paquete descargable que permite la sincronización de una instancia de Windows Server con un recurso compartido de Azure Files. El agente de Azure File Sync consta de tres componentes principales: 
- **FileSyncSvc.exe**: el servicio de Windows en segundo plano responsable de supervisar los cambios en los puntos de conexión de servidor y de iniciar las sesiones de sincronización en Azure.
- **StorageSync.sys**: el filtro del sistema de archivos de Azure File Sync, responsable de apilar los archivos en Azure Files (cuando está habilitada la característica de niveles de nube).
- **Cmdlets de administración de PowerShell**: cmdlets de PowerShell para la interacción con el proveedor de recursos de Azure Microsoft.StorageSync. Se pueden encontrar en las siguientes ubicaciones (de forma predeterminada):
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Punto de conexión de servidor
Un punto de conexión de servidor representa una ubicación específica en un servidor registrado, como una carpeta en un volumen de servidor o la raíz del volumen. Pueden existir varios puntos de conexión de servidor en el mismo volumen si sus espacios de nombres no se superponen (por ejemplo, F:\sync1 y F:\sync2). Además, puede configurar directivas de niveles de nube de manera individual para cada punto de conexión de servidor. Si agrega una ubicación de servidor con un conjunto de archivos existente como punto de conexión de servidor a un grupo de sincronización, esos archivos se combinarán con otros archivos que ya se encuentren en otros puntos de conexión del grupo de sincronización.

### <a name="cloud-endpoint"></a>Punto de conexión de nube
Un punto de conexión de nube es un recurso compartido de archivos de Azure que forma parte de un grupo de sincronización. El recurso compartido de archivos de Azure entero se sincroniza; un recurso compartido de archivos de Azure solo puede ser miembro de un punto de conexión de nube y, por tanto, de un grupo de sincronización. Si agrega un recurso compartido de archivos de Azure con un conjunto de archivos existente como punto de conexión de nube a un grupo de sincronización, esos archivos se combinarán con otros archivos que ya se encuentren en otros puntos de conexión del grupo de sincronización.

> [!Important]  
> Azure File Sync permite realizar cambios directamente en el recurso compartido de archivos de Azure, pero tenga en cuenta que un trabajo de detección de cambios de Azure File Sync deberá descubrir primero los cambios realizados en el recurso compartido de archivos de Azure. Recuerde que este trabajo de detección se inicia una sola vez cada 24 horas en el punto de conexión de nube. Vea las [preguntas más frecuentes de Azure Files](storage-files-faq.md#afs-change-detection) para obtener más información.

### <a name="cloud-tiering"></a>Niveles de nube 
Niveles de nube es una característica opcional de Azure File Sync, que permite que los archivos a los que se accede o que se usan con poca frecuencia se apilen en Azure Files. Cuando un archivo está en capas, el filtro del sistema de archivos de Azure File Sync (StorageSync.sys) sustituye el archivo localmente por un puntero, o punto de repetición de análisis, que representa una dirección URL al archivo en Azure Files. Un archivo con niveles tiene establecido el atributo "sin conexión" en NTFS, así que las aplicaciones de terceros pueden identificar este tipo de archivo. Cuando un usuario abre un archivo con niveles, el servicio Azure File Sync recupera completamente los datos de archivo de Azure Files sin necesidad de que el usuario sepa que el archivo no está almacenado localmente en el sistema. Esta funcionalidad también se conoce como administración de almacenamiento jerárquico (HSM).

## <a name="azure-file-sync-interoperability"></a>Interoperabilidad de Azure File Sync 
En esta sección se trata la interoperabilidad de Azure File Sync son características y roles de Windows Server y soluciones de terceros.

### <a name="supported-versions-of-windows-server"></a>Versiones compatibles de Windows Server
En la actualidad, la versiones de Windows Server que admite Azure File Sync son:

| Versión | SKU compatibles | Opciones de implementación compatibles |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter y Standard | Completo (servidor con una interfaz de usuario) |
| Windows Server 2012 R2 | Datacenter y Standard | Completo (servidor con una interfaz de usuario) |

Se agregarán futuras versiones de Windows Server a medida que se vayan publicando y podrían agregarse versiones anteriores de Windows en función de los comentarios de los usuarios.

> [!Important]  
> Se recomienda mantener sincronizadas todas las instancias de Windows Server usadas con Azure File Sync con las actualizaciones más recientes de Windows Update. 

### <a name="file-system-features"></a>Características del sistema de archivos
| Característica | Compatibilidad con estado | Notas |
|---------|----------------|-------|
| Listas de control de acceso (ACL) | Totalmente compatible | Azure File Sync conserva las listas ACL de Windows, y Windows Server las exige en los puntos de conexión de servidor. Las listas ACL de Windows no se admiten (aún) en Azure Files cuando se accede directamente a los archivos en la nube. |
| Vínculos físicos | Skipped | |
| Vínculos simbólicos | Skipped | |
| Puntos de montaje | Compatibilidad parcial | Los puntos de montaje pueden ser la raíz de un punto de conexión de servidor, pero se omitirán si están incluidos en el espacio de nombres del punto de conexión de servidor. |
| Uniones | Skipped | |
| Puntos de repetición de análisis | Skipped | |
| Compresión NTFS | Totalmente compatible | |
| Archivos dispersos | Totalmente compatible | Los archivos dispersos se sincronizarán (no se bloquearán), pero lo harán con la nube como un archivo completo. Si se cambia el contenido del archivo en la nube (o en otro servidor), el archivo ya no estará disperso, cuando el cambio se haya descargado. |
| Flujos de datos alternativos (ADS) | Conservados, pero no sincronizados | |

> [!Note]  
> Solo se admiten volúmenes NTFS.

### <a name="failover-clustering"></a>Clústeres de conmutación por error
La característica de clústeres de conmutación por error de Windows es compatible con Azure File Sync en la opción de implementación "Servidor de archivos para uso general". La característica de clústeres de conmutación por error no se admite en "Servidor de archivos de escalabilidad horizontal para datos de aplicación" (SOFS) o en volúmenes compartidos de clúster (CSV).

> [!Note]  
> El agente de Azure File Sync debe estar instalado en cada nodo de un clúster de conmutación por error para que la sincronización funcione correctamente.

### <a name="windows-server-data-deduplication"></a>Desduplicación de datos de Windows Server
En el caso de volúmenes que no tengan habilitada la característica de niveles de nube, Azure File Sync admite la habilitación de la desduplicación de datos en el volumen. Por el momento, no se admite la interoperabilidad entre Azure File Sync con la funcionalidad de niveles de nube habilitada y la desduplicación de datos.

### <a name="anti-virus-solutions"></a>Soluciones antivirus
Dado que un antivirus funciona examinando los archivos en busca de código malintencionado conocido, puede provocar la recuperación de archivos con niveles. Puesto que los archivos con niveles tienen establecido el atributo "sin conexión", se recomienda consultar con el proveedor de software respecto a cómo configurar su solución para omitir la lectura de archivos sin conexión. 

Las siguientes soluciones se sabe que admiten la omisión de archivos sin conexión:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (consulte la sección "Scan only what you need to" (Examen solo de lo necesario) en la página 90)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Soluciones de copia de seguridad
Al igual que sucede con las soluciones antivirus, las soluciones de copia de seguridad pueden provocar la recuperación de archivos con niveles. Se recomienda usar una solución de copia de seguridad en la nube para realizar la copia de seguridad del recurso compartido de archivos de Azure en lugar de usar un producto de copia de seguridad local.

### <a name="encryption-solutions"></a>Soluciones de cifrado
La compatibilidad con soluciones de cifrado depende de cómo se implementen. Está comprobado que Azure File Sync funciona con:

- Cifrado de BitLocker
- Azure RMS (y Active Directory RMS heredado)

está comprobado que Azure File Sync no funciona con:

- Sistema de cifrado de archivos NTFS (EFS)

En general, Azure File Sync debería poder interoperar con soluciones de cifrado que se sitúan debajo del sistema de archivos, como BitLocker, y soluciones implementadas en el formato de archivo, como BitLocker, pero no se ha creado ninguna interoperabilidad especial para ello.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Otras soluciones de administración de almacenamiento jerárquico (HSM)
Ninguna otra solución de administración de almacenamiento jerárquico debe usarse con Azure File Sync.

## <a name="region-availability"></a>Disponibilidad en regiones
Azure File Sync solo está disponible en las siguientes regiones en versión preliminar:

| Region | Ubicación de centro de datos |
|--------|---------------------|
| Oeste de EE. UU. | California, Estados Unidos |
| Europa occidental | Países Bajos |
| Sudeste de Asia | Singapur |
| Australia Oriental | Nueva Gales del Sur, Australia |

En la versión preliminar, solo se admite la sincronización con recursos compartidos de Azure File de la misma región que el servicio de sincronización de almacenamiento.

## <a name="azure-file-sync-agent-update-policy"></a>Directiva de actualización del agente de Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Pasos siguientes
* [Planeamiento de una implementación de Azure Files](storage-files-planning.md)
* [Implementación de Azure Files](storage-files-deployment-guide.md)
* [Implementación de Azure File Sync](storage-sync-files-deployment-guide.md)
