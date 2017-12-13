---
title: "Implementar Azure File Sync (versión preliminar) | Microsoft Docs"
description: Aprenda a implementar Azure File Sync de principio a fin.
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
ms.openlocfilehash: 7b4de3e7b7e98ab76c02ea7c1cf069cee94706fc
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-azure-file-sync-preview"></a>Implementar Azure File Sync (versión preliminar)
Use Azure File Sync (versión preliminar) para centralizar los recursos compartidos de archivos de su organización en Azure Files sin renunciar a la flexibilidad, el rendimiento y la compatibilidad de un servidor de archivos local. Azure File Sync transforma Windows Server en una caché rápida de los recursos compartidos de archivos de Azure. Puede usar cualquier protocolo disponible en Windows Server para acceder a sus datos localmente, como SMB, NFS y FTPS. Puede tener todas las cachés que necesite en todo el mundo.

Se recomienda encarecidamente leer [Planeamiento de una implementación de Azure Files](storage-files-planning.md) y [Planeamiento de una implementación de Azure File Sync](storage-sync-files-planning.md) antes de seguir los pasos que se describen en este artículo.

## <a name="prerequisites"></a>Requisitos previos
* Una cuenta de Azure Storage y un recurso compartido de archivos de Azure en la misma región en la que quiere implementar Azure File Sync. Para más información, consulte:
    - [Disponibilidad en regiones](storage-sync-files-planning.md#region-availability) de Azure File Sync.
    - [Crear una cuenta de almacenamiento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obtener una descripción paso a paso sobre cómo crear una cuenta de almacenamiento.
    - [Creación de un recurso compartido de archivos](storage-how-to-create-file-share.md) para obtener una descripción paso a paso sobre cómo crear un recurso compartido de archivos.
* Al menos una instancia de Windows Server o un clúster de Windows Server compatible para la sincronización con Azure File Sync. Para obtener más información acerca de las versiones compatibles de Windows Server, consulte [Interoperabilidad con Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability).

## <a name="deploy-the-storage-sync-service"></a>Implementación del servicio de sincronización de almacenamiento 
El servicio de sincronización de almacenamiento es el recurso de Azure de nivel superior para Azure File Sync. Para implementar un servicio de sincronización de almacenamiento, vaya a [Azure Portal](https://portal.azure.com/) y busque Azure File Sync. En los resultados de la búsqueda, seleccione **Azure File Sync (versión preliminar)** y, a continuación, seleccione **Crear** para abrir la pestaña **Implementar la sincronización del almacenamiento**.

En el panel que se abre, escriba la siguiente información:

- **Nombre**: un nombre único (por suscripción) para el servicio de sincronización de almacenamiento.
- **Suscripción**: la suscripción en la que quiere crear el servicio de sincronización de almacenamiento. Según la estrategia de configuración de la organización, puede tener acceso a una o más suscripciones. Una suscripción de Azure es el contenedor más básico para la facturación de cada servicio en la nube (como Azure Files).
- **Grupo de recursos**: un grupo de recursos es un grupo lógico de recursos de Azure, como una cuenta de almacenamiento o un servicio de sincronización de almacenamiento. Puede crear un nuevo grupo de recursos o seleccionar uno existente para Azure File Sync. (Se recomienda usar los grupos de recursos como contenedores para aislar los recursos de manera lógica para su organización, como la agrupación de recursos de RR. HH. o recursos de un proyecto específico).
- **Ubicación**: la región en la que quiere implementar Azure File Sync. Solo las regiones admitidas están disponibles en esta lista.

Cuando haya terminado, seleccione **Crear** para implementar el servicio de sincronización de almacenamiento.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Preparación de Windows Server para su uso con Azure File Sync
Para cada uno de los servidores que piense usar con Azure File Sync, incluidos los nodos de servidor de un clúster de conmutación por error, complete los pasos siguientes:

1. Deshabilite **Configuración de seguridad mejorada de Internet Explorer**. Esto solo es necesario para el registro inicial del servidor. Se puede volver a habilitar una vez registrado el servidor.
    1. Abra el Administrador del servidor.
    2. Haga clic en **Servidor Local**:  
        !["Servidor local" en el lado izquierdo de la interfaz de usuario Administrador del servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. En el panel secundario **Propiedades**, seleccione el vínculo de **Configuración de seguridad mejorada de IE**.  
        ![Panel "Configuración de seguridad mejorada de IE" en la interfaz de usuario Administrador del servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. En el cuadro de diálogo **Configuración de seguridad mejorada de Internet Explorer**, seleccione **Desactivado** para **Administradores** y **Usuarios**:  
        ![Ventana emergente de la configuración de seguridad mejorada de Internet Explorer con "Desactivado" seleccionado](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. Asegúrese de ejecutar al menos PowerShell 5.1.\* (PowerShell 5.1 es el valor predeterminado en Windows Server 2016). Puede comprobar que está ejecutando PowerShell 5.1.\* si examina el valor de la propiedad **PSVersion** del objeto **$PSVersionTable**:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Si el valor de PSVersion es menor que 5.1.\*, como sucede con la mayoría de las instalaciones de Windows Server 2012 R2, puede realizar la actualización fácilmente si descarga e instala [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). El paquete adecuado que hay que descargar e instalar para Windows Server 2012 R2 es **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**.

3. [Instale y configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Siempre se recomienda usar la versión más reciente de los módulos de Azure PowerShell.

## <a name="install-the-azure-file-sync-agent"></a>Instalación del agente de Azure File Sync
El agente de Azure File Sync es un paquete descargable que permite la sincronización de Windows Server con un recurso compartido de archivos de Azure. Puede descargar el agente desde el [Centro de descarga de Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Una finalice la descarga, haga doble clic en el paquete MSI para iniciar la instalación del agente de Azure File Sync.

> [!Important]  
> Si piensa usar Azure File Sync con un clúster de conmutación por error, el agente de Azure File Sync se debe instalar en todos los nodos del clúster.

El paquete de instalación del agente de Azure File Sync se debería instalar relativamente rápido y sin demasiados mensajes adicionales. Se recomienda hacer lo siguiente:
- Dejar la ruta de instalación predeterminada (C:\Archivos de programa\Azure\StorageSyncAgent), para simplificar las operaciones de solución de problemas y mantenimiento del servidor.
- Habilitar Microsoft Update para mantener actualizado Azure File Sync. Todas las actualizaciones del agente de Azure File Sync, incluidas las actualizaciones y revisiones de características, se realizan desde Microsoft Update. Se recomienda instalar la actualización más reciente de Azure File Sync. Para obtener más información, consulte [Directiva de actualización del agente de Azure File Sync](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Al término de la instalación del agente de Azure File Sync, la interfaz de usuario Registro del servidor se abre automáticamente. Vea la sección siguiente para aprender a registrar este servidor con Azure File Sync.

## <a name="register-windows-server-with-storage-sync-service"></a>Registro de un servidor de Windows Server con el servicio de sincronización de almacenamiento
El registro de Windows Server con un servicio de sincronización de almacenamiento establece una relación de confianza entre el servidor (o clúster) y el servicio de sincronización de almacenamiento. Al término de la instalación del agente de Azure File Sync, la interfaz de usuario Registro del servidor debería abrirse automáticamente. Si no es así, puede abrirla manualmente desde la ubicación del archivo: C:\Archivos de programa\Azure\StorageSyncAgent\ServerRegistration.exe. Cuando la interfaz de usuario Registro del servidor se abra, seleccione **Iniciar sesión** para comenzar.

Después de iniciar sesión se le pedirá la información siguiente:

![Captura de pantalla de la interfaz de usuario Registro del servidor](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Suscripción de Azure**: la suscripción que contiene el servicio de sincronización de almacenamiento (vea [Implementación del servicio de sincronización de almacenamiento](#deploy-the-storage-sync-service)). 
- **Grupo de recursos**: el grupo de recursos que contiene el servicio de sincronización de almacenamiento.
- **Servicio de sincronización de almacenamiento**: el nombre del servicio de sincronización de almacenamiento con el que quiere registrar.

Una vez que haya seleccionado la información adecuada, haga clic en **Registrar** para completar el registro del servidor. Como parte del proceso de registro, se le solicita un inicio de sesión adicional.

## <a name="create-a-sync-group"></a>Creación de un grupo de sincronización
Un grupo de sincronización define la topología de sincronización de un conjunto de archivos. Los puntos de conexión dentro de un grupo de sincronización se mantienen sincronizados entre sí. Un grupo de sincronización debe contener al menos un punto de conexión en la nube, que representa un recurso compartido de archivos de Azure y un punto de conexión de servidor, que representa una ruta de acceso en Windows Server. Para crear un grupo de sincronización, en [Azure Portal](https://portal.azure.com/), vaya al servicio de sincronización de almacenamiento y haga clic en **+ Grupo de sincronización**:

![Creación de un grupo de sincronización en Azure Portal](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

En el panel que se abre, escriba la información siguiente para crear un grupo de sincronización con un punto de conexión en la nube:

- **Nombre del grupo de sincronización**: el nombre del grupo de sincronización que se va a crear. Este nombre debe ser único dentro del servicio de sincronización de almacenamiento, pero puede ser cualquier nombre que considere lógico.
- **Suscripción**: la suscripción en la que ha implementado el servicio de sincronización de almacenamiento en [Implementación del servicio de sincronización de almacenamiento](#deploy-the-storage-sync-service).
- **Cuenta de almacenamiento**: si elige **Seleccionar cuenta de almacenamiento**, aparece otro panel donde puede seleccionar la cuenta de almacenamiento que tiene el recurso compartido de archivos de Azure con el que quiere realizar la sincronización.
- **Recurso compartido de archivos de Azure**: el nombre del recurso compartido de archivos de Azure con el que quiere realizar la sincronización.

Para agregar un punto de conexión de servidor, vaya al grupo de sincronización recién creado y seleccione **Agregar punto de conexión del servidor**.

![Adición de un nuevo punto de conexión de servidor al panel Grupo de sincronización](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

En el panel **Agregar punto de conexión de servidor**, escriba la siguiente información para crear un punto de conexión de servidor:

- **Servidor registrado**: el nombre del servidor o el clúster en el que se va a crear el punto de conexión de servidor.
- **Ruta de acceso**: la ruta de acceso de Windows Server que se va a sincronizar como parte del grupo de sincronización.
- **Niveles de la nube**: un conmutador para habilitar o deshabilitar los niveles de la nube. Con los niveles de la nube, los archivos que se usen o a los que se acceda con poca frecuencia se pueden colocar en capas en Azure Files.
- **Espacio disponible del volumen**: la cantidad de espacio libre que se reserva en el volumen en el que reside el punto de conexión de servidor. Por ejemplo, si el espacio disponible del volumen se establece en el 50 % en un volumen con un único punto de conexión de servidor, casi la mitad de la cantidad de datos se coloca en capas en Azure Files. Con independencia de si la característica de niveles en la nube está habilitada, el recurso compartido de archivos de Azure siempre tiene una copia completa de los datos en el grupo de sincronización.

Para agregar el punto de conexión de servidor, seleccione **Crear**. Los archivos se mantienen ahora sincronizados entre el recurso compartido de archivos de Azure y Windows Server. 

> [!Important]  
> Puede realizar cambios en cualquier punto de conexión en la nube o punto de conexión de servidor en el grupo de sincronización y sincronizar los archivos con los demás puntos de conexión del grupo de sincronización. Si realiza algún cambio directamente en el punto de conexión en la nube (recurso compartido de archivos de Azure), tenga en cuenta que un trabajo de detección de cambios de Azure File Sync deberá detectar primero esos cambios. Se inicia un trabajo de detección de cambios para un punto de conexión en la nube solo una vez cada 24 horas. Para obtener más información, consulte [Preguntas más frecuentes de Azure Files](storage-files-faq.md#afs-change-detection).

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migración de una implementación de la replicación DFS (DFS-R) a Azure File Sync
Para migrar una implementación de DFS-R a Azure File Sync:

1. Cree un grupo de sincronización para representar la topología DFS-R que se va a reemplazar.
2. Comience por el servidor que tiene el conjunto completo de datos en la topología DFS-R que se va a migrar. Instale Azure File Sync en ese servidor.
3. Registre ese servidor y cree un punto de conexión de servidor para el primer servidor que desea migrar. No habilite niveles en la nube.
4. Deje que todos los datos se sincronicen con el recurso compartido de archivos de Azure (punto de conexión en la nube).
5. Instale y registre el agente de Azure File Sync en cada uno de los demás servidores de DFS-R.
6. Deshabilite DFS-R. 
7. Cree un punto de conexión de servidor en cada uno de los servidores de DFS-R. No habilite niveles en la nube.
8. Asegúrese de que la sincronización finaliza y pruebe la topología según sea necesario.
9. Retire DFS-R.
10. Ahora se pueden habilitar los niveles en la nube en cualquier punto de conexión de servidor según sea necesario.

Para más información, consulte [Interoperabilidad de Azure File Sync con el sistema de archivos distribuido (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Pasos siguientes
- [Adición o eliminación de un punto de conexión de servidor de Azure File Sync ](storage-sync-files-server-endpoint.md)
- [Registro y cancelación del registro de un servidor con Azure File Sync](storage-sync-files-server-registration.md)
