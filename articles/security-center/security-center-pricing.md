---
title: Precios de Security Center | Microsoft Docs
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
ms.date: 10/12/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a39a8aaeb2bc3603e5fd2404f061697c98c0ff2f


---
# <a name="azure-security-center-pricing"></a>Precios de Azure Security Center
El Centro de seguridad de Azure ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en sus recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones de Azure, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

## <a name="pricing-tiers"></a>Planes de tarifa
Security Center se ofrece en dos niveles:

* El **nivel gratuito** está habilitado automáticamente en todas las suscripciones de Azure. El nivel gratuito permite ver el estado de seguridad de los recursos de Azure, la directiva de seguridad básica, recomendaciones de seguridad y la integración con productos y servicios de seguridad de asociados.
* En el **nivel Estándar** se agregan funciones de detección de amenazas avanzada, como inteligencia de amenazas, análisis del comportamiento, detección de anomalías, incidentes de seguridad e informes de evaluación de amenazas. Hay disponible una **prueba gratuita de 90 días** para el nivel Estándar.

Para más información, vea la [página de precios](https://azure.microsoft.com/pricing/details/security-center/) de Security Center.

> [!NOTE]
> Security Center usa Azure Storage para guardar los datos de seguridad generados en los nodos protegidos. Los costos asociados con este almacenamiento no se incluyen en el precio del servicio y se cobrarán por separado según las [tarifas normales de Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/). Se aplicarán cargos de almacenamiento incluso durante la prueba.
> 
> 

## <a name="try-standard-free-for-90-days"></a>Pruebe el nivel Estándar de forma gratuita durante 90 días
Hay disponible una prueba gratuita de 90 días para el nivel Estándar. Para obtener la prueba gratuita del nivel Estándar, seleccione el icono **Directiva** en la hoja **Security Center**. Seleccione la suscripción que quiera actualizar al nivel Estándar. En la hoja **Directiva de seguridad**, seleccione **Plan de tarifa**. En la hoja **Seleccione su plan de tarifa**, seleccione **Estándar: prueba gratuita**.

![Evaluación gratuita][1]

Después de 90 días, si decide continuar usando el servicio, empezaremos a cobrar automáticamente el uso.

## <a name="upgrade-to-standard"></a>Actualización al nivel Estándar
Actualice al nivel Estándar para agregar la detección de amenazas avanzada. Para obtener el nivel Estándar, seleccione el icono **Directiva** en la hoja **Security Center**. Seleccione la suscripción que quiera actualizar al nivel Estándar. En la hoja **Directiva de seguridad**, seleccione **Plan de tarifa**. En la hoja **Elegir su plan de tarifa**, seleccione **Estándar**.

![Nivel Standard][2]

## <a name="why-upgrade-to-standard"></a>¿Por qué actualizar al nivel Estándar?
El nivel Estándar de Security Center ofrece todas las características del nivel gratuito, además de la detección de amenazas avanzada. La detección de amenazas avanzada ayuda a identificar amenazas activas dirigidas a sus recursos de Azure y le ofrece la información necesaria para responder rápidamente.

Security Center utiliza análisis avanzados que superan con creces los enfoques basados en firmas. Los grandes avances registrados en las tecnologías de macrodatos y aprendizaje automático se usan para evaluar eventos en todo el tejido de la nube, lo que permite identificar amenazas que no se podrían identificar con métodos manuales, así como predecir la evolución de los ataques.

Las funciones de análisis de seguridad que se incluyen con el nivel Estándar son las siguientes:

* **Inteligencia de amenazas**: busca actores malintencionados conocidos con la inteligencia de amenazas global de los productos y servicios de Microsoft, la Unidad de delitos informáticos de Microsoft (DCU), el Centro de respuestas de seguridad de Microsoft (MSRC) y otras fuentes externas.
* **Análisis del comportamiento**: aplica patrones conocidos para identificar comportamientos malintencionados.
* **Detección de anomalías**: usa la generación de perfiles estadísticos para crear un historial de línea de base. Envía alertas sobre las desviaciones de las líneas de base establecidas que se ajustan a un posible vector de ataque.

En la hoja **Alertas de seguridad** siguiente, Security Center ha encontrado un **incidente** de seguridad. Un incidente de seguridad es la suma de todas las alertas de un recurso que se alinean con patrones de cadenas de eliminación. Al seleccionar el incidente de seguridad, se muestran más detalles sobre el incidente y una lista de las alertas relacionadas. Al seleccionar una alerta, se muestra más información sobre esa repetición.

![Incidente de seguridad][3]

En la alerta siguiente de **Comunicación de red** se muestran los detalles de la alerta. Los detalles son la descripción completa, la gravedad, el estado actual (que en este caso es descartado, lo que significa que el usuario lo descartó), el recurso atacado y los pasos para la corrección. También se muestra una lista de vínculos a informes de Inteligencia de amenazas de Microsoft. Estos informes se pueden usar con fines de corrección de seguridad y de defensa.

![Detalles de alertas de seguridad][4]

## <a name="enable-data-collection"></a>Habilitación de recolección de datos
Para habilitar el análisis de comportamiento de máquinas virtuales, es necesario activar la recopilación de datos. Es posible que tenga que habilitar la recopilación de datos la primera vez que acceda al Security Center o al crear una directiva de seguridad.

Para comprobar que la recopilación de datos está habilitada, seleccione el icono **Directiva**. Se abrirá la hoja **Directiva de seguridad** con una lista de sus suscripciones de Azure. Seleccione una suscripción. Si la opción **Recopilación de datos** está desactivada, actívela y guarde los cambios. Vea [Habilitar la recopilación de datos en Azure Security Center](security-center-enable-data-collection.md).

## <a name="next-steps"></a>Pasos siguientes
* En este documento se han presentado los precios de Security Center. Para más información sobre precios, vea la [página de precios](https://azure.microsoft.com/pricing/details/security-center/) de Security Center.
* Para más información sobre las funciones avanzadas de detección de Security Center, vea [Funciones de detección de Azure Security Center](security-center-detection-capabilities.md).
* Si tiene preguntas acerca de cómo usar Security Center, consulte las [P+F de Azure Security Center](security-center-faq.md).
* Si aún tiene preguntas sobre el uso de Security Center o sobre Azure, visite los [foros de Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSecurityCenter&filter=alltypes&sort=lastpostdesc).

<!--Image references-->
[1]: ./media/security-center-pricing/free-trial.png
[2]: ./media/security-center-pricing/standard.png
[3]: ./media/security-center-pricing/incident.png
[4]: ./media/security-center-pricing/network-alert.png



<!--HONumber=Nov16_HO3-->


