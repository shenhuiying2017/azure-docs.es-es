---
title: Administración del costo de datos en Azure Log Analytics | Microsoft Docs
description: Aprenda cómo cambiar el plan de precios y cómo administrar el volumen de datos y la directiva de retención para el área de trabajo de Log Analytics en Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: magoedte
ms.openlocfilehash: 0e4c4c9e950610526a29e02d70827a1279d9686a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="manage-cost-by-controlling-data-volume-and-retention-in-log-analytics"></a>Administración de los costos mediante el control del volumen de datos y la retención en Log Analytics
Log Analytics está diseñado para escalar y admitir la recopilación, indexación y almacenamiento de grandes cantidades de datos por día provenientes de cualquier origen dentro de su empresa o implementados en Azure.  Aunque esto puede ser un factor clave principal para su organización, la rentabilidad es en última instancia el factor clave subyacente. Con este fin, es importante comprender que el costo de un área de trabajo de Log Analytics no se basa simplemente en el volumen de datos recopilados, también depende del plan seleccionado y de cuánto tiempo se decida almacenar los datos generados a partir de los orígenes conectados.  

En este artículo revisamos cómo puede supervisar de forma proactiva el crecimiento del volumen y del almacenamiento de datos, y definir los límites para controlar esos costos asociados. 

El costo de datos puede ser considerable dependiendo de los factores siguientes: 

- Número de sistemas, componentes de la infraestructura, recursos en la nube, etc. desde los que se efectúa la recopilación 
- Tipo de datos creados por el origen, como las colas de mensajes, los registros, los eventos, los datos relacionados con la seguridad o las métricas de rendimiento 
- Volumen de datos generado por estos orígenes e ingeridos en el área de trabajo 
- Período durante el que los datos se retengan en el área de trabajo  
- Número de soluciones de administración habilitadas, origen de datos y frecuencia de recopilación 

> [!NOTE]
> Consulte la documentación de cada solución, ya que proporciona una estimación de la cantidad de datos que recopila.   

Si se encuentra en un plan *Gratis*, los datos tienen una retención limitada de siete días. En el plan *Independiente* o *De pago*, los datos recopilados están disponibles durante los últimos 31 días. El plan *Gratis* tiene 500 MB diarios de límite de ingesta, si observa que supera de forma uniforme las cantidades permitidas de volumen, puede cambiar el área de trabajo a un plan de pago para recopilar datos más allá de este límite. 

