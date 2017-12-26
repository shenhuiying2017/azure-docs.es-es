---
title: "Azure Event Hubs Capture se habilita a través del portal | Microsoft Docs"
description: Habilite la funcionalidad de captura de Event Hubs mediante Azure Portal.
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 39fbdba404bda9383c9164dd1ecd9cb23bfb5cd7
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="enable-event-hubs-capture-using-the-azure-portal"></a>Habilitación de la funcionalidad de captura de Event Hubs mediante Azure Portal

Azure [Event Hubs Capture][capture-overview] le permite ofrecer automáticamente los datos de streaming de Event Hubs a una cuenta de [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) o de [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) que prefiera.

Puede configurar la funcionalidad de captura en el momento de creación del centro de eventos mediante [Azure Portal](https://portal.azure.com). O bien puede capturar los datos de un contenedor de Azure [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) o de una cuenta de [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Para más información, consulte [Introducción a Event Hubs Capture][capture-overview].

## <a name="capture-data-to-an-azure-storage-account"></a>Captura de datos en una cuenta de Azure Storage  

Al crear un centro de eventos, puede habilitar Capture haciendo clic en el botón **On** de la pantalla del portal **Creación de un centro de eventos**. Después, para especificar una cuenta de almacenamiento y un contenedor, haga clic en **Azure Storage** en el cuadro **Capture Provider** (Proveedor de Capture). Dado que la captura de Event Hubs utiliza la autenticación de servicio a servicio con el almacenamiento, no es necesario especificar una cadena de conexión de almacenamiento. El selector de recursos selecciona automáticamente el identificador URI del recurso para la cuenta de almacenamiento. Si se usa Azure Resource Manager, es preciso suministrar explícitamente dicho identificador URI como una cadena.

La ventana de tiempo predeterminada es cinco minutos. El valor mínimo es 1 y el máximo 15. La ventana **Tamaño** tiene un intervalo de 10-500 MB.

![][1]

## <a name="capture-data-to-an-azure-data-lake-store-account"></a>Captura de datos en una cuenta de Azure Data Lake Store

Para capturar datos en Azure Data Lake Store, cree una cuenta de Data Lake Store y un centro de eventos:

### <a name="create-an-azure-data-lake-store-account-and-folders"></a>Creación de una cuenta de Azure Data Lake Store y de carpetas

1. Cree una cuenta de Data Lake Store siguiendo las instrucciones que se describen en [Introducción al uso de Azure Portal por parte de Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md).
2. Siga las instrucciones de la sección [Asignación de permisos a Event Hubs](../data-lake-store/data-lake-store-archive-eventhub-capture.md#assign-permissions-to-event-hubs) para crear una carpeta dentro de la cuenta de Data Lake Store en la que desee capturar los datos de Event Hubs y asignar permisos a Event Hubs para que pueda escribir datos en su cuenta de Data Lake Store.  

### <a name="create-an-event-hub"></a>Creación de un centro de eventos

1. Tenga en cuenta que el centro de eventos debe estar en la misma suscripción de Azure que la instancia de Azure Data Lake Store que acaba de crear. Para crear el centro de eventos, haga clic en el botón **Activado** situado bajo **Capture** en la página del portal **Crear centro de eventos**. 
2. En la página del portal **Crear centro de eventos**, seleccione **Azure Data Lake Store** en el cuadro **Capture Provider** (Proveedor de Capture).
3. En **Seleccione Data Lake Store**, especifique la cuenta de Data Lake Store que creó anteriormente y en **Data Lake Path** (Ruta de acceso de Data Lake), escriba la ruta de acceso a la carpeta de datos que ha creado.

    ![][3]

## <a name="add-or-configure-capture-on-an-existing-event-hub"></a>Adición o configuración de Capture en un centro de eventos existente

Se puede configurar Capture en los centros de eventos existentes que se encuentran en los espacios de nombres de Event Hubs. Para habilitar Capture en un centro de eventos existente o para cambiar la configuración de Capture, haga clic en el espacio de nombres para cargar la pantalla de la introducción y, después, haga clic en el centro de eventos para el que desea habilitar o cambiar la configuración de Capture. Por último, haga clic en la opción **Capture** del lado izquierdo de la página abierta y después edite la configuración, tal como se muestra en las ilustraciones siguientes:

### <a name="azure-blob-storage"></a>Azure Blob Storage

![][2]

### <a name="azure-data-lake-store"></a>Almacén de Azure Data Lake

![][4]

[1]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture1.png
[2]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture2.png
[3]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture3.png
[4]: ./media/event-hubs-capture-enable-through-portal/event-hubs-capture4.png

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre Event Hubs Capture en el artículo [Introducción a Event Hubs Capture][capture-overview].
- La Event Hubs Capture también se puede configurar a través de las plantillas de Azure Resource Manager. Para más información, consulte [Habilitar Capture mediante la plantilla de Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md).
- [Introducción al uso de Azure Portal por parte de Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)

[capture-overview]: event-hubs-capture-overview.md