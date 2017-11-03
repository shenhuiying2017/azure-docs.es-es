---
title: "Cómo implementar Azure File Sync (versión preliminar) | Microsoft Docs"
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
ms.openlocfilehash: b31b6ae413f72c626e2601ba860aad44ddaa29cd
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-deploy-azure-file-sync-preview"></a>Cómo implementar Azure File Sync (versión preliminar)
Azure File Sync (versión preliminar) permite centralizar los recursos compartidos de archivos de su organización en Azure Files sin renunciar a la flexibilidad, el rendimiento y la compatibilidad de un servidor de archivos local. Para ello la transformación de los servidores Windows Server en una caché rápida de los recursos compartidos de Azure Files. Puede usar cualquier protocolo disponible en Windows Server para tener acceso a los datos localmente (incluidos SMB, NFS y FTPS) y puede tener tantas cachés según sea necesario en todo el mundo.

Se recomienda encarecidamente leer [Planeamiento de una implementación de Azure Files](storage-files-planning.md) y [Planeamiento de una implementación de Azure File Sync](storage-sync-files-planning.md) antes de seguir los pasos de esta guía.

## <a name="prerequisites"></a>Requisitos previos
* Una cuenta de almacenamiento y un recurso compartido de Azure Files en la misma región en la que quiere implementar Azure File Sync. Para más información, consulte:
    - [Disponibilidad en regiones](storage-sync-files-planning.md#region-availability) de Azure File Sync,
    - [Crear una cuenta de almacenamiento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obtener instrucciones paso a paso sobre cómo crear una cuenta de almacenamiento, y
    - [Creación de un recurso compartido de archivos](storage-how-to-create-file-share.md) para obtener instrucciones paso a paso sobre cómo crear un recurso compartido de archivos.
* Al menos un servidor de Windows Server o un clúster de Windows Server compatible para sincronizar con Azure File Sync. Vea [Interoperabilidad con Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability) para más información sobre las versiones compatibles de Windows Server.

## <a name="deploy-the-storage-sync-service"></a>Implementación del servicio de sincronización de almacenamiento 
El servicio de sincronización de almacenamiento es el recurso de Azure de nivel superior que representa a Azure File Sync. Para implementar un servicio de sincronización de almacenamiento, vaya a [Azure Portal](https://portal.azure.com/) y busque Azure File Sync. Después de seleccionar "Azure File Sync (versión preliminar)" en los resultados de búsqueda, seleccione "Crear" para abrir la pestaña "Implementar la sincronización del almacenamiento".

La hoja resultante pide la información siguiente:

- **Nombre**: un nombre único (por suscripción) para el servicio de sincronización de almacenamiento.
- **Suscripción**: la suscripción en la que se va a crear el servicio de sincronización de almacenamiento. Según la estrategia de configuración de la organización, puede tener acceso a una o más suscripciones. Una suscripción de Azure es el contenedor más básico para la facturación de cada servicio de nube (como Azure Files).
- **Grupo de recursos**: un grupo de recursos es un grupo lógico de recursos de Azure, como una cuenta de almacenamiento o un servicio de sincronización de almacenamiento. Puede crear un grupo de recursos o usar uno existente para Azure File Sync (se recomienda usar grupos de recursos como contenedores para aislar los recursos de manera lógica para la organización, por ejemplo, agrupar recursos del departamento de recursos humanos o recursos de un proyecto determinado).
- **Ubicación**: la región en la que querría implementar Azure File Sync. Solo las regiones admitidas están disponibles en esta lista.

Una vez que se ha completado el formulario "Implementar la sincronización del almacenamiento", haga clic en "Crear" para implementar el servicio de sincronización de almacenamiento.

## <a name="prepare-windows-servers-for-use-with-azure-file-sync"></a>Preparación de los servidores de Windows Server para su uso con Azure File Sync
Para cada servidor con el que piense usar Azure File Sync, incluidos los nodos de servidor de un clúster de conmutación por error, complete los pasos siguientes:

Para todos los servidores, incluidos los nodos de servidor de un clúster de conmutación por error, que piense usar con Azure File Sync, complete los pasos siguientes:

1. Deshabilite la configuración de seguridad mejorada de Internet Explorer. Esto solo es necesario para el registro inicial del servidor; se puede volver a habilitar una vez que se ha registrado el servidor.
    1. Abra el Administrador del servidor.
    2. Haga clic en **Servidor Local**:  
        !["Servidor local" en el lado izquierdo de la interfaz de usuario Administrador del servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. Seleccione el vínculo de **Configuración de seguridad mejorada de IE** en el panel secundario Propiedades:  
        !["Configuración de seguridad mejorada de IE" en la interfaz de usuario Administrador del servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. Seleccione **Desactivado** para Administradores y Usuarios en la ventana emergente de la configuración de seguridad mejorada de Internet Explorer:  
        ![Ventana emergente de la configuración de seguridad mejorada de Internet Explorer con "Desactivado" seleccionado](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. Asegúrese de ejecutar al menos PowerShell 5.1.\* (PowerShell 5.1 es el valor predeterminado en Windows Server 2016). Puede comprobar que está ejecutando PowerShell 5.1.\* si examina el valor de la propiedad PSVersion del objeto $PSVersionTable:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    - Si PSVersion es menor que 5.1.\*, como será el caso con la mayoría de las instalaciones de Windows Server 2012 R2, puede actualizar fácilmente si descarga e instala [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). El paquete adecuado que hay que descargar e instalar para Windows Server 2012 R2 es **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**.

3. [Instale y configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Siempre se recomienda usar la versión más reciente de los módulos de Azure PowerShell.

## <a name="install-the-azure-file-sync-agent"></a>Instalación del agente de Azure File Sync
El agente de Azure File Sync es un paquete descargable que permite la sincronización de un servidor de Windows Server con un recurso compartido de Azure Files. Puede descargar el agente en el [Centro de descarga de Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Una vez descargado, haga doble clic en el paquete MSI para iniciar la instalación del agente de Azure File Sync.

> [!Important]  
> Si piensa usar Azure File Sync con un clúster de conmutación por error, el agente de Azure File Sync se debe instalar en cada nodo del clúster.

El paquete de instalación del agente de Azure File Sync se debería instalar relativamente rápido y sin demasiados mensajes adicionales. Se recomienda lo siguiente:
- Dejar la ruta de instalación predeterminada (`C:\Program Files\Azure\StorageSyncAgent`) para simplificar la solución de problemas y el mantenimiento del servidor.
- Habilitar Microsoft Update para mantener actualizado Azure File Sync. Todas las actualizaciones, incluidas las actualizaciones y revisiones de características, del agente de Azure File Sync se realizan desde Microsoft Update. Siempre se recomienda elegir la actualización más reciente de Azure File Sync. Vea la [directiva de actualización de Azure File Sync](storage-sync-files-planning.md#azure-file-sync-agent-update-policy) para más información.

Al término de la instalación del agente de Azure File Sync, la interfaz de usuario Registro del servidor se inicia automáticamente. Vea la sección siguiente para aprender a registrar este servidor con Azure File Sync.

## <a name="register-windows-server-with-storage-sync-service"></a>Registro de un servidor de Windows Server con el servicio de sincronización de almacenamiento
El registro del servidor de Windows Server con un servicio de sincronización de almacenamiento establece una relación de confianza entre el servidor (o clúster) y el servicio de sincronización de almacenamiento. La interfaz de usuario Registro del servidor debe iniciarse automáticamente tras la instalación del agente de Azure File Sync, pero si no es así, puede abrirla manualmente desde su ubicación: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`. Una vez que la interfaz de usuario Registro del servidor esté abierta, haga clic en **Iniciar sesión** para comenzar.

Después de iniciar sesión se le pide la información siguiente:

![Captura de pantalla de la interfaz de usuario Registro del servidor](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Suscripción de Azure**: la suscripción que contiene el servicio de sincronización de almacenamiento (vea [Implementación del servicio de sincronización de almacenamiento](#deploy-the-storage-sync-service) arriba). 
- **Grupo de recursos**: el grupo de recursos que contiene el servicio de sincronización de almacenamiento.
- **Servicio de sincronización de almacenamiento**: el nombre del servicio de sincronización de almacenamiento con el que quiere registrar.

Una vez que haya seleccionado la información adecuada en las listas desplegables, haga clic en **Registrar** para completar el registro del servidor. Como parte del proceso de registro, se le solicita un inicio de sesión adicional.

## <a name="create-a-sync-group"></a>Creación de un grupo de sincronización
Un grupo de sincronización define la topología de sincronización de un conjunto de archivos. Los puntos de conexión dentro de un grupo de sincronización se mantienen sincronizados entre sí. Un grupo de sincronización debe contener al menos un punto de conexión de nube, que representa un recurso compartido de Azure Files, y un punto de conexión de servidor, que representa una ruta de acceso de un servidor de Windows Server. Para crear un grupo de sincronización, vaya al servicio de sincronización de almacenamiento en [Azure Portal](https://portal.azure.com/) y haga clic en **+ Grupo de sincronización**:

![Creación de un grupo de sincronización en Azure Portal](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

El panel resultante pide la información siguiente para crear un grupo de sincronización con un punto de conexión de nube:

- **Nombre del grupo de sincronización**: el nombre del grupo de sincronización que se va a crear. Este nombre debe ser único dentro del servicio de sincronización de almacenamiento, pero puede ser cualquier nombre que considere lógico.
- **Suscripción**: la suscripción en la que ha implementado el servicio de sincronización de almacenamiento en [Implementación del servicio de sincronización de almacenamiento](#deploy-the-storage-sync-service) arriba.
- **Cuenta de almacenamiento**: si hace clic en "Seleccionar cuenta de almacenamiento", se abre un panel adicional que permite seleccionar la cuenta de almacenamiento que contiene el recurso compartido de Azure Files con el que quiere sincronizar.
- **Recurso compartido de Azure Files**: el nombre del recurso compartido de Azure Files con el que quiere sincronizar.

Para agregar un punto de conexión de servidor, vaya al grupo de sincronización recién creado y haga clic en "Agregar punto de conexión del servidor".

![Adición de un nuevo punto de conexión de servidor al panel Grupo de sincronización](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

El panel resultante "Agregar punto de conexión del servidor" requiere la siguiente información para crear un punto de conexión de servidor:

- **Servidor registrado**: el nombre del servidor o el clúster en el que se va a crear el punto de conexión de servidor.
- **Ruta de acceso**: la ruta de acceso del servidor de Windows Server que se va a sincronizar como parte del grupo de sincronización.
- **Niveles de la nube**: un conmutador para habilitar o deshabilitar niveles de la nube, lo que permite que los archivos a los que se accede o que se usan con poca frecuencia se coloquen en niveles en Azure Files.
- **Espacio disponible del volumen**: la cantidad de espacio libre que se reserva en el volumen en el que reside el punto de conexión de servidor. Por ejemplo, si el espacio disponible del volumen se establece en el 50 % en un volumen con un único punto de conexión de servidor, casi la mitad de la cantidad de datos se coloca en niveles en Azure Files. Observe que con independencia de si la característica de niveles de la nube está habilitada, el recurso compartido de Azure Files siempre tiene una copia completa de los datos del grupo de sincronización.

Haga clic en "Crear" para agregar el punto de conexión de servidor. Los archivos ahora se mantienen sincronizados entre el recurso compartido de Azure Files y el servidor de Windows Server. 

> [!Important]  
> Puede realizar cambios en cualquier punto de conexión de servidor o la nube del grupo de sincronización y sincronizar los archivos con los demás puntos de conexión del grupo de sincronización. Si realiza algún cambio directamente en el punto de conexión de nube (recurso compartido de archivos de Azure Files), tenga en cuenta que un trabajo de detección de cambios de Azure File Sync deberá detectar primero esos cambios y dicho trabajo solo se inicia una sola vez cada 24 horas en el punto de conexión de nube. Consulte las [preguntas más frecuentes de Azure Files](storage-files-faq.md#afs-change-detection) para obtener más información.

## <a name="next-steps"></a>Pasos siguientes
- [Add/Remove an Azure File Sync Server Endpoint](storage-sync-files-server-endpoint.md) (Adición o eliminación de un punto de conexión de servidor de Azure File Sync)
- [Register/deregister a server with Azure File Sync](storage-sync-files-server-registration.md) (Registro y cancelación del registro de un servidor en Azure File Sync)