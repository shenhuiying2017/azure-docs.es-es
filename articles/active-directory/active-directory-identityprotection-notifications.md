---
title: Notificaciones de Azure Active Directory Identity Protection | Microsoft Docs
description: "Obtenga información sobre cómo contribuyen las notificaciones a sus actividades de investigación."
services: active-directory
keywords: "azure active directory identity protection, detección de aplicaciones en la nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, punto vulnerable, directiva de seguridad"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: ce3379d5b5e883c6601c40aca191e8b84e3ad8d3
ms.openlocfilehash: 0170b5d2435f6e856478ee9e55ae26c626288f75
ms.contentlocale: es-es
ms.lasthandoff: 12/22/2016


---
<a id="azure-active-directory-identity-protection-notifications" class="xliff"></a>
# Notificaciones de Azure Active Directory Identity Protection
Azure AD Identity Protection envía dos tipos de correos electrónicos de notificación automatizados para ayudar a administrar el riesgo del usuario y los eventos de riesgo:

* Correo electrónico de alerta para usuarios en peligro
* Correo electrónico de resumen semanal

<a id="user-compromised-alert-email" class="xliff"></a>
## Correo electrónico de alerta para usuarios en peligro
Se genera una alerta de correo electrónico para usuarios en peligro cuando Azure AD Identity Protection identifica que una cuenta se ha puesto en peligro. El correo electrónico incluye un vínculo al informe Usuarios marcados como de riesgo en el panel de Identity Protection. Se recomienda que las notificaciones de cuentas en peligro se investiguen inmediatamente.

<a id="weekly-digest-email" class="xliff"></a>
## Correo electrónico de resumen semanal
El correo electrónico de resumen semanal contiene un sumario de nuevos eventos de riesgo.<br>
Incluye:

* Usuarios en riesgo
* Actividades sospechosas
* Puntos vulnerables detectados
* Vínculos a los informes relacionados en Identity Protection

<br>
![Corrección](./media/active-directory-identityprotection-notifications/400.png "Remediation")
<br>

Puede desactivar la opción de envío de un correo electrónico de resumen semanal.
<br><br>
![Riesgos de usuario](./media/active-directory-identityprotection-notifications/62.png "User risks")
<br>

**Para abrir el cuadro de diálogo de configuración relacionado**:

1. En la hoja **Azure AD Identity Protection**, haga clic en **Configuración**.
   <br><br>
   ![Directiva de riesgo de usuario](./media/active-directory-identityprotection-notifications/401.png "User risk policy")
   <br>
2. En la sección **General**, haga clic en **Notificaciones**.
   <br><br>
   ![Directiva de riesgo de usuario](./media/active-directory-identityprotection-notifications/405.png "User risk policy")
   <br>

<a id="see-also" class="xliff"></a>
## Consulte también
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

