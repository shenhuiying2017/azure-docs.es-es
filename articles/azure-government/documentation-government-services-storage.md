---
title: Almacenamiento de Azure Government | Microsoft Docs
description: "Esto proporciona una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para la administración de Azure"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/13/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 004860b319b7b32955415ac2d6c7308c32037848
ms.openlocfilehash: 36312ce195e931d174e00d54cab4cb071a85b618


---
# <a name="azure-government-storage"></a>Almacenamiento de Azure Government
## <a name="azure-storage"></a>Almacenamiento de Azure
Para más información sobre este servicio y cómo usarlo, consulte la [documentación pública de Azure Storage](../storage/index.md).

### <a name="variations"></a>Variaciones
Las direcciones URL de las cuentas de almacenamiento de Azure Government son diferentes:

| Tipo de servicio | Azure Public | Azure Government |
| --- | --- | --- |
| Almacenamiento de blobs |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| Almacenamiento de colas |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| Almacenamiento de tablas |*.table.core.windows.net |*.table.core.usgovcloudapi.net |

> [!NOTE]
> Todos los scrips y todo el código deben tener en cuenta los puntos de conexión adecuados.  Consulte [Configuración de las cadenas de conexión de Azure Storage](../storage/storage-configure-connection-string.md). 
>
>

Para más información sobre las API, consulte el <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx"> constructor de cuenta de almacenamiento de nube</a>.

El sufijo del punto de conexión que se usará en estas sobrecargas es core.usgovcloudapi.net.

### <a name="considerations"></a>Consideraciones
La siguiente información identifica el límite de Azure Government para Almacenamiento de Azure:

| Se permiten datos regulados o controlados | No se permiten datos regulados o controlados |
| --- | --- |
| Los datos especificados, almacenados y procesados dentro de un producto de Azure Storage pueden contener datos controlados para exportación. Autenticadores estáticos, como contraseñas y PIN de smartcard para el acceso a componentes de la plataforma Azure. Claves privadas de certificados que se usan para administrar los componentes de la plataforma Azure. Otros secretos o información de seguridad, como certificados, claves de cifrado, claves maestras y claves de almacenamiento almacenadas en servicios de Azure. |Los metadatos de Almacenamiento de Azure no pueden contener datos controlados para exportación. Estos metadatos incluyen todos los datos de configuración especificados al crear y mantener su producto de almacenamiento.  No inserte datos regulados o controlados en los siguientes campos: Grupos de recursos, Nombres de implementación, Nombres de recursos o Etiquetas de recursos. |

## <a name="premium-storage"></a>Premium Storage
Para más información sobre este servicio, consulte [Premium Storage: almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage/storage-premium-storage.md).

### <a name="variations"></a>Variaciones
Premium Storage está disponible con carácter general en USGov Virginia. Esto incluye máquinas virtuales de la serie DS.

### <a name="considerations"></a>Consideraciones
Las mismas consideraciones sobre los datos de almacenamiento mencionadas anteriormente se aplican a las cuentas Premium Storage.

## <a name="next-steps"></a>Pasos siguientes
Para obtener información complementaria y actualizaciones, suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government</a>.



<!--HONumber=Nov16_HO3-->


