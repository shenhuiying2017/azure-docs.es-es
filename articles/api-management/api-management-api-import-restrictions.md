---
title: Restricciones y problemas conocidos en la importación de la API Azure API Management | Microsoft Docs
description: Detalles de los problemas conocidos y las restricciones en la importación en Azure API Management con los formatos de Open API, WSDL o WADL.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: ab4bc4024248675c6325159b5507add1274addc9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="api-import-restrictions-and-known-issues"></a>Restricciones de importación de API y problemas conocidos
## <a name="about-this-list"></a>Acerca de esta lista
Al importar una API, podría encontrarse con algunas restricciones o identificar problemas que es necesario corregir antes de realizar esta operación. En este artículo se documenta todo esto, organizado por el formato de importación de la API.

## <a name="open-api"></a>Open API/Swagger
Si recibe errores al importar el documento de Open API, asegúrese de que lo ha validado, ya sea mediante el diseñador en Azure Portal (Diseño - Front-End - Open API Specification Editor [Editor de especificaciones de Open API]) o con una herramienta de terceros como <a href="http://www.swagger.io">Swagger Editor</a>.

* Se admite solo el formato JSON para OpenAPI.
* Los esquemas a los que se hace referencia con propiedades **$ref** no pueden contener otras propiedades **$ref**.
* Los punteros de **$ref** no pueden hacer referencia a archivos externos.
* **x-ms-paths** y **x-servers** son las únicas extensiones admitidas.
* Las extensiones personalizadas se omiten durante la importación y no se guardan ni conservan para la exportación.

> [!IMPORTANT]
> Consulte este [documento](https://blogs.msdn.microsoft.com/apimanagement/2018/03/28/important-changes-to-openapi-import-and-export/) para obtener información importante y consejos relacionados con la importación de OpenAPI.

## <a name="wsdl"></a>WSDL
Los archivos WSDL se usan para generar las API de paso a través de SOAP, o para servir como back-end de una API de SOAP a REST.

* **WSDL:Import**: actualmente APIM no admite las API que usan este atributo. Los clientes deben combinar los elementos importados en un documento.
* **Mensajes con varias partes**: actualmente APIM no admite estos tipos de mensajes.
* Los servicios de SOAP **WCF wsHttpBinding** creados con Windows Communication Foundation deben utilizar basicHttpBinding - wsHttpBinding.
* **MTOM**: Los servicios que usan MTOM <em>pueden</em> funcionar. No se ofrece soporte técnico oficial en este momento.
* APIM no admite los tipos de **recursión** que se definen de manera recursiva (por ejemplo, hacen referencia a una matriz de ellos mismos).

## <a name="wadl"></a>WADL
Actualmente, no hay ningún problema de importación conocido de WADL.
