---
title: "API de REST: operaciones de administración de cuentas en Azure Data Lake Store | Microsoft Docs"
description: "Con Azure Data Lake Store y la API de REST WebHDFS se realizan operaciones de administración de cuentas en Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 5fafde870a01a6ceb5e86f7b00b0ca11b748c68a
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="account-management-operations-on-azure-data-lake-store-using-rest-api"></a>Operaciones de administración de cuentas en Azure Data Lake Store con la API de REST
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [API DE REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Con este artículo aprenderá a realizar operaciones de administración de cuentas en Data Lake Store con la API de REST. Las operaciones de administración de cuentas incluyen,por ejemplo, la creación o la eliminación de cuentas de Data Lake Store. Para instrucciones sobre cómo realizar operaciones de sistema de archivos en Data Lake Store con la API de REST, consulte [Operaciones de sistema de archivos en Azure Data Lake Store con la API de REST](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Requisitos previos
* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](http://curl.haxx.se/)**. En este artículo se usa cURL para demostrar cómo realizar llamadas de la API de REST en una cuenta de Almacén de Data Lake.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>¿Cómo se puede autenticar mediante Azure Active Directory?
Puede usar dos enfoques para autenticar con Azure Active Directory.

* Para la autenticación del usuario final para la aplicación (interactiva), consulte el artículo sobre la [autenticación del usuario final con Data Lake Store mediante el SDK de .NET](data-lake-store-end-user-authenticate-rest-api.md).
* Para la autenticación entre servicios para la aplicación (no interactiva), consulte el artículo sobre la [autenticación entre servicios con Data Lake Store mediante el SDK de .NET](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-store-account"></a>Crear una cuenta de Almacén de Data Lake
Esta operación se basa en la llamada de la API de REST que se define [aquí](https://msdn.microsoft.com/library/mt694078.aspx).

Use el siguiente comando cURL. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

En el comando anterior, reemplace \<`REDACTED`\> por el token de autorización que recuperó anteriormente. La carga útil de la solicitud de este comando se encuentra en el archivo **input.json** que se proporciona para el parámetro `-d` anterior. El contenido del archivo input.json es similar al siguiente fragmento de código:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-store-account"></a>Eliminar una cuenta del Almacén de Data Lake
Esta operación se basa en la llamada de la API de REST que se define [aquí](https://msdn.microsoft.com/library/mt694075.aspx).

Use el siguiente comando cURL para eliminar la cuenta del Almacén de Data Lake. Reemplace **\<yourstorename>** por el nombre de Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Debe ver algo parecido al siguiente fragmento de código:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>pasos siguientes
* [Operaciones de sistema de archivos en Azure Data Lake Store con la API de REST](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Otras referencias
* [Azure Data Lake Store REST API Reference](https://docs.microsoft.com/rest/api/datalakestore/) (Referencia sobre la API de REST de Azure Data Lake)
* [Abrir aplicaciones Big Data de origen que funcionan con el Almacén de Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)

