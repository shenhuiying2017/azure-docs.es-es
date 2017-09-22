---
title: Precios de Azure Security Center | Microsoft Docs
description: "En este artículo se ofrece información sobre los precios de Azure Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: cd4001a3280c83875f30b04d5d38caec26f4f0b1
ms.contentlocale: es-es
ms.lasthandoff: 09/13/2017

---
# <a name="azure-security-center-pricing"></a>Precios de Azure Security Center
Azure Security Center ofrece una administración de seguridad unificada y una protección contra amenazas avanzada para las cargas de trabajo que se ejecutan en Azure, de forma local y en otras nubes. Ofrece visibilidad y control sobre las cargas de trabajo de nube híbrida, defensas activas que reducen la exposición a las amenazas y una detección inteligente, lo que le permitirá mantenerse al día de los ciberataques, que se expanden rápidamente.

## <a name="pricing-tiers"></a>Planes de tarifa
Security Center se ofrece en dos niveles:

- El nivel **Gratis** se habilita automáticamente en todas las suscripciones de Azure y proporciona recomendaciones de directivas de seguridad, recomendaciones de evaluación continua de la seguridad y recomendaciones de seguridad que requieren acción para que pueda proteger sus recursos de Azure.
- El nivel **Estándar** amplía las capacidades del nivel Gratis a las cargas de trabajo que se ejecutan en nubes privadas y otras nubes públicas, lo que ofrece una administración de seguridad unificada y protección contra amenazas en todas sus cargas de trabajo de nube híbrida. El nivel Estándar también incorpora capacidades de detección de amenazas avanzada, donde se emplea el análisis de comportamiento integrado y el aprendizaje automático para identificar ataques y vulnerabilidades de día cero, controles de acceso y de aplicación para reducir la exposición a los ataques a la red y al malware, etc. El nivel Estándar es gratuito durante los primeros 60 días.

Para más información, vea la [página de precios](https://azure.microsoft.com/pricing/details/security-center/) de Security Center.

## <a name="try-standard-free-for-60-days"></a>Pruebe el nivel Estándar de forma gratuita durante 60 días
El nivel Estándar se ofrece gratis para los primeros 60 días. Después de 60 días, si decide continuar usando el servicio, se empezará a cobrar automáticamente por su uso.

Puede actualizar toda una suscripción a Azure al nivel Estándar, que heredarán todos los recursos de la suscripción, o puede definir una directiva única para actualizar solo un grupo de recursos en concreto.

Para obtener el nivel Estándar:

1. Seleccione **Directiva de seguridad** en el menú principal de **Security Center**.
2. Seleccione la suscripción que quiera actualizar al nivel Estándar.
3. En la hoja **Directiva de seguridad**, seleccione **Plan de tarifa**.
4. Seleccione **Estándar** para efectuar la actualización.
5. Haga clic en **Guardar**.

![Incidente de seguridad][1]

> [!NOTE]
> Para habilitar todas las características de Security Center, debe aplicar el plan de tarifa Estándar a la suscripción o grupo de recursos que contiene las máquinas virtuales aplicables. La configuración de los precios para un área de trabajo no habilita el acceso a VM Just-In-Time, los controles de aplicación adaptables ni las detecciones de red para los recursos de Azure.
>
>

## <a name="why-upgrade-to-standard"></a>¿Por qué actualizar al nivel Estándar?
Security Center ofrece una seguridad mejorada y una protección contra amenazas para las cargas de trabajo de nube híbrida, como, por ejemplo:

- **Seguridad híbrida**: Obtenga una vista unificada de la seguridad de todas sus cargas de trabajo locales y en la nube. Aplique directivas de seguridad y evalúe constantemente la seguridad de las cargas de trabajo de nube híbrida para garantizar el cumplimiento normativo con los estándares de seguridad. Recopile, busque y analice datos de seguridad de una gran variedad de orígenes, incluidos firewalls y otras soluciones de asociados.
- **Detección de amenazas avanzada**: Use análisis avanzado y el gráfico de seguridad inteligente de Microsoft para adelantarse a los ciberataques en constante cambio.  Aproveche la funcionalidad integrada de análisis del comportamiento y aprendizaje automático para identificar ataques y vulnerabilidades de seguridad de día cero. Supervise las redes, las máquinas y los servicios en la nube para detectar ataques entrantes y actividad posterior a una infracción de seguridad. Optimice la investigación con herramientas interactivas e inteligencia de amenazas contextual.
- **Controles de acceso y de aplicación**: Bloquee el malware y otras aplicaciones no deseadas aplicando recomendaciones de inclusión en lista blanca adaptadas a sus cargas de trabajo específicas y basadas en el aprendizaje automático. Reduzca la superficie expuesta a ataques de red con el acceso controlado Just-In-Time a los puertos de administración de las máquinas virtuales de Azure, lo que reducirá drásticamente la exposición a ataques por fuerza bruta y otros tipos de ataques de red.


## <a name="next-steps"></a>Pasos siguientes
En este artículo se han presentado los precios de Security Center. Para obtener más información sobre la seguridad mejorada y la protección contra amenazas avanzada del nivel Estándar, vea:

- [Detección de amenazas avanzada](security-center-threat-report.md)
- [Control de acceso a VM Just-In-Time](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png

