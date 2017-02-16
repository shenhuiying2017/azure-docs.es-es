---
title: "Aplicación de actualizaciones del sistema en Azure Security Center | Microsoft Docs"
description: "En este documento se muestra cómo implementar las recomendaciones de **Aplicar actualizaciones del sistema** y **Reiniciar tras actualizar el sistema** de Azure Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 8fc8917555028d3045166e462362d502e677e4a0


---
# <a name="apply-system-updates-in-azure-security-center"></a>Aplicar actualizaciones del sistema en Azure Security Center
Azure Security Center supervisa diariamente las máquinas virtuales Windows y Linux por si faltan actualizaciones de sistema operativo. Security Center recupera una lista de actualizaciones críticas y de seguridad disponibles desde Windows Update o Windows Server Update Services (WSUS), dependiendo de qué servicio está configurado en una máquina virtual Windows.  Security Center comprueba también las últimas actualizaciones de los sistemas Linux. Si falta una actualización del sistema en la máquina virtual, Security Center le recomendará que aplique las actualizaciones del sistema

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo.  No se trata de una guía paso a paso.
> 
> 

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
1. En la hoja **Recomendaciones**, seleccione **Aplicar actualizaciones del sistema**.
   ![Aplicar actualizaciones del sistema][1]
2. Se abre la hoja **Aplicar actualizaciones del sistema** con una lista de las máquinas virtuales en las que faltan actualizaciones del sistema. Seleccione una máquina virtual.
   ![Seleccionar una máquina virtual][2]
3. Se abre una hoja que muestra una lista de las actualizaciones que faltan en esa máquina virtual. Seleccione una actualización del sistema. En este ejemplo, vamos a seleccionar KB3156016.
   ![Actualizaciones de seguridad que faltan][3]
4. Siga los pasos de la hoja **Actualización de seguridad** para aplicar la actualización que falta.
   ![Security update][4]

## <a name="reboot-after-system-updates"></a>Reiniciar tras actualizar el sistema
1. Vuelva a la hoja **Recomendaciones** . Se genera una nueva entrada después de aplicar las actualizaciones del sistema, denominada **Reiniciar tras actualizar el sistema**. Esta entrada permite saber que tiene que reiniciar la máquina virtual para completar el proceso de aplicación de las actualizaciones del sistema.
   ![Reiniciar tras actualizar el sistema][5]
2. Seleccione **Reiniciar tras actualizar el sistema**. Se abre la hoja **Hay un reinicio pendiente para completar las actualizaciones del sistema** que muestra una lista de máquinas virtuales que deben reiniciarse para completar el proceso de aplicación de actualizaciones del sistema.
   ![Reinicio pendiente][6]

Reinicie la máquina virtual de Azure para completar el proceso.

## <a name="see-also"></a>Consulte también
Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que le ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md) : obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md) : aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : busque las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : encuentre publicaciones de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/recommendation.png
[2]:./media/security-center-apply-system-updates/select-vm.png
[3]: ./media/security-center-apply-system-updates/missing-security-updates.png
[4]: ./media/security-center-apply-system-updates/security-update.png
[5]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[6]: ./media/security-center-apply-system-updates/restart-pending.png



<!--HONumber=Dec16_HO2-->


