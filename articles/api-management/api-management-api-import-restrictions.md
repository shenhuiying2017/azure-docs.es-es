---
title: "Restricciones y problemas conocidos en la importación de la API Azure API Management | Microsoft Docs"
description: "Detalles de los problemas conocidos y las restricciones en la importación en Azure API Management con los formatos de Open API, WSDL o WADL."
services: api-management
documentationcenter: 
author: vladvino
manager: vlvinogr
editor: 
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: 758babce3ed387ed4864f1934650cf701bda788f
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="api-import-restrictions-and-known-issues"></a>Restricciones de importación de API y problemas conocidos
## <a name="about-this-list"></a>Acerca de esta lista
Al importar una API, podría encontrarse con algunas restricciones o identificar problemas que es necesario corregir antes de realizar esta operación. En este artículo se documenta todo esto, organizado por el formato de importación de la API.

## <a name="open-api"></a>Open API/Swagger
Si recibe errores al importar el documento de Open API, asegúrese de que lo ha validado, ya sea mediante el diseñador en Azure Portal (Diseño - Front-End - Open API Specification Editor [Editor de especificaciones de Open API]) o con una herramienta de terceros como <a href="http://www.swagger.io">Swagger Editor</a>.

* **Nombre de host**: APIM requiere un atributo de nombre de host.
* **Ruta de acceso base**: APIM requiere un atributo de ruta de acceso base.
* **Esquemas**: APIM requiere una matriz de esquema. 

## <a name="wsdl"></a>WSDL
Los archivos WSDL se usan para generar las API de paso a través de SOAP, o para servir como back-end de una API de SOAP a REST.

* **WSDL:Import**: actualmente APIM no admite las API que usan este atributo. Los clientes deben combinar los elementos importados en un documento.
* **Mensajes con varias partes**: actualmente APIM no admite estos tipos de mensajes.
* Los servicios de SOAP **WCF wsHttpBinding** creados con Windows Communication Foundation deben utilizar basicHttpBinding - wsHttpBinding.
* **MTOM**: Los servicios que usan MTOM <em>pueden</em> funcionar. No se ofrece soporte técnico oficial en este momento.
* APIM no admite los tipos de **recursión** que se definen de manera recursiva (por ejemplo, hacen referencia a una matriz de ellos mismos).

## <a name="wadl"></a>WADL
Actualmente, no hay ningún problema de importación conocido de WADL.
