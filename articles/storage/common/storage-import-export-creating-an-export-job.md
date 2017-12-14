---
title: "Creación de un trabajo de exportación para Azure Import/Export | Microsoft Docs"
description: "Obtenga información sobre cómo crear un trabajo de exportación para el servicio Microsoft Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 613d480b-a8ef-4b28-8f54-54174d59b3f4
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 70f74b685f4d5b902b6f6780f7c1de4d357164b8
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="creating-an-export-job-for-the-azure-importexport-service"></a>Creación de un trabajo de exportación para el servicio Azure Import/Export
Para crear un trabajo de exportación para el servicio Microsoft Azure Import/Export con la API de REST, debe seguir estos pasos:

-   Seleccionar los blobs que va a exportar.

-   Obtener una ubicación de envío.

-   Crear el trabajo de exportación.

-   Enviar sus unidades de disco vacías a Microsoft a través de un servicio de transporte admitido.

-   Actualizar el trabajo de exportación con la información del paquete.

-   Recibir las unidades de disco de Microsoft.

 Vea [Uso del servicio Microsoft Azure Import/Export para transferir datos a Blob Storage](storage-import-export-service.md) para leer una introducción al servicio Import/Export y ver un tutorial en el que se demuestra cómo usar [Azure Portal](https://portal.azure.com/) para crear y administrar trabajos de importación y exportación.

## <a name="selecting-blobs-to-export"></a>Selección de los blobs que va a exportar
 Para crear un trabajo de exportación, debe proporcionar una lista de blobs que desea exportar de la cuenta de almacenamiento. Hay varias maneras de seleccionar los blobs que se van a exportar:

-   Puede usar una ruta de acceso relativa del blob para seleccionar un único blob y todas sus instantáneas.

-   Puede usar una ruta de acceso relativa del blob para seleccionar un único blob excluyendo sus instantáneas.

-   Puede usar una ruta de acceso relativa del blob y un tiempo de instantánea para seleccionar una sola instantánea.

-   Puede utilizar un prefijo de blob para seleccionar todos los blobs e instantáneas con el prefijo especificado.

-   Puede exportar todos los blobs e instantáneas en la cuenta de almacenamiento.

 Para obtener más información sobre cómo especificar los blobs que va a exportar, vea la operación [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate).

## <a name="obtaining-your-shipping-location"></a>Obtención de la ubicación de envío
Antes de crear un trabajo de exportación, necesita obtener un nombre de la ubicación de envío y una dirección mediante una llamada a la operación [Get Location](https://portal.azure.com) o [List Locations](/rest/api/storageimportexport/listlocations). `List Locations` devolverá una lista de ubicaciones y sus direcciones de correo. Puede seleccionar una ubicación de la lista devuelta y enviar las unidades de disco duro a esa dirección. También puede usar la operación `Get Location` para obtener directamente la dirección de envío para una ubicación específica.

Siga los pasos siguientes para obtener la ubicación de envío:

-   Identifique el nombre de la ubicación de la cuenta de almacenamiento. Este valor puede encontrarse en el campo **Ubicación** del **Panel** de la cuenta de almacenamiento de Azure Portal o puede consultarse mediante el uso de la operación [Get Storage Account Properties](/rest/api/storagerp/storageaccounts#StorageAccounts_GetProperties) de Service Management API.

-   Recupere la ubicación que está disponible para procesar esta cuenta de almacenamiento mediante una llamada a la operación `Get Location`.

-   Si la propiedad `AlternateLocations` de la ubicación contiene la propia ubicación, entonces es apropiado usar esta ubicación. De lo contrario, vuelva a llamar a la operación `Get Location` con una de las ubicaciones alternativas. La ubicación original podría estar cerrada temporalmente para el mantenimiento.

## <a name="creating-the-export-job"></a>Creación del trabajo de exportación
 Para crear el trabajo de exportación, llame a la operación [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate). Tiene que proporcionar la siguiente información:

-   Un nombre para el trabajo.

-   El nombre de la cuenta de almacenamiento.

-   El nombre de la ubicación envío, obtenido en el paso anterior.

-   Un tipo de trabajo (exportar).

-   El remite al que deben enviarse las unidades de disco después de que se ha completado el trabajo de exportación.

-   La lista de blobs (o prefijos de blob) que se exportarán.

## <a name="shipping-your-drives"></a>Envío de las unidades de disco
 A continuación, use la herramienta Azure Import/Export para determinar el número de unidades que necesita enviar, en función de los blobs seleccionados para la exportación y del tamaño de la unidad de disco. Vea [Referencia de la herramienta Azure Import-Export](storage-import-export-tool-how-to-v1.md) para obtener información detallada.

 Introduzca todas las unidades de disco en un único paquete y envíelo a la dirección obtenida en el paso anterior. Anote el número de seguimiento del paquete para el paso siguiente.

> [!NOTE]
>  Debe enviar las unidades de disco a través de un servicio de transporte admitido, que proporcionará un número de seguimiento del paquete.

## <a name="updating-the-export-job-with-your-package-information"></a>Actualización del trabajo de exportación con la información del paquete
 Cuando tenga el número de seguimiento, llame a la operación [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) para actualizar el nombre del transportista y el número de seguimiento del trabajo. También puede especificar el número de unidades, el remite y la fecha de envío.

## <a name="receiving-the-package"></a>Recepción del paquete
 Una vez procesado el trabajo de exportación, las unidades de disco se le devolverán con los datos cifrados. Puede recuperar la clave de BitLocker de cada una de las unidades de disco mediante una llamada a la operación [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get). A continuación, puede desbloquear la unidad con la clave. El archivo de manifiesto de cada unidad contiene la lista de archivos de la unidad, así como la dirección del blob original de cada archivo.

## <a name="next-steps"></a>Pasos siguientes

* [Uso de la API de REST del servicio Azure Import/Export](storage-import-export-using-the-rest-api.md)
