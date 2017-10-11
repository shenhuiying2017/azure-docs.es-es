---
title: Importar una API en Azure API Management | Microsoft Docs
description: "Obtenga información sobre cómo importar una API y sus operaciones en Azure API Management."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 40398b0a-ac2c-43f0-89e1-07e4abbf502f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: c851b88fc1067e65044266d07775717c028e75d9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>Importación de la definición de una API con operaciones en Administración de API de Azure
En Administración de API se pueden crear nuevas API y agregar las operaciones manualmente o se puede importar la API junto con las operaciones en un solo paso.

Las API y sus operaciones se pueden importar con los siguientes formatos.

* WADL
* Swagger

En esta guía se muestra cómo crear una API e importar sus operaciones en un solo paso. Para obtener más información sobre la creación manual de una API y la incorporación de operaciones, consulte [Creación de API][How to create APIs] e [Incorporación de operaciones a una API][How to add operations to an API].

## <a name="import-api"> </a>Importación de una API
Las API se crean y se configuran en el portal del publicador. Para obtener acceso al portal del publicador, haga clic en el **portal del publicador** en Azure Portal para el servicio API Management. Si aún no ha creado ninguna instancia del servicio de API Management, consulte [Creación de una instancia del servicio API Management][Create an API Management service instance] en el tutorial [Introducción a Azure API Management][Get started with Azure API Management].

![Portal del publicador][api-management-management-console]

Haga clic en **API** en el menú **API Management** de la izquierda y haga clic en **Importar API**.

![Importar API][api-management-import-apis]

La ventana **Importar API** tiene tres pestañas que corresponden a tres formas de proporcionar la especificación de API.

* **Desde el portapapeles** permite pegar la especificación de API en el cuadro de texto designado para ello.
* **Desde el archivo** permite buscar el archivo que contiene la especificación de API y seleccionarlo.
* **Desde URL** permite suministrar la dirección URL de la especificación para la API.

![Import API format][api-management-import-api-clipboard]

Después de proporcionar la especificación de API, use los botones de radio de la derecha para indicar el formato de especificación. Se admiten los siguientes formatos.

* WADL
* Swagger

A continuación, especifique un **Sufijo de dirección URL API web**. Este se anexa a la dirección URL base del servicio Administración de API. La dirección URL base es común para todas las API hospedadas en cada instancia de un servicio Administración de API. Administración de API distingue las API por su sufijo, por lo que el sufijo debe ser único para cada API de una instancia específica de un servicio de Administración de API.

Una vez especificados todos los valores, haga clic en **Guardar** para crear la API y las operaciones asociadas. 

> [!NOTE]
> Para ver un tutorial de la importación de una API de calculadora básica en formato Swagger, consulte [Administración de su primera API en Administración de API de Azure](api-management-get-started.md).
> 
> 

## <a name="export-api"> </a> Exportación de una API
Además de importar nuevas API, puede exportar las definiciones de sus API desde el portal del publicador. Para ello, haga clic en **Exportar API** en la **pestaña Resumen** de la **API**.

![Export API][api-management-export-api]

Las API se pueden exportar con WADL o Swagger. Seleccione el formato que desee, haga clic en **Guardar**y elija la ubicación en la que desea guardar el archivo.

![Export API format][api-management-export-api-format]

## <a name="next-steps"> </a>Pasos siguientes
Una vez creada una API e importadas las operaciones, se pueden revisar y definir las configuraciones adicionales, agregar la API a un producto y publicarlo para ponerlo a disposición de los desarrolladores. Para obtener más información, consulte las siguientes guías.

* [Definición de la configuración de la API][How to configure API settings]
* [Creación y publicación de un producto][How to create and publish a product]

[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create APIs]: api-management-howto-create-apis.md
[How to configure API settings]: api-management-howto-create-apis.md#configure-api-settings
