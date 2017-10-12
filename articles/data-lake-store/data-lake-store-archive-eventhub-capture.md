---
title: Capturar datos de Event Hubs en Azure Data Lake Store | Microsoft Docs
description: Usar Azure Data Lake Store para capturar datos de Event Hubs
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: a9e69576958ae96d22a4eb03d0df429f0b307298
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-data-lake-store-to-capture-data-from-event-hubs"></a>Usar Azure Data Lake Store para capturar datos de Event Hubs

Obtenga información sobre cómo usar Azure Data Lake Store para capturar datos recibidos por Azure Event Hubs.

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Una cuenta de Almacén de Azure Data Lake**. Para obtener instrucciones sobre cómo crear una, consulte la [introducción a Azure Data Lake Store](data-lake-store-get-started-portal.md).

*  **Un espacio de nombres de Event Hubs**. Para obtener instrucciones, consulte [Creación de un espacio de nombres de Event Hubs](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Asegúrese de que la cuenta de Data Lake Store y el espacio de nombres de Event Hubs se encuentran en la misma suscripción de Azure.


## <a name="assign-permissions-to-event-hubs"></a>Asignar permisos a Event Hubs

En esta sección, creará una carpeta en la cuenta en que quiere capturar los datos de Event Hubs. También asignará permisos a Event Hubs para que pueda escribir datos en una cuenta de Data Lake Store. 

1. Abra la cuenta de Data Lake Store en que quiere capturar los datos de Event Hubs y después haga clic en **Explorador de datos**.

    ![Explorador de datos de Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Explorador de datos de Data Lake Store")

2.  Haga clic en **Nueva carpeta** y después escriba un nombre para la carpeta en que quiere capturar los datos.

    ![Crear una carpeta en Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Crear una carpeta en Data Lake Store")

3. Asigne permisos en la raíz de Data Lake Store. 

    a. Haga clic en **Explorador de datos**, seleccione la raíz de la cuenta de Data Lake Store y después haga clic en **Acceso**.

    ![Asignar permisos a la raíz de Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Asignar permisos a la raíz de Data Lake Store")

    b. En **Acceso**, haga clic en **Agregar**, en **Seleccionar usuario o grupo** y después busque `Microsoft.EventHubs`. 

    ![Asignar permisos a la raíz de Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Asignar permisos a la raíz de Data Lake Store")
    
    Haga clic en **Seleccionar**.

    c. En **Asignar permisos**, haga clic en **Seleccionar permisos**. Establezca **Permisos** en **Ejecutar**. Establezca **Agregar a** en **Esta carpeta y todos los elementos secundarios**. Establezca **Agregar como** en **Una entrada de permiso de acceso y una entrada de permiso predeterminado**.

    ![Asignar permisos a la raíz de Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Asignar permisos a la raíz de Data Lake Store")

    Haga clic en **Aceptar**.

4. Asigne permisos a la carpeta en la cuenta de Data Lake Store en que quiere capturar los datos.

    a. Haga clic en **Explorador de datos**, seleccione la carpeta de la cuenta de Data Lake Store y después haga clic en **Acceso**.

    ![Asignar permisos a la carpeta de Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Asignar permisos a la carpeta de Data Lake Store")

    b. En **Acceso**, haga clic en **Agregar**, en **Seleccionar usuario o grupo** y después busque `Microsoft.EventHubs`. 

    ![Asignar permisos a la carpeta de Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Asignar permisos a la carpeta de Data Lake Store")
    
    Haga clic en **Seleccionar**.

    c. En **Asignar permisos**, haga clic en **Seleccionar permisos**. Establezca **Permisos** en **Leer, escribir** y **ejecutar**. Establezca **Agregar a** en **Esta carpeta y todos los elementos secundarios**. Por último, establezca **Agregar como** en **Una entrada de permiso de acceso y una entrada de permiso predeterminado**.

    ![Asignar permisos a la carpeta de Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Asignar permisos a la carpeta de Data Lake Store")
    
    Haga clic en **Aceptar**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-store"></a>Configurar Event Hubs para capturar datos en Data Lake Store

En esta sección, creará un centro de eventos en un espacio de nombres de Event Hubs. También configurará el centro de eventos para capturar los datos en una cuenta de Azure Data Lake Store. En esta sección, se da por supuesto que ya ha creado un espacio de nombres de Event Hubs.

2. En el panel **Introducción** del espacio de nombres de Event Hubs, haga clic en **+ Centro de eventos**.

    ![Crear centro de eventos](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Crear centro de eventos")

3. Proporcione los siguientes valores para configurar Event Hubs para capturar datos en Data Lake Store.

    ![Crear centro de eventos](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Crear centro de eventos")

    a. Especifique un nombre para el centro de eventos.
    
    b. Para este tutorial, establezca **Recuento de particiones** y **Retención de mensajes** en los valores predeterminados.
    
    c. Establezca **Capture** en **Activado**. Establezca la **Ventana de tiempo** (la frecuencia de captura) y **Ajustar tamaño de la ventana** (tamaño de los datos que se capturarán). 
    
    d. En **Capture Provider** (Proveedor de Capture), seleccione **Azure Data Lake Store** y después seleccione el Data Lake Store que ha creado anteriormente. En **Data Lake Path** (Ruta de acceso de Data Lake), escriba el nombre de la carpeta que ha creado en la cuenta de Data Lake Store. Basta con proporcionar la ruta de acceso relativa a la carpeta.

    e. Deje **Formatos de nombre de archivo de Capture de ejemplo** en el valor predeterminado. Esta opción rige la estructura de carpetas que se crea en la carpeta de captura.

    f. Haga clic en **Crear**.

## <a name="test-the-setup"></a>Probar la configuración

Ahora puede enviar datos a Azure Event Hubs para probar la solución. Siga las instrucciones de [Envío de eventos a Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Cuando empiece a enviar los datos, los verá reflejados en Data Lake Store con la estructura de carpetas que ha especificado. Por ejemplo, verá una estructura de carpetas, tal y como se muestra en la siguiente captura de pantalla, en Data Lake Store.

![Datos de ejemplo del centro de eventos en Data Lake Store](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Datos de ejemplo del centro de eventos en Data Lake Store")

> [!NOTE]
> Incluso si no tiene mensajes que lleguen a Event Hubs, este escribe archivos vacíos solo con los encabezados en la cuenta de Data Lake Store. Los archivos se escriben en el mismo intervalo de tiempo que ha proporcionado al crear los centros de eventos.
> 
>

## <a name="analyze-data-in-data-lake-store"></a>Análisis de datos en el Almacén de Data Lake

Una vez que los datos están en Data Lake Store, puede ejecutar trabajos analíticos para procesar y estudiar los datos. Vea [USQL Avro Example](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) (Ejemplo de Avro de USQL) sobre cómo hacer esto con Azure Data Lake Analytics.
  

## <a name="see-also"></a>Otras referencias
* [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
* [Copiar datos de los blobs de almacenamiento de Azure en el Almacén Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
