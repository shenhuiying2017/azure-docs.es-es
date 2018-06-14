---
title: Conexión de Microsoft Azure Application Gateway con Azure Security Center | Microsoft Docs
description: Aprenda a integrar Microsoft Azure Application Gateway y Azure Security Center para mejorar la seguridad general de todos los recursos.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: terrylan
ms.openlocfilehash: 7c15e5a86df7ff2a374aa9b62d2775b1eb035fc6
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
ms.locfileid: "29854287"
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>Conexión de Microsoft Azure Application Gateway con Azure Security Center
Este documento le ayuda a configurar la integración del firewall de aplicaciones web de Application Gateway (WAF) y Security Center.

## <a name="why-connect-application-gateway"></a>Motivos para la conexión de Application Gateway
El firewall de aplicaciones web de Application Gateway protege las aplicaciones web de ataques web comunes, como inyección de código SQL, ataques de scripts entre sitios y secuestros de sesiones. Security Center se integra con Application Gateway para impedir y detectar amenazas a aplicaciones web no protegidas de su entorno.

## <a name="how-do-i-configure-this-integration"></a>¿Cómo se puede configurar esta integración?
Security Center detecta instancias de WAF previamente implementadas en la suscripción. Conecte estas soluciones a Security Center para permitir la integración de las alertas.

> [!NOTE]
> El firewall de aplicaciones web de Application Gateway también se puede aprovisionar desde **Recomendaciones** de Security Center como se indica en [Adición de un firewall de aplicaciones web](security-center-add-web-application-firewall.md).
>
>

1. Inicie sesión en el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/).

2. En el **menú de Microsoft Azure**, seleccione **Security Center**. Se abre **Security Center: Información general**.

3. En **Información general**, seleccione **Soluciones de seguridad**.

  ![Introducción a Security Center](./media/security-center-connect-application-gateway/overview.png)

4. En **Soluciones detectadas** busque firewall de aplicaciones web basado en SaaS de Microsoft y seleccione **CONECTAR**.

  ![Soluciones detectadas](./media/security-center-connect-application-gateway/connect.png)

5. Abra **Conexión de una solución de WAF**.  Seleccione **Conectar** para integrar el firewall de aplicaciones web y Security Center.

  ![Conexión de una solución de WAF](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a integrar un firewall de aplicaciones web de Application Gateway en Security Center. Para más información sobre Security Center, consulte los siguientes artículos:

* [Integración de soluciones de seguridad en Security Center](security-center-partner-integration.md)
* [Conexión de Microsoft Advanced Threat Analytics con Security Center](security-center-ata-integration.md)
* [Conexión de Azure Active Directory Identity Protection con Security Center](security-center-aadip-integration.md)
* [Supervisión del estado de seguridad en Security Center](security-center-monitoring.md).
* [Supervise las soluciones de asociados con Security Center](security-center-partner-solutions.md).
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md).
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/).
