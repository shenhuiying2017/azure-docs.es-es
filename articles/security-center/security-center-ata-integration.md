---
title: Conexión de Microsoft Advanced Threat Analytics con Azure Security Center | Microsoft Docs
description: Obtenga información acerca de cómo integrar Azure Security Center con Microsoft Advanced Threat Analytics.
services: security-center
documentationcenter: na
author: terrylan
manager: MBaldwin
editor: ''
ms.assetid: 5d80bf91-16c3-40b3-82fc-e0805e6708db
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: yurid
ms.openlocfilehash: a3444b9d42ffdd5f81568f0e9e09557096b4415f
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32775928"
---
# <a name="connecting-microsoft-advanced-threat-analytics-to-azure-security-center"></a>Conexión de Microsoft Advanced Threat Analytics con Azure Security Center
Este documento le ayuda a configurar la integración entre Microsoft Advanced Threat Analytics y Azure Security Center.

## <a name="why-add-advanced-threat-analytics-data"></a>¿Por qué agregar datos de Advanced Threat Analytics?
[Advanced Threat Analytics (ATA)](https://docs.microsoft.com/advanced-threat-analytics/what-is-ata) es una plataforma local que ayuda a detectar comportamientos sospechosos de usuario. Cuando se conecta, puede ver las acciones sospechosas detectadas por ATA en Security Center. Esta integración permite ver, poner en correlación e investigar todas las alertas de seguridad relacionadas con las cargas de trabajo de nube híbrida en Security Center. 

## <a name="how-do-i-configure-this-integration"></a>¿Cómo se puede configurar esta integración?
Si damos por hecho que ya tiene instalado de forma local ATA y en correcto funcionamiento, siga estos pasos para configurar esta integración:

1. Inicie sesión en el Centro de ATA y obtenga acceso al portal de ATA.
2. Haga clic en **servidor de Syslog** en el panel izquierdo.

    ![Servidor de Syslog](./media/security-center-ata-integration/security-center-ata-integration-fig1.png)

3. En el campo **Punto de conexión del servidor de Syslog**, escriba 127.0.0.7 (debe ser esta dirección) y escriba 5114 en el puerto (recomendado). Aunque el número de puerto es una recomendación, debería funcionar cualquier puerto único. Deje el resto de las opciones tal y como están y haga clic en **Guardar**.
4. Haga clic en **Notificaciones** en el panel izquierdo y habilite todas las notificaciones de Syslog (recomendado), como se muestra en la siguiente imagen:

    ![Notificaciones](./media/security-center-ata-integration/security-center-ata-integration-fig2.png)

5. Haga clic en **Save**(Guardar).
6. Abra el panel **Security Center**.
7. En el panel izquierdo, haga clic en **Soluciones de seguridad**.
8. En **Advanced Threat Analytics**, haga clic en **Agregar**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig3.png)
    
9. Vaya al último paso y haga clic en **Descargar agente**.

    ![ATA](./media/security-center-ata-integration/security-center-ata-integration-fig4.png)

10. En la página **Agregar nuevo equipo que no es de Azure**, seleccione el área de trabajo.

    ![Ajeno a Azure](./media/security-center-ata-integration/security-center-ata-integration-fig5.png)

11. En la página **Agente directo**, descargue el agente de Windows adecuado y tome nota del **Identificador de área de trabajo** y **Clave principal**.

    ![Agente directo](./media/security-center-ata-integration/security-center-ata-integration-fig6.png)

12. Instale el agente en el Centro de ATA. Durante la instalación, asegúrese de seleccionar la opción **Conectar el agente a Azure Log Analytics** y proporcione el  *de área de trabajo* y *clave principal* cuando se solicite.


Una vez que finaliza la instalación, se completa la integración y podrá ver las alertas nuevas que se envían desde ATA a Security Center en el resultado de la **búsqueda**. La solución aparece en la página **Soluciones de seguridad**, en **Soluciones conectadas**. 

## <a name="next-steps"></a>Pasos siguientes
En este documento ha aprendido a conectar Microsoft ATA con Security Center. Para más información sobre Security Center, consulte los siguientes artículos:

* [Conexión de Azure Active Directory Identity Protection con Azure Security Center](security-center-aadip-integration.md)
* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que lo ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md) : obtenga más información sobre cómo supervisar el estado de sus recursos en Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md) : aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Seguridad de datos de Azure Security Center](security-center-data-security.md): aprenda cómo se administran y protegen los datos en Security Center.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenga las últimas noticias e información sobre la seguridad en Azure.


