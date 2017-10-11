---
title: Establecimiento de alertas en Azure Application Insights | Microsoft Docs
description: "Reciba notificaciones acerca de tiempos de respuesta lentos, excepciones y otros cambios de rendimiento o uso de la aplicación web."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: c8386ffc5d68260eeb75edf7efb77db1163dcef8
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="set-alerts-in-application-insights"></a>Definición de alertas en Application Insights
[Azure Application Insights][start] puede avisarle sobre los cambios en las métricas de rendimiento o de uso de la aplicación web. 

Application Insights supervisa la aplicación activa en una [amplia variedad de plataformas][platforms] para ayudarlo a diagnosticar problemas de rendimiento y entender los patrones de uso.

Hay tres tipos de alertas:

* **Alertas de métricas** le avisan cuando una métrica cruza un valor de umbral durante un período determinado: por ejemplo, tiempos de respuesta, recuentos de excepciones, uso de la CPU o vistas de página. 
* [**Pruebas web**][availability] le informan de que el sitio no está disponible en Internet o responde lentamente. [Más información][availability].
* [**Diagnósticos proactivos**](app-insights-proactive-diagnostics.md) se configuran automáticamente para que le notifiquen acerca de patrones de rendimiento no habituales.

Nos centraremos en las alertas de métricas en este artículo.

## <a name="set-a-metric-alert"></a>Establecimiento de una alerta de métrica
Abra la hoja Reglas de alerta y, a continuación, utilice el botón Agregar. 

![En la hoja Reglas de alerta, elija Agregar alerta. Establezca la aplicación como el recurso que se va a medir, proporcione un nombre para la alerta y elija una métrica.](./media/app-insights-alerts/01-set-metric.png)

* Establezca el recurso antes de las demás propiedades. **Elija el recurso "(components)"** si desea establecer alertas sobre métricas de rendimiento o de uso.
* El nombre que asigne a la alerta debe ser único dentro del grupo de recursos (no solo en la aplicación).
* Asegúrese de tener en cuenta las unidades en las que se le pide que escriba el valor de umbral.
* Si activa la casilla "Enviar correo electrónico a propietarios, colaboradores y lectores", las alertas se envían por correo electrónico a todos los usuarios con acceso a este grupo de recursos. Para expandir dicho conjunto de usuarios, agréguelos al [grupo de recursos o suscripción](app-insights-resources-roles-access-control.md) (no al recurso).
* Si especifica "Correos electrónicos adicionales", las alertas se envían a esos usuarios o grupos (independientemente de que haya activado la casilla anterior). 
* Establezca una [dirección de webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) si ha configurado una aplicación web para responder a las alertas. Se llamará a esta dirección cuando la alerta se active y cuando se haya resuelto. (Pero tenga en cuenta que, en la actualidad, los parámetros de consulta no se pasan como propiedades de webhook).
* Puede habilitar o deshabilitar la alerta: consulte los botones de la parte superior de la hoja.

*No puedo ver el botón Agregar alerta* 

* ¿Está usando una cuenta de organización? Puede establecer alertas si tiene acceso de propietario o colaborador a este recurso de aplicación. Eche un vistazo a la hoja Access Control. [Más información sobre el control de acceso][roles].

> [!NOTE]
> En la hoja de alertas, verá que ya hay una alerta configurada: [Proactive Diagnostics](app-insights-proactive-failure-diagnostics.md). La alerta automática supervisa una métrica determinada: el índice de errores de las solicitudes. A menos que decida deshabilitar esta opción, no es necesario establecer su propio índice de errores de las solicitudes. 
> 
> 

## <a name="see-your-alerts"></a>Visualización de alertas
Recibirá un correo electrónico cuando un alerta cambia el estado entre inactivo y activo. 

En la hoja de reglas Alerta se muestra el estado actual de cada alerta.

Hay un resumen de la actividad reciente en la lista desplegable de alertas:

![Lista desplegable de alertas](./media/app-insights-alerts/010-alert-drop.png)

El historial de cambios de estado está en el registro de actividad:

