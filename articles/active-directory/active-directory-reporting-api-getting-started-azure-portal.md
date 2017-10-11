---
title: "Introducción a la API de generación de informes de Azure AD | Microsoft Docs"
description: "Introducción a la API de generación informes de Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/18/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9944cbd2b1b7c4acb18d37da1394c0bbc170f77d
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Introducción a la API de generación de informes de Azure Active Directory

Azure Active Directory proporciona una variedad de informes. Los datos de estos informes pueden ser muy útiles para las aplicaciones, como sistemas SIEM, auditorías y herramientas de inteligencia empresarial. Las API de generación de informes de Azure AD proporcionan acceso mediante programación a los datos a través de un conjunto de API de REST. Estas API pueden llamarse desde una variedad de lenguajes de programación y herramientas.

Este artículo proporciona la información que necesita para empezar a trabajar con las API de generación de informes de Azure AD.
En la sección siguiente, puede encontrar más detalles sobre el uso de las API de auditoría e inicio de sesión. 

Para conocer las preguntas más frecuentes, lea nuestras [Preguntas más frecuentes](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-faq). En caso de problemas, [abra una incidencia de soporte técnico](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="learning-map"></a>Mapa de aprendizaje
1. **Preparación** : para poder usar los ejemplos de la API, debe completar la [requisitos previos para tener acceso a la API de generación de informes de Azure AD](active-directory-reporting-api-prerequisites-azure-portal.md).
2. **Exploración** : obtenga una primera impresión de las API de generación de informes.
   
   * [Uso de los ejemplos de la API de auditoría](active-directory-reporting-api-audit-samples.md) 
   * [Uso de los ejemplos de la API de generación de informes de actividad de inicio de sesión](active-directory-reporting-api-sign-in-activity-samples.md)
3. **Personalización** : cree su propia solución: 
   
   * [Uso de la referencia de la API de auditoría](active-directory-reporting-api-audit-reference.md) 
   * [Uso de la referencia de la API de generación de informes de actividad de inicio de sesión](active-directory-reporting-api-sign-in-activity-reference.md)

## <a name="next-steps"></a>Pasos siguientes
Si tiene curiosidad por ver todos los puntos de conexión disponibles de la API Graph de Azure AD, use este vínculo: [https://graph.windows.net/tenant-name/activities/$metadata?api-version=beta](https://graph.windows.net/tenant-name/activities/$metadata?api-version=beta).

