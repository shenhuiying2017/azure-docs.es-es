---
title: Almacenamiento de Azure Government | Microsoft Docs
description: "Esto proporciona una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para la administración de Azure"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/22/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 831334f2b835d00aa3fd22292764b69e85d735a6
ms.openlocfilehash: a6b61df5884031eb5b53f983b834357bd23a622e


---
# <a name="azure-government-storage"></a>Almacenamiento de Azure Government
## <a name="azure-storage"></a>Almacenamiento de Azure
Para más información sobre este servicio y cómo usarlo, consulte la [documentación pública de Azure Storage](../storage/index.md).

### <a name="storage-service-availability-by-azure-government-region"></a>Disponibilidad del servicio de almacenamiento por región de Azure Government

| Servicio | USGov Virginia | USGov Iowa | Notas
| --- | --- | --- | --- |
| [Blob Storage] (../storage/storage-introduction.md#blob-storage) |GA |GA |
| [Table Storage] (../storage/storage-introduction.md#table-storage) |GA  |GA |
| [Queue Storage] (../storage/storage-introduction.md#queue-storage) |GA | GA |
| [File Storage] (../storage/storage-introduction.md#file-storage) |GA |GA |
| [Hot/Cool Blob Storage] (../storage/storage-blob-storage-tiers.md) |N/D |N/D |
| [Storage Service Encryption] (../storage/storage-service-encryption.md) |GA |GA |
| [Premium Storage] (../storage/storage-premium-storage.md) |GA |N/D | Incluye máquinas virtuales de la serie DS. |
| [Blob Import/Export] (../storage/storage-import-export-service.md) |GA |GA |
| [StorSimple] (../storsimple/storsimple-ova-overview.md) |GA |GA |

### <a name="variations"></a>Variaciones
Las direcciones URL de las cuentas de almacenamiento de Azure Government son diferentes:

| Tipo de servicio | Azure Public | Azure Government |
| --- | --- | --- |
| Almacenamiento de blobs |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| Almacenamiento de colas |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| Almacenamiento de tablas |*.table.core.windows.net |*.table.core.usgovcloudapi.net |
| File Storage |*.file.core.windows.net |*.file.core.usgovcloudapi.net | 

> [!NOTE]
> Todos los scrips y todo el código deben tener en cuenta los puntos de conexión adecuados.  Consulte [Configuración de las cadenas de conexión de Azure Storage](../storage/storage-configure-connection-string.md). 
>
>

Para más información sobre las API, consulte el <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx"> constructor de cuenta de almacenamiento de nube</a>.

El sufijo del punto de conexión que se usará en estas sobrecargas es core.usgovcloudapi.net.

> [!NOTE]
> El [Explorador de Microsoft Azure Storage] (.. / vs-azure-tools-storage-manage-with-storage-explorer.md) actualmente no es compatible con la [Conexión a una suscripción de Azure] (.. / vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-an-azure-subscription) mediante la adición de una cuenta de Azure Government. Use otros métodos de [Conexión a una cuenta de almacenamiento] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service).
Cuando realice la [Asociación de una cuenta de almacenamiento externo] (.. / vs-azure-tools-storage-manage-with-storage-explorer.md#attach-to-an-external-storage-account), en el **paso 3**, seleccione **Otros (especificar a continuación)** como dominio de punto de conexión de almacenamiento y especifique **core.usgovcloudapi.net** para Azure Government.
>
>

> [!NOTE]
> Si se devuelve el error 53 "No se ha encontrado la ruta de acceso de la red." al realizar el [Montaje del recurso compartido de archivos] (.. / storage/storage-dotnet-how-to-use-files.md#mount-the-file-share). puede deberse a que el firewall haya bloquea el puerto de salida. Intentar montar el recurso compartido de archivos en la máquina virtual que se encuentre en la misma suscripción de Azure que la cuenta de almacenamiento.
>
>

> [!NOTE]
> Al implementar el servicio StorSimple Manager, utilice las direcciones URL https://portal.azure.us/ y https://manage.windowsazure.us/ para Azure Portal y el portal clásico, respectivamente. Para obtener instrucciones de implementación para la matriz virtual de StorSimple, consulte [Requisitos del sistema de la matriz virtual de StorSimple] (../storsimple/storsimple-ova-system-requirements.md) y para la serie StorSimple 8000, consulte [Software de StorSimple, alta disponibilidad y requisitos de red] (../storsimple/storsimple-system-requirements.md) y vaya a la sección de implementación del menú de navegación de la izquierda. Para obtener documentación de StorSimple general, vea [Documentación de StorSimple?] (../storsimple/index.md).
>
>

### <a name="considerations"></a>Consideraciones
La siguiente información identifica el límite de Azure Government para Almacenamiento de Azure:

| Se permiten datos regulados o controlados | No se permiten datos regulados o controlados |
| --- | --- |
| Los datos especificados, almacenados y procesados dentro de un producto de Azure Storage pueden contener datos controlados para exportación. Autenticadores estáticos, como contraseñas y PIN de smartcard para el acceso a componentes de la plataforma Azure. Claves privadas de certificados que se usan para administrar los componentes de la plataforma Azure. Otros secretos o información de seguridad, como certificados, claves de cifrado, claves maestras y claves de almacenamiento almacenadas en servicios de Azure. |Los metadatos de Almacenamiento de Azure no pueden contener datos controlados para exportación. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener su producto de almacenamiento.  No inserte datos regulados o controlados en los siguientes campos: Grupos de recursos, Nombres de implementación, Nombres de recursos o Etiquetas de recursos. |

## <a name="next-steps"></a>Pasos siguientes
Para obtener información complementaria y actualizaciones, suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government</a>.



<!--HONumber=Feb17_HO2-->


