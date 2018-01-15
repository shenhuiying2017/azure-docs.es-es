---
title: "Autenticación entre servicios: API de REST con Data Lake Store mediante Azure Active Directory | Microsoft Docs"
description: "Aprenda a realizar la autenticación entre servicios con Data Lake Store mediante Azure Active Directory mediante la API de REST"
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
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 80934d3e5ded5c01e473f8450a3484d84c46e94e
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="service-to-service-authentication-with-data-lake-store-using-rest-api"></a>Autenticación entre servicios con Data Lake Store mediante la API de REST
> [!div class="op_single_selector"]
> * [Uso de Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Uso del SDK de .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Uso de Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Uso de la API de REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

En este artículo, aprenderá a usar la API de REST para realizar la autenticación entre servicios con Azure Data Lake Store. En el caso de la autenticación del usuario final con Data Lake Store mediante la API de REST, consulte [End-user authentication with Data Lake Store using REST API](data-lake-store-end-user-authenticate-rest-api.md) (Autenticación del usuario final con Data Lake Store mediante la API de REST).

## <a name="prerequisites"></a>Requisitos previos
* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Cree una aplicación "web" de Azure Active Directory**. Debe haber completado los pasos descritos en [Service-to-service authentication with Data Lake Store using Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md) (Autenticación entre servicios con Data Lake Store mediante Azure Active Directory).

## <a name="service-to-service-authentication"></a>Autenticación entre servicios
En este escenario, la aplicación proporciona sus propias credenciales para realizar las operaciones. Para ello, debe emitir una solicitud POST como la que se muestra en el siguiente fragmento de código: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

La salida de esta solicitud incluye un token de autorización (que se indica mediante `access-token` en la siguiente salida) que se pasa posteriormente con las llamadas de la API de REST. Guarde el token de autenticación en un archivo de texto; lo necesitará al realizar llamadas de REST a Data Lake Store.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

En este artículo se usa el enfoque **no interactivo** . Para más información sobre el enfoque no interactivo (llamadas de servicio a servicio), consulte el tema sobre [llamadas de servicio a servicio utilizando las credenciales del cliente](https://msdn.microsoft.com/library/azure/dn645543.aspx). 

## <a name="next-steps"></a>pasos siguientes
En este artículo, ha aprendido a usar la autenticación entre servicios con Azure Data Lake Store mediante la API de REST. Ahora puede consultar los siguientes artículos, que tratan acerca de cómo usar la API de REST con Azure Data Lake Store.

* [Operaciones de administración de cuentas en Data Lake Store mediante la API de REST](data-lake-store-get-started-rest-api.md)
* [Operaciones de datos en Data Lake Store mediante la API de REST](data-lake-store-data-operations-rest-api.md)

