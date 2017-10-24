---
title: "Preguntas más frecuentes sobre conectores personalizados - Azure Logic Apps | Microsoft Docs"
description: "Preguntas más frecuentes sobre los requisitos, los desencadenadores y demás acerca de la creación de conectores personalizados"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 225e56de3985acae871ddec447b763e7de61cb80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="faq-custom-connectors"></a>Preguntas más frecuentes: conectores personalizados

## <a name="requirements"></a>Requisitos

**P:** ¿Puedo crear un conector sin las API de REST? </br>
**R:** No, para crear un conector debe admitir las API de REST de HTTP estables para el servicio. 

**P:** ¿Qué herramientas puedo usar para crear un conector? </br>
**R:** Azure tiene funciones y servicios que puede utilizar para exponer cualquier servicio como una API, como Azure App Service para hospedaje, API Management y mucho más.

**P:** ¿Qué tipos de autenticación se admiten? </br>
**R:** Puede usar estos estándares de autenticación admitidos:

* [OAuth 2.0](https://oauth.net/2/), incluido [Azure Active Directory](https://azure.microsoft.com/develop/identity/) o servicios específicos, por ejemplo, Dropbox, GitHub y SalesForce
* OAuth 2.0 genérico
* [Autenticación básica](https://swagger.io/docs/specification/authentication/basic-authentication/)
* [Clave de API](https://swagger.io/docs/specification/authentication/api-keys/)

## <a name="triggers"></a>Desencadenadores

**P:**  ¿Puedo compilar desencadenadores sin webhooks? </br>
**R:** No, los conectores personalizados para Azure Logic Apps y Microsoft Flow admiten solo desencadenadores basados en webhook. Si desea solicitar otros patrones para la implementación, póngase en contacto con [ condevhelp@microsoft.com ](mailto:condevhelp@microsoft.com) con más detalles acerca de la API.

## <a name="certification"></a>Certificación

**Preguntas**: No soy partner de Microsoft ni proveedor de Software independiente (ISV). Aun así, ¿puedo crear conectores? </br>
**R**: Sí, puede registrar estos conectores para uso interno de la organización, pero si desea certificar y liberar públicamente un conector, debe ser propietario del servicio subyacente o presentar derechos explícitos para usar la API.

## <a name="other"></a>Otros

**P:**  Mis API utilizan un host dinámico. ¿Cómo las implemento con OpenAPI? </br>
**R:** Los conectores personalizados no admiten hosts dinámicos. Utilice en su lugar un host estático con fines de desarrollo y pruebas. Si desea certificar el conector, pregunte a su contacto de Microsoft acerca de la implementación dinámica.

**P:** ¿Se puede utilizar la versión 2 de una colección de Postman? </br>
**R:** No, actualmente se admite solo la versión 1 de una colección de Postman.

**P:** ¿Se puede utilizar OpenAPI 3.0? </br>
**R:** No, actualmente se admite solo OpenAPI 2.0.