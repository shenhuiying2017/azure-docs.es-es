---
title: "Escalado del número de instancias de forma manual o automática con Azure Portal | Microsoft Docs"
description: Aprenda a escalar los servicios de Azure.
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2397596a-071f-4d49-8893-bec5f735bd7b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: ancav
ms.openlocfilehash: 7e16926b5a1c28c0c8c80ee8c17e8c2fdb9e55a7
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="scale-instance-count-manually-or-automatically"></a>Escalación del recuento de instancias de forma manual o automática
En el [Azure Portal](https://portal.azure.com/), puede establecer manualmente el número de instancias de su servicio o bien establecer parámetros para que esta cantidad se escale automáticamente en función de la demanda. Esto se conoce normalmente como *escalar horizontalmente* o *reducir horizontalmente*.

Antes de escalar según el recuento de instancias, tenga en cuenta que, además de con el recuento de instancias, el escalado está relacionado con el **plan de tarifa**. Diferentes niveles de precios pueden tener diferentes cantidades de núcleos y memoria y, por tanto, es posible encontrar un mejor rendimiento para el mismo número de instancias. (lo que se conoce como *Escalar verticalmente* o *Reducir verticalmente*). Este artículo trata específicamente sobre *Reducir horizontalmente* y *Escalar horizontalmente*.

Puede reducir horizontalmente en el portal, y también puede utilizar la [API de REST](https://msdn.microsoft.com/library/azure/dn931953.aspx) o el [SDK de .NET](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) para ajustar la escala de forma manual o automática.

## <a name="scaling-manually"></a>Escalado manual
1. En [Azure Portal](https://portal.azure.com/), haga clic en **Examinar** y luego navegue hasta el recurso que desea escalar, como un **plan de App Service**.
2. Haga clic en **Configuración > Escalar horizontalmente (plan de App Service).**
3. En la parte superior de la hoja **Escalar** puede ver un historial de las acciones de escalado automático del servicio.
   
    ![Hoja Escala](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)
   
   > [!NOTE]
   > Solo aparecerán en este gráfico las acciones que se lleven a cabo mediante el escalado automático. Si ajusta manualmente el recuento de instancias, el cambio no se reflejará en este gráfico.
   > 
   > 
4. Puede ajustar manualmente el número de **Instancias** con el control deslizante.
5. Haga clic en el comando **Guardar** y se le escalará a ese número de instancias casi de inmediato.

## <a name="scaling-based-on-a-pre-set-metric"></a>Escalado según una métrica preestablecida
Si desea que el número de instancias se ajuste automáticamente en función de una métrica, seleccione la métrica que desee en la lista desplegable **Escalar por** . Por ejemplo, para un **plan de App Service** puede escalar por **Porcentaje de CPU**.

1. Cuando seleccione una métrica aparecerá un control deslizante o cuadros de texto para que especifique el número de instancias entre el que desea escalar:
   
    ![Hoja Escala con Porcentaje de la CPU](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)
   
    El escalado automático nunca llevará a su servicio por debajo o por encima de los límites que establezca, independientemente de la carga.
2. En segundo lugar, se elige el intervalo de destino para la métrica. Por ejemplo, si elige **Porcentaje de CPU**, puede establecer un objetivo para el promedio de CPU entre todas las instancias del servicio. Se producirá un escalado horizontal cuando el promedio de CPU supere el valor máximo definido; del mismo modo, se producirá una reducción horizontal cuando el promedio de CPU caiga por debajo del mínimo.
3. Haga clic en el comando **Guardar** . El escalado automático comprobará el estado cada pocos minutos para asegurarse de que se encuentra en el rango de instancias y en el destino de la métrica. Cuando el servicio recibe tráfico adicional, obtendrá más instancias sin hacer nada.

## <a name="scale-based-on-other-metrics"></a>Escalación según otras métricas
Puede escalar en función de métricas diferentes de los valores preestablecidos que aparecen en la lista desplegable **Escalar por** , y puede incluso tener un conjunto complejo de reglas de escalado y reducción horizontal.

### <a name="adding-or-changing-a-rule"></a>Incorporación o cambio de una regla
1. Elija las **reglas de programación y rendimiento** en la lista desplegable **Escalar por:**: ![Reglas de rendimiento](./media/insights-how-to-scale/Insights_PerformanceRules.png).
2. Si anteriormente tenía el escalado automático, obtendrá una vista de las reglas exactas que tenía.
3. Para escalar según otra métrica , haga clic en la fila **Agregar regla** . También puede hacer clic en una de las filas existentes para cambiar de la métrica que tenía anteriormente a la métrica por la que desea escalar.
   ![Add rule](./media/insights-how-to-scale/Insights_AddRule.png)
4. Ahora debe seleccionar por qué métrica desea escalar. Al elegir una métrica hay un par de cosas que debe tener en cuenta:
   
   * El *recurso* del que proviene la métrica. Normalmente, este será el mismo que el recurso que está escalando. Sin embargo, si desea escalar por la longitud de una cola de Almacenamiento, el recurso es la cola por la que desea escalar.
   * El propio *nombre de la métrica* .
   * La *agregación de tiempo* de la métrica. Se trata de cómo se combinan los datos sobre la *duración*.
5. Después de elegir la métrica, se debe elegir el umbral para dicha métrica y el operador. Por ejemplo, podría decir **Mayor que** **80 %**.
6. A continuación, elija la acción que desee realizar. Hay un par de diferentes tipos de acciones:
   
   * Aumentar o disminuir por: esto agregará o quitará el número de instancias según el **valor** que defina.
   * Porcentaje de aumento o reducción: esto cambiará el recuento de instancias según un porcentaje. Por ejemplo, podría poner 25 en el campo **valor** y, si actualmente tuviera 8 instancias, se agregarían 2.
   * Aumentar o disminuir a : establecerá el recuento de instancias en el **valor** que defina.
7. Por último, puede elegir espera: el tiempo que tiene que esperar esta regla tras una acción de escalado anterior para volver a escalar.
8. Después de configurar la regla, haga clic en **Aceptar**.
9. Una vez que haya configurado todas las reglas que desea, asegúrese de hacer clic en el comando **Guardar** .

### <a name="scaling-with-multiple-steps"></a>Escalación con varios pasos
Los ejemplos anteriores son bastante básicos. Sin embargo, si desea ser más agresivo en cuanto a la escalación o la reducción vertical, puede agregar incluso varias reglas de escala para la misma métrica. Por ejemplo, puede definir dos reglas de escalado en el porcentaje de CPU:

1. Escalar horizontalmente por 1 instancia si el porcentaje de CPU es superior al 60 %
2. Escalar horizontalmente por 3 instancias si el porcentaje de CPU es superior al 85 %

![Varias reglas de escalado](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Con esta regla adicional, si la carga excede del 85 % antes de una acción de escalado, obtendrá dos instancias adicionales en lugar de una.

## <a name="scale-based-on-a-schedule"></a>Escalación según una programación
De forma predeterminada, al crear una regla de escala se aplicará siempre una regla de escalado. Puede ver que al hacer clic en el encabezado de perfil:

![Perfil](./media/insights-how-to-scale/Insights_Profile.png)

Sin embargo, es posible que desee una escalación más agresiva en el día, o en la semana, que en el fin de semana. Incluso puede apagar el servicio completamente fuera de horas de trabajo.

1. Para ello, en el perfil que tiene, seleccione **periodicidad** en lugar de **siempre** y elija las veces que desea que se aplique el perfil.
2. Por ejemplo, para tener un perfil que se aplique durante la semana, en el desplegable **Días**, desactive **Sábado** y **Domingo**.
3. Para tener un perfil que se aplique durante el día, establezca la **Hora de inicio** en la hora del día en que desee que comience.
   
    ![Periodicidad predeterminada](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)
4. Haga clic en **Aceptar**.
5. A continuación, deberá agregar el perfil que desee aplicar a otras horas. Haga clic en la fila **Agregar perfil** .
    ![No laborable](./media/insights-how-to-scale/Insights_ProfileOffWork.png)
6. Ponga un nombre a su nuevo segundo perfil; por ejemplo, podría llamarlo **No laborable**.
7. A continuación, seleccione de nuevo **periodicidad** y elija el intervalo de recuento de instancias que desee durante este tiempo.
8. Al igual que con el perfil predeterminado, elija los **días** que desee aplicar a este perfil y la **hora de inicio** durante el día.
   
   > [!NOTE]
   > El escalado automático utilizará las reglas del horario de verano sin importar la **Zona horaria** que seleccione. Sin embargo, durante el horario de verano, la diferencia UTC mostrará la diferencia de la zona horaria básica, no la diferencia UTC del horario de verano.
   > 
   > 
9. Haga clic en **Aceptar**.
10. Ahora, necesitará agregar las reglas que desee aplicar cuando esté en vigor el segundo perfil. Haga clic en **Agregar regla**, y, a continuación, puede crear la misma regla que tenga para el perfil predeterminado.
    
    ![Agregar una regla para el horario no laborable](./media/insights-how-to-scale/Insights_RuleOffWork.png)
11. Asegúrese de crear una regla tanto para el escalado horizontal como para la reducción horizontal; si no, cuando el perfil esté en vigor, el recuento de instancias únicamente crecerá (o disminuirá).
12. Finalmente, haga clic en **Guardar**.

## <a name="next-steps"></a>Pasos siguientes
* [Supervise las métricas de servicio](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y que responde adecuadamente.
* [Habilite la supervisión y el diagnóstico](insights-how-to-use-diagnostics.md) para recopilar métricas detalladas de alta frecuencia en su servicio.
* [Reciba notificaciones de alerta](insights-receive-alert-notifications.md) cada vez que se produzcan eventos de operaciones o las métricas traspasen un umbral.
* [Supervise el rendimiento de la aplicación](../application-insights/app-insights-azure-web-apps.md) si desea comprender exactamente cómo funciona su código en la nube.
* [Vea eventos y el registro de actividades](insights-debugging-with-events.md) para saber todo lo que ha sucedido en el servicio.
* [Supervise la disponibilidad y la capacidad de respuesta de cualquier página web](../application-insights/app-insights-monitor-web-app-availability.md) con Application Insights, para poder averiguar si su página está inactiva.

