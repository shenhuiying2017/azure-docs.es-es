---
title: 'Tutorial de Azure Security Center: respuesta a incidentes relacionados con la seguridad | Microsoft Docs'
description: 'Tutorial de Azure Security Center: respuesta a incidentes relacionados con la seguridad'
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2018
ms.author: yurid
ms.custom: mvc
ms.openlocfilehash: b6adf4bec2d9f92ee3cde9c73a03beb5a58c3f3f
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="tutorial-respond-to-security-incidents"></a>Tutorial: respuesta a incidentes relacionados con la seguridad
Security Center analiza continuamente las cargas de trabajo de la nube híbrida y usa el análisis avanzado e inteligencia de amenazas para alertarle de cualquier actividad malintencionada. Además, en Security Center se pueden integrar alertas de otros servicios y productos de seguridad, así como crear alertas personalizadas basadas en indicadores u orígenes de inteligencia propios. Una vez que se genera una alerta, es necesario investigarla y tomar las medidas correctoras de inmediato. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Evaluar las prioridades de las alertas de seguridad
> * Investigar en profundidad para determinar la causa raíz y el alcance de un incidente de seguridad
> * Buscar en los datos de seguridad para facilitar la investigación

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos
Para recorrer todas las características que se tratan en este tutorial, es preciso tener el plan de tarifa Estándar de Security Center. Dicho plan se puede probar de forma gratuita los primeros 60 días. En [Guía de inicio rápido de Azure Security Center](security-center-get-started.md) le explicamos cómo realizar la actualización al plan de tarifa Estándar.

## <a name="triage-security-alerts"></a>Evaluar las prioridades de las alertas de seguridad
Security Center proporciona una vista unificada de todas las alertas de seguridad. Las alertas de seguridad se clasifican en función de su gravedad y cuando se combinan varias posibles alertas relacionadas, se genera un incidente de seguridad. Cuando la evolución de las prioridades de las alertas e incidentes debería:

- Descartar las alertas para las que no se necesitan acciones adicionales, por ejemplo, si la alerta es un falso positivo
- Actuar para corregir los ataques conocidos, por ejemplo bloqueando el tráfico de red procedente de una dirección IP malintencionada
- Determinar las alertas que requieren una posterior investigación


1. En el menú principal de Security Center, en **DETECCIÓN**, seleccione **Alertas de seguridad**:

  ![Alertas de seguridad](./media/tutorial-security-incident/tutorial-security-incident-fig1.png)  

2. Para más información acerca de este incidente, en la lista de alertas, haga clic en un incidente de seguridad, que es una colección de alertas. Se abre **Security incident detected** (Incidente de seguridad detectado).

  ![Incidente de seguridad](./media/tutorial-security-incident/tutorial-security-incident-fig2.png)

3. En esta pantalla, la descripción del incidente de seguridad se encuentra en la parte superior y en el resto la lista de alertas que forman parte de este incidente. Para más información, haga clic en la alerta que desea investigar en profundidad.

  ![Incidente de seguridad](./media/tutorial-security-incident/tutorial-security-incident-fig3.png)

  El tipo de alerta puede variar. Para más información acerca del tipo de alerta y los posibles pasos para corregirla, lea el artículo [Comprensión de las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). En las alertas que se puedan descartar de forma segura puede hacer clic con el botón derecho en la alerta y seleccione la opción **Descartar**:

  ![Alerta](./media/tutorial-security-incident/tutorial-security-incident-fig4.png)

4. Si no se conocen la causa raíz ni el alcance de la actividad malintencionada, continúe con el paso siguiente para investigar la alerta en profundidad.

## <a name="investigate-an-alert-or-incident"></a>Investigación de una alerta o un incidente
1. En la página **Alerta de seguridad**, haga clic en el botón **Iniciar investigación** (si ya la ha iniciado, el nombre cambia a **Continuar investigación**).

  ![Investigación](./media/tutorial-security-incident/tutorial-security-incident-fig5.png)

  El mapa de la investigación es una representación gráfica de las entidades que conectadas a esta alerta o incidente de seguridad. Al hacer clic en cualquiera de las entidades en la asignación, la información acerca de ella mostrará nuevas entidades, lo que hará que el mapa se expanda. Las propiedades de la entidad seleccionada en el mapa están resaltadas en el panel del lado derecho de la página. La información disponible en cada pestaña variará en función de la entidad seleccionada. Durante el proceso de investigación, revise toda la información relevante para comprender mejor el movimiento del atacante.

2. Si necesita más pruebas o debe investigar más las entidades que se encontraron durante la investigación, continúe con el paso siguiente.

## <a name="search-data-for-investigation"></a>Búsqueda de datos para la investigación

Las funcionalidades de búsqueda de Security Center se pueden usar no solo para encontrar más evidencia de sistemas en peligro sino también para obtener más detalles acerca de las entidades que forman parte de la investigación.

Para realizar una búsqueda, abra el panel de **Security Center**, haga clic en **Buscar** en el panel de navegación izquierdo, seleccione el área de trabajo que contenga las entidades que desea buscar, escriba la consulta de la búsqueda y haga clic en el botón Buscar.

## <a name="clean-up-resources"></a>Limpieza de recursos
Otras guías de inicio rápido y tutoriales de esta colección se basan en los valores de esta. Si planea continuar trabajando con las guías rápidas y tutoriales posteriores, debe seguir ejecutando el plan de tarifa Estándar y mantener el aprovisionamiento automático habilitado. Si no planea continuar o desea volver al nivel Gratis:

1. Vuelva al menú principal de Security Center y seleccione **Directiva de seguridad**.
2. Seleccione la suscripción o directiva que desea que vuelva al nivel Gratis. Se abre **Directiva de seguridad**.
3. En **COMPONENTES DE LA DIRECTIVA**, seleccione **Plan de tarifa**.
4. Seleccione **Gratis** para cambiar la suscripción de Estándar a Gratis.
5. Seleccione **Guardar**.

Si desea deshabilitar el aprovisionamiento automático:

1. Vuelva al menú principal de Security Center y seleccione **Directiva de seguridad**.
2. Seleccione la suscripción en la que quiere deshabilitar el aprovisionamiento automático.
3. En **Directiva de seguridad: Colección de datos**, en **Incorporación**, seleccione **Desactivado** para deshabilitar el aprovisionamiento automático.
4. Seleccione **Guardar**.

>[!NOTE]
> La deshabilitación del aprovisionamiento automático no quita Microsoft Monitoring Agent de las máquinas virtuales de Azure en las que se ha aprovisionado el agente. La deshabilitación del aprovisionamiento automático limita la supervisión de seguridad de los recursos.
>

## <a name="next-steps"></a>pasos siguientes
En este tutorial, ha obtenido información acerca de las características de Security Center que se deben usar cuando se responde a un incidente de seguridad, como:

> [!div class="checklist"]
> * Incidente de seguridad, que es una agregación de las alertas relacionadas de un recurso
> * Mapa de la investigación, que es una representación gráfica de las entidades que conectadas a un alerta o incidente de seguridad
> * Capacidades de búsqueda, para buscar más evidencia de los sistemas en peligro

Para más información acerca de las características de investigación de Security Center, consulte:

> [!div class="nextstepaction"]
> [Investigación de incidentes y alertas](security-center-investigation.md)
