---
title: "Directivas de retención de informes de Azure Active Directory | Microsoft Docs"
description: "Directivas de retención de datos de informes en Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/15/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 61d3e8fbe26ab24ba0b551e52be0769228f09a11
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-report-retention-policies"></a>Directivas de retención de informes de Azure Active Directory


Este tema proporciona respuestas a las preguntas más frecuentes en relación a la retención de datos de los distintos informes de actividad de Azure Active Directory. 

**P: ¿Cómo puede empezar la recopilación de datos de actividad?**

**R:**

| Edición de Azure AD | Inicio de la recopilación |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Al suscribirse a una suscripción |
| Azure AD Free | La primera vez que abra la [hoja de Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) o use las [API de informes](https://aka.ms/aadreports)  |

---
**P: ¿Cuándo estarán disponibles los datos de actividad en Azure Portal?**

**R:**

- **Inmediatamente**: si ya ha estado trabajando con informes en Azure Portal.
- **Dentro de 2 horas**: si no ha activado la generación de informes en Azure Portal.

---
**P: ¿Cómo puede empezar la recopilación de señales de seguridad?**  

**R:** En el caso de las señales de seguridad, el proceso de recopilación se inicia cuando decide usar Identity Protection Center. 


---
**P: ¿Durante cuánto tiempo se almacenan los datos recopilados?**

**R:**

**Informes de actividad**    

| Informe                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Auditoría de directorio        | 7 días        | 30 días             | 30 días             |
| Actividad de inicio de sesión       | N/D           | 30 días             | 30 días             |
| Uso de Azure MFA        | 30 días       | 30 días             | 30 días             |

**Señales de seguridad**

| Informe         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Usuarios en riesgo  | 7 días        | 30 días             | 90 días             |
| Inicios de sesión no seguros | 7 días        | 30 días             | 90 días             |

---
