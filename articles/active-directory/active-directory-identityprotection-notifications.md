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
ms.date: 10/17/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: abc0f3926905295a9cf239146cce7fc57da7eb29
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notificaciones de Azure Active Directory Identity Protection
Azure AD Identity Protection envía dos tipos de correos electrónicos de notificación automatizados para ayudar a administrar el riesgo del usuario y los eventos de riesgo:

* Correo electrónico de alerta para usuarios en peligro
* Correo electrónico de resumen semanal

## <a name="user-compromised-alert-email"></a>Correo electrónico de alerta para usuarios en peligro
Se genera una alerta de correo electrónico para usuarios en peligro cuando Azure AD Identity Protection identifica que una cuenta se ha puesto en peligro. El correo electrónico incluye un vínculo al informe Usuarios marcados como de riesgo en el panel de Identity Protection. Se recomienda que las notificaciones de cuentas en peligro se investiguen inmediatamente.

## <a name="weekly-digest-email"></a>Correo electrónico de resumen semanal
El correo electrónico de resumen semanal contiene un sumario de nuevos eventos de riesgo.<br>
Incluye:

* Usuarios en riesgo
* Actividades sospechosas
* Puntos vulnerables detectados
* Vínculos a los informes relacionados en Identity Protection

<br>
![Corrección](./media/active-directory-identityprotection-notifications/400.png "Corrección")
<br>

Puede desactivar la opción de envío de un correo electrónico de resumen semanal.
<br><br>
![Riesgos de usuario](./media/active-directory-identityprotection-notifications/62.png "riesgos de usuario")
<br>

**Para abrir el cuadro de diálogo de configuración relacionado, siga estos pasos**:

1. En la hoja **Azure AD Identity Protection**, haga clic en **Configuración**.
   <br><br>
   ![Directiva de riesgo de usuario](./media/active-directory-identityprotection-notifications/401.png "Directiva de riesgo de usuario")
   <br>
2. En la sección **General**, haga clic en **Notificaciones**.
   <br><br>
   ![Directiva de riesgo de usuario](./media/active-directory-identityprotection-notifications/405.png "Directiva de riesgo de usuario")
   <br>

## <a name="see-also"></a>Otras referencias
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)
