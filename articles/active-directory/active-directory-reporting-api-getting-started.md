---

title: "Introducción a la API de generación de informes de Azure AD | Microsoft Docs"
description: "Introducción a la API de generación informes de Azure Active Directory"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: dhanyahk;markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 414e6b0f23616f8f3d059f250335b0e530729741
ms.contentlocale: es-es
ms.lasthandoff: 05/09/2017


---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Introducción a la API de generación de informes de Azure Active Directory
*Esta documentación forma parte de la [guía de generación de informes de Azure Active Directory](active-directory-reporting-guide.md).*

Azure Active Directory proporciona una variedad de informes. Los datos de estos informes pueden ser muy útiles para las aplicaciones, como sistemas SIEM, auditorías y herramientas de inteligencia empresarial. Las API de generación de informes de Azure AD proporcionan acceso mediante programación a los datos a través de un conjunto de API de REST. Estas API pueden llamarse desde una variedad de lenguajes de programación y herramientas.

Este artículo proporciona la información que necesita para empezar a trabajar con las API de generación de informes de Azure AD.
En la sección siguiente, puede encontrar más detalles sobre el uso de las API de auditoría e inicio de sesión. Para el resto de las API, consulte el artículo de [eventos e informes de Azure AD (versión preliminar)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview).

Para ver preguntas, problemas o comentarios, póngase en contacto con el equipo de [ayuda de informes de AAD](mailto:aadreportinghelp@microsoft.com).

## <a name="learning-map"></a>Mapa de aprendizaje
1. **Preparación** : para poder usar los ejemplos de la API, debe completar la [requisitos previos para tener acceso a la API de generación de informes de Azure AD](active-directory-reporting-api-prerequisites.md).
2. **Exploración** : obtenga una primera impresión de las API de generación de informes.
   
   * [Uso de los ejemplos de la API de auditoría](active-directory-reporting-api-audit-samples.md) 
   * [Uso de los ejemplos de la API de generación de informes de actividad de inicio de sesión](active-directory-reporting-api-sign-in-activity-samples.md)
3. **Personalización** : cree su propia solución: 
   
   * [Uso de la referencia de la API de auditoría](active-directory-reporting-api-audit-reference.md) 
   * [Uso de la referencia de la API de generación de informes de actividad de inicio de sesión](active-directory-reporting-api-sign-in-activity-reference.md)

## <a name="next-steps"></a>Pasos siguientes
Todos los puntos de conexión disponibles de API Graph de Azure se encuentran en la página [https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta).