![En la hoja de información general, haga clic en Configuración, Registros de auditoría](./media/app-insights-alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Funcionamiento de las alertas
* Una alerta tiene tres estados: "Nunca activada", "Activada" y "Resuelta". "Activada" significa que la condición especificada tenía el valor true cuando se evaluó por última vez.
* Se genera una notificación cuando una alerta cambia de estado. (Si la condición de alerta ya tenía el valor true cuando creó la alerta, es posible que no reciba una notificación hasta que la condición cambie al valor false).
* Si ha activado la casilla de correos electrónicos o si ha proporcionado direcciones de correo electrónico, cada notificación generará un correo. También puede consultar la lista desplegable de notificaciones.
* Una alerta se evalúa cada vez que llega una métrica, pero no en caso contrario.
* La evaluación agrega la métrica durante el período anterior y, luego, la compara con el umbral para determinar el nuevo estado.
* El período que elija especifica el intervalo en el que se agregan métricas. No afecta a la frecuencia con la que se evalúa la alerta: depende de la frecuencia de llegada de métricas.
* Si no llega ningún dato para una métrica concreta durante un tiempo, este intervalo tiene efectos diferentes en la evaluación de la alerta y en los gráficos del Explorador de métricas. En el Explorador de métricas, si no se ve ningún dato durante más tiempo que el intervalo de muestreo del gráfico, el gráfico mostrará un valor de 0. Pero una alerta basada en la misma métrica no se volverá a evaluar, y el estado de la alerta permanecerá sin cambios. 
  
    Cuando finalmente llegan datos, el gráfico se desplazará a un valor distinto de cero. La alerta se evaluará en función de los datos disponibles para el período especificado. Si el nuevo punto de datos es el único disponible en el período, el agregado se basará en ese punto.
* Una alerta puede parpadear con frecuencia entre los estados de alerta y correcto, incluso si se establece un período largo. Esto puede suceder si el valor de métrica se sitúa alrededor del umbral. No hay ninguna histéresis en el umbral: la transición a alerta se produce en el mismo valor que la transición a correcto.

## <a name="what-are-good-alerts-to-set"></a>¿Qué alertas es conveniente establecer?
Depende de la aplicación. Para empezar, es mejor no establecer demasiadas métricas. Observe durante un tiempo sus gráficos de métrica mientras se ejecuta la aplicación para hacerse una idea de cómo se comporta normalmente. Este procedimiento le ayudará a encontrar maneras de mejorar su rendimiento. A continuación, configure alertas para que le avisen cuando las métricas salgan de la zona normal. 

Las alertas más populares son:

* Las [métricas del explorador][client], especialmente los **tiempos de carga de página del explorador**, son buenas para aplicaciones web. Si la página tiene muchos scripts, debe buscar **excepciones del explorador**. Para obtener estas métricas y alertas, tiene que configurar [la supervisión de páginas web][client].
* **Tiempo de respuesta del servidor** para las aplicaciones web del lado servidor. Además de configurar alertas, eche un vistazo a esta métrica para ver si varía desproporcionadamente con tasas de solicitud altas: la variación puede indicar que la aplicación se está quedando sin recursos. 
* **Excepciones de servidor** : para verlas, deberá realizar alguna [configuración adicional](app-insights-asp-net-exceptions.md).

No olvide que los [diagnósticos proactivos de frecuencia de errores](app-insights-proactive-failure-diagnostics.md) permiten supervisar automáticamente la velocidad a la que la aplicación responde a solicitudes con códigos de error. 

## <a name="automation"></a>Automatización
* [Uso de PowerShell para automatizar la configuración de alertas](app-insights-powershell-alerts.md)
* [Uso de Webhook para automatizar la respuesta a alertas](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="see-also"></a>Otras referencias
* [Pruebas web de disponibilidad](app-insights-monitor-web-app-availability.md)
* [Use PowerShell to set alerts in Application Insights (Uso de PowerShell para definir alertas en Application Insights)](app-insights-powershell-alerts.md)
* [Proactive diagnostics](app-insights-proactive-diagnostics.md) 

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

