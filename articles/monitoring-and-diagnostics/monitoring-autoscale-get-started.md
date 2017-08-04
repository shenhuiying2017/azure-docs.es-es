---
title: "Introducción al escalado automático en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo escalar los recursos en Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: ancav
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ed6647979d33564afe68e46d52833f88ffa2fbda
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="get-started-with-auto-scale-in-azure"></a>Introducción al escalado automático en Azure
Este artículo describe cómo configurar el escalado automático de recursos en Azure Portal.

El escalado automático de Azure Monitor solo se aplica a Conjuntos de escalado de máquinas virtuales, Cloud Services, planes de App Service y App Service Environment. 

## <a name="discover-the-auto-scale-settings-in-your-subscriptions"></a>Detección de la configuración de escalado automático en las suscripciones
Puede detectar todos los recursos a los que se aplica el escalado automático en Azure Monitor. Siga los pasos indicados a continuación para ver un tutorial paso a paso.

- Abra [Azure Portal][1].
- Haga clic en el icono de Azure Monitor en el panel de navegación izquierdo.
  ![Inicio de Azure Monitor][2]
- Haga clic en la opción Escalado automático para ver todos los recursos a los que se aplica el escalado automático, junto con su estado de escalado automático actual ![Detección de escalado automático en Azure Monitor][3]

Puede utilizar el panel de filtro de la parte superior para limitar la lista a los recursos seleccionados de un grupo de recursos específico, seleccionar tipos de recursos concretos o seleccionar un recurso determinado.

Para cada recurso, encontrará el recuento de instancias actual y su estado de escalado automático. El estado de escalado automático puede ser:

- No configurado: aún no se ha habilitado la configuración de escalado automático para este recurso.
- Habilitado: se ha habilitado la configuración de escalado automático para este recurso.
- Deshabilitado: se ha deshabilitado la configuración de escalado automático para este recurso.

## <a name="create-your-first-auto-scale-setting"></a>Creación de la primera configuración de escalado automático

Se va a ofrecer ahora un tutorial paso a paso sencillo sobre cómo crear la primera configuración de escalado automático.

- Abra la hoja "Escalado automático" en Azure Monitor y seleccione un recurso que desee escalar. (En los pasos siguientes se usa un plan de App Service asociado con una aplicación web. Puede [crear su primera aplicación web de ASP.NET en Azure en cinco minutos][4]).
- En la hoja de configuración de escalado del recurso, tenga en cuenta que el recuento de la instancia actual es 1. Haga clic en "Enable autoscale" (Habilitar escalado automático).
  ![Configuración de escalado para la nueva aplicación web][5]
- Proporcione un nombre para la configuración de escalado y haga clic en "Agregar una regla". Tenga en cuenta que las opciones de la regla de escalado se abren como un panel Contexto en el lado derecho. De forma predeterminada, establece la opción para escalar el recuento de instancias en 1 si el porcentaje de CPU del recurso supera el 70 %. Deje los valores predeterminados y haga clic en Agregar.
  ![Crear configuración de escalado para una aplicación web][6]
- Ahora ha creado su primera regla de escalado. Observe que UX ofrece procedimientos recomendados e indica que "se recomienda tener al menos una regla de reducción horizontal". Para ello, haga clic en "Agregar una regla" y establezca el "Operador" en "Menos que", el "Umbral" en "20" y la "Operación" en "Reducir recuento en". Ahora debería tener una configuración de escalado que escale o reduzca horizontalmente en función del uso de CPU.
  ![Escala en función de la CPU][8]
- Haga clic en "Guardar".

¡Enhorabuena! Ya ha establecido correctamente la primera configuración del escalado para escalar automáticamente la aplicación web en función del uso de CPU.

> Nota: Los mismos pasos son aplicables para empezar a trabajar con un rol de servicio en la nube o VMSS.

