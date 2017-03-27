---
title: "Restricciones y problemas conocidos en la importación de la API Azure API Management | Microsoft Docs"
description: "Detalles de los problemas conocidos y las restricciones en la importación en Azure API Management con los formatos de Open API, WSDL o WADL."
services: api-management
documentationcenter: 
author: mattfarm
manager: vlvinogr
editor: 
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: apipm
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: f941f87019a667deba5ec3e5cd054d04318689db
ms.lasthandoff: 03/10/2017


---
# <a name="api-import-restrictions-and-known-issues"></a>Restricciones de importación de API y problemas conocidos
## <a name="about-this-list"></a>Acerca de esta lista
Aunque hacemos todo lo que está en nuestras manos para garantizar que la importación de su API en Azure API Management se realiza de forma tan transparente y sencilla como es posible, en ocasiones imponemos restricciones o identificamos problemas que deberán corregirse para poder importar correctamente. Este artículo documentan todo esto y está organizado por el formato de importación de la API.

## <a name="open-api"> </a>Open API/Swagger
En general, si recibe errores al importar el documento de Open API, asegúrese de que lo ha validado, ya sea mediante el diseñador en el nuevo Azure Portal (Diseño - Front-End - Open API Specification Editor (Editor de especificaciones de Open API)) o con una herramienta de terceros como <a href="http://www.swagger.io">Swagger Editor</a>.

* **Nombre de host**: Se requiere un atributo de nombre de host.
* **Ruta de acceso base**: Se requiere un atributo de ruta de acceso base.
* **Esquemas**: Se requiere una matriz de esquema. 

## <a name="wsdl"> </a>WSDL
Los archivos WSDL se utilizan para generar API de paso a través de SOAP, o para servir como back-end de una API de SOAP a REST.

* **WSDL:Import**: Actualmente no se admiten API que usen este atributo. Los clientes deben combinar los elementos importados en un documento.
* Los **mensajes con varias partes** no se admiten actualmente.
* Los servicios de SOAP **WCF wsHttpBinding** creados con Windows Communication Foundation deben utilizar basicHttpBinding - wsHttpBinding.
* **MTOM**: Los servicios que usan MTOM <em>pueden</em> funcionar. No se ofrece soporte técnico oficial en este momento.
* No se admiten los tipos de **recursión** que se definen de forma recursiva (por ejemplo, una referencia a una matriz de ellos mismos).

## <a name="wadl"> </a>WADL
No hay ningún problema de importación WADL conocido actualmente.


[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md

