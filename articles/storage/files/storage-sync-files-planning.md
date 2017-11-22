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
ms.openlocfilehash: 241b744f5c5e89f53addb4d41d732245d76ef9a3
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2017
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Planeamiento de una implementación de Azure File Sync (versión preliminar)
Use Azure File Sync (versión preliminar) para centralizar los recursos compartidos de archivos de su organización en Azure Files sin renunciar a la flexibilidad, el rendimiento y la compatibilidad de un servidor de archivos local. Azure File Sync transforma Windows Server en una caché rápida de los recursos compartidos de los archivos de Azure. Puede usar cualquier protocolo disponible en Windows Server para acceder a sus datos localmente, como SMB, NFS y FTPS. Puede tener todas las cachés que necesite en todo el mundo.

Este artículo describe las consideraciones importantes para una implementación de Azure File Sync. Se recomienda que lea [Planeamiento de una implementación de Azure Files](storage-files-planning.md). 

## <a name="azure-file-sync-terminology"></a>Terminología de Azure File Sync
Antes de entrar en detalles para planear una implementación de Azure File Sync, es importante comprender la terminología.

### <a name="storage-sync-service"></a>Servicio de sincronización de almacenamiento
El servicio de sincronización de almacenamiento es el recurso de Azure de nivel superior para Azure File Sync. El recurso del servicio de sincronización de almacenamiento es un homólogo del recurso de la cuenta de almacenamiento, y se puede implementar igualmente en grupos de recursos de Azure. Se requiere un recurso de nivel superior distinto del recurso de la cuenta de almacenamiento dado que el servicio de sincronización de almacenamiento puede crear relaciones de sincronización con varias cuentas de almacenamiento mediante varios grupos de sincronización. Una suscripción puede tener implementados varios recursos del servicio de sincronización de almacenamiento.

### <a name="sync-group"></a>Grupo de sincronización
Un grupo de sincronización define la topología de sincronización de un conjunto de archivos. Los puntos de conexión dentro de un grupo de sincronización se mantienen sincronizados entre sí. Si, por ejemplo, tiene dos conjuntos distintos de archivos que desea administrar con Azure File Sync, podría crear dos grupos de sincronización y agregar a cada uno puntos de conexión diferentes. Un servicio de sincronización de almacenamiento puede hospedar tantos grupos de sincronización como sean necesarios.  

### <a name="registered-server"></a>Servidor registrado
El objeto Servidor registrado representa una relación de confianza entre el servidor (o clúster) y el servicio de sincronización de almacenamiento. Puede registrar tantos servidores como desee en una instancia del servicio de sincronización de almacenamiento. Sin embargo, un servidor (o clúster) se puede registrar con un solo servicio de sincronización de almacenamiento a la vez.

### <a name="azure-file-sync-agent"></a>Agente de Azure File Sync
El agente de Azure File Sync es un paquete descargable que permite la sincronización de una instancia de Windows Server con un recurso compartido de archivos de Azure. El agente de Azure File Sync consta de tres componentes principales: 
- **FileSyncSvc.exe**: el servicio de Windows en segundo plano responsable de supervisar los cambios en los puntos de conexión de servidor y de iniciar las sesiones de sincronización en Azure.
- **StorageSync.sys**: el filtro del sistema de archivos de Azure File Sync, responsable de apilar los archivos en Azure Files (cuando está habilitada la característica de niveles de la nube).
- **Cmdlets de administración de PowerShell**: cmdlets de PowerShell para la interacción con el proveedor de recursos de Azure Microsoft.StorageSync. Puede encontrar estos elementos en las siguientes ubicaciones (valor predeterminado):
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Punto de conexión de servidor
Un punto de conexión de servidor representa una ubicación específica en un servidor registrado, como una carpeta en un volumen de servidor o la raíz del volumen. Pueden existir varios puntos de conexión de servidor en el mismo volumen si sus espacios de nombres no se superponen (por ejemplo, F:\sync1 y F:\sync2). Además, puede configurar directivas de niveles de nube de manera individual para cada punto de conexión de servidor. Si agrega una ubicación de servidor con un conjunto de archivos existente como punto de conexión de servidor a un grupo de sincronización, esos archivos se combinarán con otros archivos que ya se encuentren en otros puntos de conexión del grupo de sincronización.

