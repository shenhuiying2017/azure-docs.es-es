---
title: "Adición o eliminación de un punto de conexión de servidor de Azure File Sync (versión preliminar) | Microsoft Docs"
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
ms.openlocfilehash: 2ab14183a0ca4ade7873dbdece407937a746b663
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="addremove-an-azure-file-sync-preview-server-endpoint"></a>Adición o eliminación de un punto de conexión de servidor de Azure File Sync (versión preliminar)
Azure File Sync (versión preliminar) permite centralizar los recursos compartidos de archivos de su organización en Azure Files sin renunciar a la flexibilidad, el rendimiento y la compatibilidad de un servidor de archivos local. Para ello la transformación de los servidores Windows Server en una caché rápida de los recursos compartidos de Azure Files. Puede usar cualquier protocolo disponible en Windows Server para tener acceso a los datos localmente (incluidos SMB, NFS y FTPS) y puede tener tantas cachés según sea necesario en todo el mundo.

Un *punto de conexión de servidor* representa una ubicación específica en un *servidor registrado*, como una carpeta en un volumen de servidor o la raíz del volumen. Pueden existir varios puntos de conexión de servidor en el mismo volumen si sus espacios de nombres no se superponen (por ejemplo, F:\sync1 y F:\sync2). Además, puede configurar directivas de niveles de nube de manera individual para cada punto de conexión de servidor. Si agrega una ubicación de servidor con un conjunto de archivos existente como punto de conexión de servidor a un grupo de sincronización, esos archivos se combinarán con otros archivos que ya se encuentren en otros puntos de conexión del grupo de sincronización.

Consulte [How to deploy Azure File Sync (preview)](storage-sync-files-deployment-guide.md) (Implementación de Azure File Sync [versión preliminar]).

## <a name="prerequisites"></a>Requisitos previos
Para crear un punto de conexión de servidor, primero debe asegurarse de que se cumplen los criterios siguientes: 
- El servidor tiene instalado el agente de Azure File Sync y se ha registrado. Puede encontrar instrucciones para instalar el agente de Azure File Sync en el artículo [Registro y cancelación del registro de un servidor con Azure File Sync (versión preliminar)](storage-sync-files-server-registration.md). 
- Asegúrese de que se ha implementado un servicio de sincronización de almacenamiento. Para más información sobre cómo implementar un servicio de sincronización de almacenamiento, consulte [How to deploy Azure File Sync (preview)](storage-sync-files-deployment-guide.md) (Implementación de Azure File Sync [versión preliminar]). 
- Asegúrese de que se ha implementado un grupo de sincronización. Aprenda a [crear un grupo de sincronización](storage-sync-files-deployment-guide.md#create-a-sync-group).
- Asegúrese de que el servidor está conectado a Internet y que se puede acceder a Azure.

## <a name="add-a-server-endpoint"></a>Adición de un punto de conexión de servidor
Para agregar un punto de conexión de servidor, vaya al grupo de sincronización que prefiera y seleccione "Agregar punto de conexión del servidor".

![Adición de un nuevo punto de conexión de servidor al panel Grupo de sincronización](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

La siguiente información es necesaria en **Agregar punto de conexión del servidor**:

- **Servidor registrado**: nombre del servidor o el clúster en el que crear el punto de conexión de servidor.
- **Ruta de acceso**: ruta de acceso del servidor de Windows Server que se va a sincronizar como parte del grupo de sincronización.
- **Niveles de la nube**: un conmutador para habilitar o deshabilitar niveles de nube, que permite que los archivos a los que se accede o que se usan con poca frecuencia se apilen en Azure Files.
- **Espacio disponible del volumen**: cantidad de espacio libre que se reserva en el volumen en el que reside el punto de conexión de servidor. Por ejemplo, si el espacio disponible del volumen se establece en el 50 % en un volumen con un único punto de conexión de servidor, casi la mitad de la cantidad de datos se coloca en niveles en Azure Files. Con independencia de si la característica de niveles de nube está habilitada, el recurso compartido de archivos de Azure siempre tiene una copia completa de los datos en el grupo de sincronización.

Seleccione **Crear** para agregar el punto de conexión de servidor. Los archivos de un espacio de nombres de un grupo de sincronización se mantienen ahora sincronizados. 

## <a name="remove-a-server-endpoint"></a>Eliminación de un punto de conexión de servidor
Cuando la característica de niveles de nube está habilitada para un punto de conexión de servidor, los archivos se *apilan* a los recursos compartidos de Azure Files. De esta forma, los recursos compartidos de archivos locales funcionan como una caché, en lugar de como una copia completa del conjunto de datos, para realizar un uso eficiente del espacio del servidor de archivos. Sin embargo, si se quita un punto de conexión de servidor con archivos con niveles que aún se encuentran localmente en el servidor, esos archivos se volverán inaccesibles. Por tanto, si sigue queriendo acceder a los archivos, debe recuperar todos los archivos con niveles de Azure Files antes de continuar con la cancelación del registro. 

Para ello, se puede usar el cmdlet de PowerShell, tal y como se muestra a continuación:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Si el volumen local que hospeda el servidor no tiene suficiente espacio disponible para recuperar todos los datos con niveles, el cmdlet `Invoke-StorageSyncFileRecall` dará error.  

Para quitar el punto de conexión de servidor:

1. Vaya al servicio de sincronización de almacenamiento donde está registrado el servidor.
2. Desplácese al grupo de sincronización deseado.
3. Quite el punto de conexión de servidor que desee del grupo de sincronización del servicio de sincronización de almacenamiento. Para ello, haga clic con el botón derecho en el punto de conexión de servidor que le interese en el panel Grupo de sincronización.

    ![Eliminación de un punto de conexión de servidor de un grupo de sincronización](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Pasos siguientes
- [Registro y cancelación del registro de un servidor con Azure File Sync (versión preliminar)](storage-sync-files-server-registration.md)
- [Planeamiento de una implementación de Azure File Sync](storage-sync-files-planning.md)