---
title: "Creación de un trabajo de importación para Azure Import/Export | Microsoft Docs"
description: "Obtenga información sobre cómo crear un trabajo de importación para el servicio Microsoft Azure Import/Export."
author: muralikk
manager: syadav
editor: syadav
services: storage
documentationcenter: 
ms.assetid: 8b886e83-6148-4149-9d0f-5d48ec822475
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: d373d2a0e601f2796719fc5efb8761f276ab24d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="creating-an-import-job-for-the-azure-importexport-service"></a>Creación de un trabajo de importación para el servicio Azure Import/Export

Para crear un trabajo de importación para el servicio Microsoft Azure Import/Export con la API de REST, debe seguir estos pasos:

-   Preparar las unidades con la herramienta Azure Import/Export.

-   Obtener la ubicación a la que se va a enviar la unidad.

-   Crear el trabajo de importación.

-   Enviar sus unidades de disco a Microsoft a través de un servicio de transporte admitido.

-   Actualizar el trabajo de importación con la información de envío.

 Vea [Uso del servicio Microsoft Azure Import/Export para transferir datos a Blob Storage](storage-import-export-service.md) para leer una introducción al servicio Import/Export y ver un tutorial en el que se demuestra cómo usar [Azure Portal](https://portal.azure.com/) para crear y administrar trabajos de importación y exportación.

## <a name="preparing-drives-with-the-azure-importexport-tool"></a>Preparación de las unidades con la herramienta Azure Import/Export

Los pasos para preparar las unidades para un trabajo de importación son los mismos si crea el trabajo a través del portal o a través de la API de REST.

A continuación se muestra una breve descripción general de cómo preparar la unidad. Vea [Azure Import-ExportTool Reference](storage-import-export-tool-how-to-v1.md) (Referencia de la herramienta Azure Import/Export) para obtener las instrucciones completas. Puede descargar la herramienta Azure Import/Export [aquí](http://go.microsoft.com/fwlink/?LinkID=301900).

La preparación de la unidad conlleva:

-   Identificar los datos que se van a importar.

-   Identificar los blobs de destino en Microsoft Azure Storage.

-   Usar la herramienta Azure Import/Export para copiar los datos en una o varias unidades de disco duro.

 La herramienta Azure Import/Export también genera un archivo de manifiesto para cada una de las unidades de disco mientras se prepara. Un archivo de manifiesto contiene:

-   Una enumeración de todos los archivos pensados para la carga y las asignaciones de estos archivos en los blobs.

-   Sumas de comprobación de los segmentos de cada archivo.

-   Información sobre los metadatos y las propiedades para asociar a cada blob.

-   Una lista de la acción que se realizará si un blob que se va a cargar tiene el mismo nombre que un blob existente en el contenedor. Las opciones posibles son: a) sobrescribir el blob con el archivo, b) conservar el blob existente y omitir la carga del archivo, c) anexar un sufijo al nombre para que no entre en conflicto con otros archivos.

## <a name="obtaining-your-shipping-location"></a>Obtención de la ubicación de envío

Antes de crear un trabajo de importación, necesita obtener un nombre de la ubicación de envío y una dirección mediante una llamada a la operación [List Locations](/rest/api/storageimportexport/listlocations). `List Locations` devolverá una lista de ubicaciones y sus direcciones de correo. Puede seleccionar una ubicación de la lista devuelta y enviar las unidades de disco duro a esa dirección. También puede usar la operación `Get Location` para obtener directamente la dirección de envío para una ubicación específica.

 Siga los pasos siguientes para obtener la ubicación de envío:

-   Identifique el nombre de la ubicación de la cuenta de almacenamiento. Este valor puede encontrarse en el campo **Ubicación** del **Panel** de la cuenta de almacenamiento de Azure Portal o puede consultarse mediante el uso de la operación [Get Storage Account Properties](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties) de Service Management API.

-   Recupere la ubicación que está disponible para procesar esta cuenta de almacenamiento mediante una llamada a la operación `Get Location`.

-   Si la propiedad `AlternateLocations` de la ubicación contiene la propia ubicación, entonces es apropiado usar esta ubicación. De lo contrario, vuelva a llamar a la operación `Get Location` con una de las ubicaciones alternativas. La ubicación original podría estar cerrada temporalmente para el mantenimiento.

## <a name="creating-the-import-job"></a>Creación del trabajo de importación
Para crear el trabajo de importación, llame a la operación [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate). Tiene que proporcionar la siguiente información:

-   Un nombre para el trabajo.

-   El nombre de la cuenta de almacenamiento.

-   El nombre de la ubicación envío, obtenido en el paso anterior.

-   Un tipo de trabajo (importar).

-   El remite al que deben enviarse las unidades de disco después de que se ha completado el trabajo de importación.

-   La lista de unidades del trabajo. Para cada unidad, debe incluir la siguiente información que se obtuvo durante el paso de preparación de la unidad:

    -   El Id. de unidad

    -   La clave de BitLocker

    -   La ruta de acceso relativa del archivo de manifiesto de la unidad de disco duro

    -   Hash MD5 del archivo de manifiesto codificado en Base16

## <a name="shipping-your-drives"></a>Envío de las unidades de disco
Debe enviar las unidades de disco a la dirección que ha obtenido en el paso anterior, y debe proporcionar al servicio Import/Export el número de seguimiento del paquete.

> [!NOTE]
>  Debe enviar las unidades de disco a través de un servicio de transporte admitido, que proporcionará un número de seguimiento del paquete.

## <a name="updating-the-import-job-with-your-shipping-information"></a>Actualización del trabajo de importación con la información de envío
Cuando tenga el número de seguimiento, llame a la operación [Update Job Properties](/api/storageimportexport/jobs#Jobs_Update) para actualizar el nombre del transportista, el número de seguimiento del trabajo y el número de cuenta del transportista para el envío de devolución. También puede especificar el número de unidades y la fecha de envío.

## <a name="next-steps"></a>Pasos siguientes

* [Uso de la API de REST del servicio Azure Import/Export](storage-import-export-using-the-rest-api.md)