### <a name="cloud-endpoint"></a>Punto de conexión de nube
Un punto de conexión de nube es un recurso compartido de archivos de Azure que forma parte de un grupo de sincronización. El recurso compartido de archivos de Azure entero se sincroniza; un recurso compartido de archivos de Azure solo puede ser miembro de un punto de conexión de nube. Por lo tanto, un recurso compartido de archivos de Azure puede ser un miembro de un solo grupo de sincronización. Si agrega un recurso compartido de archivos de Azure con un conjunto de archivos existente como punto de conexión de nube a un grupo de sincronización, esos archivos se combinarán con otros archivos que ya se encuentren en otros puntos de conexión del grupo de sincronización.

> [!Important]  
> Azure File Sync admite realizar cambios directamente en el recurso compartido de archivos de Azure. Sin embargo, los cambios realizados en el recurso compartido de archivos de Azure primero deben ser detectados por un trabajo de detección de cambios de Azure File Sync. Se inicia un trabajo de detección de cambios para un punto de conexión en la nube solo una vez cada 24 horas. Para más información, visite [Preguntas más frecuentes de Azure Files](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Niveles de nube 
Niveles de nube es una característica opcional de Azure File Sync, con la que los archivos a los que se accede o que se usan con poca frecuencia se pueden apilar en Azure Files. Cuando un archivo está en capas, el filtro del sistema de archivos de Azure File Sync (StorageSync.sys) sustituye al archivo localmente por un puntero o punto de repetición de análisis. El punto de repetición de análisis representa una dirección URL del archivo en Azure Files. Un archivo con niveles tiene establecido el atributo "sin conexión" en NTFS, de tal modo que las aplicaciones de terceros puedan identificar este tipo de archivo. Cuando un usuario abre un archivo con niveles, el servicio Azure File Sync recupera completamente los datos de archivo de Azure Files sin necesidad de que el usuario sepa que el archivo no está almacenado localmente en el sistema. Esta funcionalidad también se conoce como administración de almacenamiento jerárquico (HSM).

## <a name="azure-file-sync-interoperability"></a>Interoperabilidad de Azure File Sync 
En esta sección se trata la interoperabilidad de Azure File Sync son características y roles de Windows Server y soluciones de terceros.

### <a name="supported-versions-of-windows-server"></a>Versiones compatibles de Windows Server
En la actualidad, la versiones de Windows Server que admite Azure File Sync son:

| Versión | SKU compatibles | Opciones de implementación compatibles |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter y Standard | Completo (servidor con una interfaz de usuario) |
| Windows Server 2012 R2 | Datacenter y Standard | Completo (servidor con una interfaz de usuario) |

Las versiones futuras de Windows Server se agregarán tan pronto como se publiquen. Las versiones anteriores de Windows podrían agregarse en función de los comentarios del usuario.

> [!Important]  
> Se recomienda mantener sincronizadas todas las instancias que use con Azure File Sync con las actualizaciones más recientes de Windows Update. 

### <a name="file-system-features"></a>Características del sistema de archivos
| Característica | Compatibilidad con el estado | Notas |
|---------|----------------|-------|
| Listas de control de acceso (ACL) | Totalmente compatible | Azure File Sync conserva las listas ACL de Windows, y Windows Server las exige en los puntos de conexión de servidor. Las listas ACL de Windows no se admiten (aún) en Azure Files cuando se accede directamente a los archivos en la nube. |
| Vínculos físicos | Skipped | |
| Vínculos simbólicos | Skipped | |
| Puntos de montaje | Compatibilidad parcial | Los puntos de montaje podrían ser la raíz de un punto de conexión de servidor, pero se omiten si están incluidos en el espacio de nombres del punto de conexión de servidor. |
| Uniones | Skipped | |
| Puntos de repetición de análisis | Skipped | |
| Compresión NTFS | Totalmente compatible | |
| Archivos dispersos | Totalmente compatible | Los archivos dispersos se sincronizan (no se bloquean), pero lo hacen con la nube como un archivo completo. Si se cambia el contenido del archivo en la nube (o en otro servidor), el archivo ya no estará disperso cuando el cambio se haya descargado. |
| Flujos de datos alternativos (ADS) | Conservados, pero no sincronizados | |

> [!Note]  
> Solo se admiten volúmenes NTFS.

### <a name="failover-clustering"></a>Clústeres de conmutación por error
La característica de clústeres de conmutación por error de Windows es compatible con Azure File Sync en la opción de implementación "Servidor de archivos para uso general". La característica de clústeres de conmutación por error no se admite en "Servidor de archivos de escalabilidad horizontal para datos de aplicación" (SOFS) o en volúmenes compartidos de clúster (CSV).

> [!Note]  
> El agente de Azure File Sync debe estar instalado en cada nodo de un clúster de conmutación por error para que la sincronización funcione correctamente.

### <a name="data-deduplication"></a>Desduplicación de datos
En el caso de volúmenes que no tengan habilitada la característica de niveles de nube, Azure File Sync admite la habilitación de la desduplicación de datos de Windows Server en el volumen. En este momento, no se admite la interoperabilidad entre Azure File Sync con la funcionalidad de niveles de nube habilitada y la desduplicación de datos.

### <a name="antivirus-solutions"></a>Soluciones antivirus
Dado que un antivirus funciona examinando los archivos en busca de código malintencionado conocido, puede provocar la recuperación de archivos con niveles. Puesto que los archivos con niveles tienen establecido el atributo "sin conexión", se recomienda consultar con el proveedor de software respecto a cómo configurar su solución para omitir la lectura de archivos sin conexión. 

Las siguientes soluciones se sabe que admiten la omisión de archivos sin conexión:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (consulte "Scan only what you need to" [Examen solo de lo necesario en la página 90 del PDF])
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Soluciones de copia de seguridad
Al igual que sucede con las soluciones antivirus, las soluciones de backup pueden provocar la recuperación de archivos con niveles. Se recomienda usar una solución de backup en la nube para realizar la copia de seguridad del recurso compartido de archivos de Azure en lugar de usar un producto de backup local.

### <a name="encryption-solutions"></a>Soluciones de cifrado
La compatibilidad con soluciones de cifrado depende de cómo se implementen. Está comprobado que Azure File Sync funciona con:

- Cifrado de BitLocker
- Azure Rights Management Services (Azure RMS) (y Active Directory RMS heredado)

está comprobado que Azure File Sync no funciona con:

- Sistema de cifrado de archivos NTFS (EFS)

En general, Azure File Sync debe admitir la interoperabilidad con soluciones de cifrado que se encuentran a continuación en el sistema de archivos, como BitLocker, y con las soluciones que se implementan en el formato de archivo, como BitLocker. No se ha realizado ninguna interoperabilidad especial para las soluciones que se encuentran por encima del sistema de archivos (por ejemplo, EFS de NTFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Otras soluciones de administración de almacenamiento jerárquico (HSM)
No deben utilizarse otras soluciones HSM deben utilizarse con Azure File Sync.

## <a name="region-availability"></a>Disponibilidad en regiones
Azure File Sync solo está disponible en las siguientes regiones en versión preliminar:

| Region | Ubicación de centro de datos |
|--------|---------------------|
| Oeste de EE. UU. | California, Estados Unidos |
| Europa occidental | Países Bajos |
| Sudeste de Asia | Singapur |
| Australia Oriental | Nueva Gales del Sur, Australia |

En la versión preliminar, solo se admite la sincronización con recursos compartidos de archivos de Azure de la misma región que el servicio de sincronización de almacenamiento.

## <a name="azure-file-sync-agent-update-policy"></a>Directiva de actualización del agente de Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Pasos siguientes
* [Planeamiento de una implementación de Azure Files](storage-files-planning.md)
* [Implementación de Azure Files](storage-files-deployment-guide.md)
* [Implementación de Azure File Sync](storage-sync-files-deployment-guide.md)