## <a name="other-considerations"></a>Otras consideraciones
### <a name="scale-based-on-a-schedule"></a>Escalación según una programación
Además de escalar siempre en función de la CPU, también puede establecer la escala de forma diferente para días específicos de la semana.

- Haga clic en "Add a scale condition" (Agregar una condición de escalado).
- Establezca el modo y las reglas de escalado de la misma forma que la condición predeterminada.
- Seleccione "Repeat specific days" (Repetir días específicos) para la programación.
- Seleccione los días y la hora de inicio y fin en que debe aplicarse la condición de escalado en los días seleccionados.

![Condición de escalado basada en programación][9]
### <a name="scale-differently-on-specific-dates"></a>Escalado distinto en fechas concretas
Además de escalar siempre en función de la CPU, también puede establecer la escala de forma diferente para fechas específicas.

- Haga clic en "Add a scale condition" (Agregar una condición de escalado).
- Establezca el modo y las reglas de escalado de la misma forma que la condición predeterminada.
- Seleccione "Specify start/end dates" (Especificar fechas de inicio y fin) para la programación.
- Seleccione las fechas de inicio y fin y la hora de inicio y fin en que debe aplicarse la condición de escalado en las fechas seleccionadas.

![Condición de escalado basada en fechas][10]

### <a name="view-the-scale-history-of-your-resource"></a>Consulta del historial de escalado de un recurso
Cada vez que un recurso se escala o reduce verticalmente, se registra un evento en el registro de actividad. Puede consultar el historial de escalado del recurso de las últimas 24 horas si cambia a la pestaña "Historial de ejecución".

![Historial de ejecuciones][11]

Si desea consultar el historial de escalado completo (de hasta 90 días), puede hacer clic en "Haga clic aquí para ver más detalles". De esta forma, se inicia el registro de actividad de recurso y se selecciona previamente la categoría como "escalado automático".

### <a name="view-the-scale-definition-of-the-resource"></a>Consulta de la definición de escalado del recurso
La configuración de escalado automático es un recurso de ARM. Puede ver la definición de escala en JSON al cambiar a la pestaña "JSON".

![Definición de escala][12]

Puede realizar cambios en JSON directamente, si es necesario. Estos cambios se reflejarán al guardar.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Deshabilitar el escalado automático y escalar manualmente las instancias
Pueden darse ocasiones en que desee deshabilitar la configuración actual de escalado y escalar los recursos manualmente.

Haga clic en el botón "Deshabilitar escalado automático" en la parte superior.
![Deshabilitar escalado automático][13]

Tenga en cuenta que esta opción deshabilita la configuración, pero puede recuperarla al habilitar el escalado automático de nuevo. Ahora puede establecer el número de instancias a las que desea escalar manualmente.

![Definición manual de escalado][14]

Siempre se puede volver al escalado automático; para ello, haga clic en "Enable autoscale" (Habilitar escalado automático) y luego haga clic en "Save" (Guardar).

## <a name="next-steps"></a>Pasos siguientes
- [Creación de una alerta de registro de actividades para supervisar todas las operaciones del motor de escalado automático en su suscripción](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Creación de una alerta de registro de actividades para supervisar todas las operaciones con errores de reducción y escalado horizontal automático en su suscripción](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/monitoring-autoscale-get-started/azure-monitor-launch.png
[3]: ./media/monitoring-autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet
[5]: ./media/monitoring-autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/monitoring-autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/monitoring-autoscale-get-started/scale-in-recommendation.png
[8]: ./media/monitoring-autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/monitoring-autoscale-get-started/scale-condition-schedule.png
[10]: ./media/monitoring-autoscale-get-started/scale-condition-dates.png
[11]: ./media/monitoring-autoscale-get-started/scale-history.png
[12]: ./media/monitoring-autoscale-get-started/scale-definition-json.png
[13]: ./media/monitoring-autoscale-get-started/disable-autoscale.png
[14]: ./media/monitoring-autoscale-get-started/set-manualscale.png