> [!NOTE]
> Pueden aplicarse cargos si elige seleccionar un período de retención mayor para el nivel de pago. Puede cambiar el tipo de plan en cualquier momento. Para obtener más información sobre los precios, vea la [información sobre los precios](https://azure.microsoft.com/pricing/details/log-analytics/). 

Hay dos maneras para limitar el volumen de datos y ayudar a controlan el costo, son el límite diario y la retención de datos.  

## <a name="review-estimated-cost"></a>Revisión del costo estimado
Log Analytics permite entender fácilmente lo que los costos probablemente vayan a ser, en base a los patrones de uso reciente.  Para ello, siga estos pasos.  

1. Inicie sesión en el [Portal de Azure](http://portal.azure.com). 
2. En Azure Portal, haga clic en **Todos los servicios**. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Log Analytics**.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
3. En el panel de suscripciones de Log Analytics, seleccione el área de trabajo y, a continuación, haga clic en **Usage and estimated costs** (Uso y costos estimados) en el panel izquierdo.<br><br> ![Página Usage and estimated costs (Uso y costos estimados)](media/log-analytics-manage-cost-storage/usage-estimated-cost-dashboard-01.png)<br>

Desde aquí puede revisar el volumen de datos para el mes. Esto incluye todos los datos recibidos y retenidos en el área de trabajo de Log Analytics.  Haga clic en **Detalles de uso** en la parte superior de la página para ver el panel de uso con información sobre las tendencias de volumen de datos por origen, equipos y oferta. Para ver y establecer un límite diario o para modificar el período de retención, haga clic en **Administración del volumen de datos**.
 
Los cargos de Log Analytics se agregarán a la factura de Azure. Puede consultar los detalles de su factura de Azure en la sección de facturación de Azure Portal o en el [Portal de facturación de Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Límite diario
Al crear un área de trabajo de Log Analytics desde Azure Portal y elegir el plan *Gratis*, el límite por día se establece en 500 MB. No hay ningún límite para otros planes de precios. Puede configurar un límite diario y limitar la ingesta diaria para el área de trabajo, pero tenga cuidado de establecer el límite diario como el objetivo al que llegar.  En caso contrario, perderá los datos para el resto del día, lo que puede afectar a otros servicios y soluciones de Azure cuya funcionalidad puede depender de la disponibilidad de datos actualizados en el área de trabajo.  Como resultado, esto afecta a la capacidad de observar y recibir alertas cuando cambian las condiciones de mantenimiento de los recursos que respaldan los servicios de TI.  El límite diario está pensado para usarse como una manera de administrar el aumento inesperado del volumen de datos de los recursos administrados y permanecer dentro de su límite, o simplemente cuando desee limitar cargos no planeados para el área de trabajo.  

Cuando se alcanza el límite diario, la recopilación de tipos de datos facturables se detiene durante el resto del día. Un mensaje emergente de advertencia aparece en la parte superior de la página del área de trabajo de Log Analytics seleccionada, y se envía un evento de operación para la tabla *Operación* en la categoría **LogManagement**. La recopilación de datos se reanuda después de que se restablezca el tiempo definido en *Daily limit will be set at* (El límite diario se establecerá en). Se recomienda definir una regla de alerta en función de este evento de operación que esté configurada para notificar cuando se ha alcanzado el límite diario de datos. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificación del límite diario de datos para definir 
Revise el [uso de Log Analytics y costos estimados](log-analytics-usage.md) para comprender la tendencia de ingesta de datos y cuál es el límite de volumen diario para definir. Se debe considerar con cuidado, ya que no podrá supervisar los recursos una vez que se alcance el límite. 

### <a name="manage-the-maximum-daily-data-volume"></a>Administración del volumen de datos diario máximo 
Los pasos siguientes describen cómo configurar un límite para administrar el volumen de datos que Log Analytics ingiere por día.  

1. En el área de trabajo seleccione **Usage and estimated costs** (Uso y costos estimados) en el panel izquierdo.
2. En la página **Usage and estimated costs** (Uso y costos estimados) del área de trabajo seleccionada, haga clic en **Administración del volumen de datos** en la parte superior de la página. 
5. El límite diario está en **OFF** (Desactivado) de manera predeterminada, haga clic en **ON** (Activado) para habilitarlo y luego establezca el límite de volumen de datos en GB/día.<br><br> ![Configuración del límite de datos con Log Analytics](media/log-analytics-manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-limit-reached"></a>Alerta cuando se alcanza el límite
Aunque Azure Portal presenta una indicación visual cuando se alcanza el umbral de límite de datos, este comportamiento no tiene que alinearse necesariamente con la forma en la que el usuario administra los problemas de funcionamiento que requieren atención inmediata.  Para recibir una notificación de alerta, puede crear una nueva regla de alerta en Azure Monitor.  Para más información, consulte [cómo crear, ver y administrar alertas](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).      

Para comenzar, esta es la configuración recomendada para la alerta:

* Destino: Seleccione el recurso de Log Analytics
* Criterios: 
   * Nombre de señal: búsqueda de registros personalizada
   * Consulta de búsqueda: operación | donde Detalle tiene "OverQuota"
   * Basada en: número de resultados
   * Condición: mayor que
   * Umbral: 0
   * Período: 5 (minutos)
   * Frecuencia: 5 (minutos)
* Nombre de regla de alerta: Límite de datos diario alcanzado
* Gravedad: Advertencia (gravedad 1)

Una vez que se define la alerta y se alcanza el límite, se desencadena una alerta que realiza la respuesta que se define en el grupo de acciones. Puede notificar a su equipo a través de mensajes de correo electrónico y de texto, o automatizar acciones mediante webhooks, runbooks de Automation o [integrar con una solución de ITSM externa](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Cambio del período de retención de datos 
Los pasos siguientes describen cómo configurar cuánto tiempo se conservan los datos de registro en el área de trabajo.
 
1. En el área de trabajo seleccione **Usage and estimated costs** (Uso y costos estimados) en el panel izquierdo.
2. En la página **Usage and estimated costs** (Uso y costos estimados), haga clic en **Administración del volumen de datos** en la parte superior de la página.
5. En el panel, mueva el control deslizante para aumentar o disminuir el número de días y, luego, haga clic en **Aceptar**.  Si el nivel es *gratuito*, no podrá modificar el período de retención de datos y tendrá que actualizar al nivel de pago para controlar esta configuración.<br><br> ![Cambio de la configuración de retención de datos del área de trabajo](media/log-analytics-manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="troubleshooting"></a>solución de problemas
**Pregunta**: ¿Qué solución hay si Log Analytics ya no recopila datos? 
**Respuesta**: Si tiene el plan de tarifa Gratis y ha enviado más de 500 MB de datos en un día, la recopilación de datos se detiene durante el resto del día. Alcanzar el límite diario es un motivo frecuente de que Log Analytics deje de recopilar datos o de que parezca que faltan datos.  
Log Analytics crea un evento de tipo Operación cuando la recopilación de datos se inicia y se detiene.  
Ejecute la siguiente consulta en la búsqueda para comprobar si ha alcanzado el límite diario y faltan datos: Operación | donde OperationCategory == "Estado de recolección de datos"   
Cuando se detiene la recopilación de datos, OperationStatus es Advertencia. Cuando se inicia la recopilación de datos, OperationStatus es Correcto.  
La tabla siguiente describe los motivos por los que se detiene la recopilación de datos y una acción recomendada para reanudarla:  

|Motivo por el que se detiene la recopilación| Solución| 
|-----------------------|---------|
|Se ha alcanzado el límite diario de datos gratuitos<sup>1</sup>|Espere hasta el día siguiente para que la recopilación se reinicie automáticamente, o cambie a un plan de tarifa de pago.|
|Se ha alcanzado el límite diario definido en la administración de volumen de datos|Espere hasta el día siguiente para que se reinicie automáticamente la recopilación o aumente el límite diario de volumen de datos que se describe en [Administración del volumen de datos diario máximo](#manage-the-maximum-daily-volume)|
|La suscripción de Azure está en estado suspendido debido a:<br> Prueba gratuita finalizada<br> Pase para Azure expirado<br> Se ha alcanzado el límite de gasto mensual (por ejemplo, en una suscripción de MSDN o Visual Studio)|Cambie a una suscripción de pago<br> Quite el límite o espere a que se restablezca|

<sup>1</sup> Si el área de trabajo tiene el plan de tarifa gratuito, el envío de datos diario se limita a 500 MB. Cuando se alcanza el límite diario, la recopilación de datos se detiene hasta el día siguiente. Los datos enviados mientras la recopilación de datos está detenida no se indexan ni están disponibles para las búsquedas. Cuando la recopilación de datos se reanuda, únicamente se procesan los nuevos datos enviados. 

Log Analytics usa la hora UTC. La hora de restablecimiento varía en las diferentes áreas de trabajo para evitar que todas las áreas de trabajo limitadas empiecen a ingerir datos al mismo tiempo. Si el área de trabajo alcanza el límite diario, el procesamiento se reanudará una vez definido el tiempo de restablecimiento en **Daily limit will be set at** (El límite diario se establecerá en).<br><br> ![Zona horaria UTC para el límite de Log Analytics](media/log-analytics-manage-cost-storage/data-volume-mgmt-limit-utc.png)

**Pregunta**: ¿Cómo puedo recibir una notificación cuando se detiene la recopilación de datos? 
**Respuesta**: Siga los pasos descritos para *Crear una alerta para el límite de datos diario* para recibir una notificación cuando se detenga la recopilación de datos, y siga los pasos descritos en la incorporación de acciones a las reglas de alerta con la configuración de una acción de correo electrónico, webhook o runbook para la regla de alerta. 

## <a name="next-steps"></a>Pasos siguientes  

Para determinar la cantidad de datos recopilada, qué orígenes los envían y los diferentes tipos de datos enviados para ayudar a administrar el consumo y el costo, consulte [Análisis del uso de datos en Log Analytics](log-analytics-usage.md).