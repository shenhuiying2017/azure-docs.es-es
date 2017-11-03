---
title: "Conexión de Azure AD Identity Protection con Azure Security Center | Microsoft Docs"
description: "Obtenga información sobre cómo Azure Security Center se integra con Azure Active Directory Identity Protection."
services: security-center
documentationcenter: na
author: YuriDio
manager: MBaldwin
editor: 
ms.assetid: 0d4b77c2-dba4-4e46-8f55-ab04ddd92496
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 7562dd5e1c303a6cb97d25bda5aa080bb5643583
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2017
---
# <a name="connecting-azure-active-directory-identity-protection-to-azure-security-center"></a>Conexión de Azure AD Identity Protection con Azure Security Center
Este documento lo ayuda a configurar la integración entre Azure Active Directory (AD) Identity Protection y Azure Security Center.

## <a name="why-connect-azure-ad-identity-protection"></a>¿Por qué se debe conectar Azure AD Identity Protection?
[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) facilita la detección de posibles vulnerabilidades que afectan a las identidades de la organización. Cuando se conecta, es posible ver las alertas de Azure AD Identity Protection en Security Center. Esta integración permite ver, poner en correlación e investigar todas las alertas de seguridad relacionadas con las cargas de trabajo de nube híbrida en Security Center. 

## <a name="how-do-i-configure-this-integration"></a>¿Cómo se puede configurar esta integración?
Si su organización ya usa Azure AD Identity Protection, siga estos pasos para configurar la integración:

1. Abra el panel **Security Center**.
2. En el panel izquierdo, haga clic en **Soluciones de seguridad**. Security Center detecta automáticamente si Azure AD Identity Protection está habilitado en su organización.

    ![AADIP](./media/security-center-aadip-integration/security-center-aadip-integration-fig1.png)

3. Haga clic en **CONECTAR**.
4. En la página **Integrar Azure AD Identity Protection**, desplácese hacia abajo y seleccione el área de trabajo adecuada:

    ![área de trabajo](./media/security-center-aadip-integration/security-center-aadip-integration-fig2.png)

5. Haga clic en **Conectar**.

Después de finalizar esta configuración, la solución Azure AD Identity Protection aparece en la página **Soluciones de seguridad** en **Soluciones conectadas**. 

## <a name="next-steps"></a>Pasos siguientes
En este documento ha aprendido a conectar Azure AD Identity Protection con Security Center. Para más información sobre Security Center, consulte los siguientes artículos:

* [Connecting Microsoft Advanced Threat Analytics to Azure Security Center](security-center-ata-integration.md) (Conexión de Microsoft Advanced Threat Analytics con Azure Security Center)
* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que lo ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md) : obtenga información sobre cómo supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md) : aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Seguridad de datos de Azure Security Center](security-center-data-security.md): aprenda cómo se administran y protegen los datos en Security Center.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenga las últimas noticias e información sobre la seguridad en Azure.


