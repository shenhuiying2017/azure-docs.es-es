---
title: "Directivas de retención de informes de Azure Active Directory | Microsoft Docs"
description: "Directivas de retención de datos de informes en Azure Active Directory"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2016
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 8a7d1bd1db8abdac5b741d79b10c9b363afecd24
ms.openlocfilehash: 9f8d9c845c391ec24aa3b265aa3078840fe3240a


---
# <a name="azure-active-directory-report-retention-policies"></a>Directivas de retención de informes de Azure Active Directory
*Esta documentación forma parte de la [guía de informes de Azure Active Directory](active-directory-reporting-guide.md).*


Este tema proporciona respuestas a las preguntas más frecuentes en relación a la retención de datos de los distintos informes de actividad de Azure Active Directory. 

¿Cómo puede empezar la recopilación de datos de actividad?

| Edición de Azure AD | Inicio de la recopilación |
| :--              | :--   |
|Premium y Premium 2 | Cuando se suscribe a una licencia |
| Gratuito | La primera vez que abra la [hoja de Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) o use las [API de informes](https://aka.ms/aadreports)  |


¿Cuándo estarán disponibles los datos de actividad en Azure Portal?

- **Inmediatamente**: si ya ha estado trabajando con informes en el Portal de Azure clásico
- **Dentro de 2 horas**: si no ha activado la generación de informes en el Portal de Azure clásico

¿Cómo puede empezar la recopilación de señales de seguridad?  
En el caso de las señales de seguridad, el proceso de recopilación se inicia cuando decide usar Identity Protection Center. 

¿Durante cuánto tiempo se almacenan los datos recopilados?

**Informes de actividad**    

| Informe | Azure AD Free | Azure AD Premium 1 | Azure AD Premium 2 |
| :--    | :--           | :--                | :--                |
| Auditoría de directorio | 7 días | 30 días | 30 días |
| Actividad de inicio de sesión |    7 días | 30 días | 30 días |

**Señales de seguridad**

| Informe | Azure AD Free | Azure AD Premium 1 | Azure AD Premium 2 |
| :--    | :--           | :--                | :--                |
| Inicios de sesión no seguros | 7 días | 30 días | 90 días |





<!--HONumber=Dec16_HO4-->


