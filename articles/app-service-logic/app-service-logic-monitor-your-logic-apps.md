<properties 
	pageTitle="Supervisar aplicaciones lógicas en el Servicio de aplicaciones de Azure | Microsoft Azure" 
	description="Visualización de lo que han hecho las aplicaciones lógicas" 
	authors="jeffhollan" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2016"
	ms.author="jehollan"/>

# Supervisión de aplicaciones lógicas

Tras [crear una aplicación lógica](app-service-logic-create-a-logic-app.md), podrá ver el historial completo de su ejecución en el Portal de Azure. También puede configurar servicios, como Diagnósticos de Azure y alertas de Azure, para supervisar eventos en tiempo real y avisarle en el caso de eventos como "cuando más de 5 ejecuciones produzcan error en una hora".

## Supervisión en el Portal de Azure

Para ver el historial, seleccione **Examinar** y, luego, **Logic Apps**. Aparecerá una lista con todas las aplicaciones lógicas incluidas en la suscripción. Seleccione la aplicación lógica que quiere supervisar. Verá una lista de todas las acciones y desencadenadores que se han producido para esta aplicación lógica.

![Información general](./media/app-service-logic-monitor-your-logic-apps/overview.png)

Hay algunas secciones de esta hoja que resultan útiles:

- En **Resumen** se muestran **todas las ejecuciones** y **historial de desencadenadores**.
	- En **Todas las ejecuciones** se muestran las ejecuciones de aplicaciones lógicas más recientes. Puede hacer clic en cualquier fila para ver detalles sobre la ejecución o hacer clic en el icono para mostrar más ejecuciones.
	- En **Historial de desencadenadores** se muestra toda la actividad relacionada con los desencadenadores en esta aplicación lógica. Por ejemplo, podría ser una comprobación omitida de nuevos datos (al buscar si se ha agregado un nuevo archivo FTP), los datos que se han devuelto correctamente para activar una aplicación lógica o los errores de configuración.
- El servicio **Diagnósticos** le permite ver detalles y eventos en tiempo de ejecución y suscribirse a [alertas de Azure](#adding-azure-alerts).

### Visualización de los detalles de ejecución

Esta lista de ejecuciones muestra el **estado**, la **hora de inicio** y la **duración** de una ejecución en particular. Seleccione cualquier fila para ver detalles sobre esa ejecución.

La vita de supervisión muestra cada paso de la ejecución, las entradas y salidas y los mensajes de error que se puedan haber producido.

![Ejecuciones y acciones](./media/app-service-logic-monitor-your-logic-apps/monitor-view.png)

Si necesita detalles adicionales, como el **id. de correlación** de la ejecución (que puede usarse para la API de REST), puede hacer clic en el botón **Detalles de ejecución**. Aquí se incluyen todos los pasos, el estado y las entradas y salidas de la ejecución.

## Diagnósticos y alertas de Azure

Además de los detalles que se proporcionan en el Portal de Azure y la API de REST que acabamos de ver, puede configurar la aplicación lógica para que utilice Diagnósticos de Azure para más información y realizar la depuración.

1. Haga clic en la sección **Diagnósticos** de la hoja Aplicación lógica.
1. Haga clic aquí para configurar los **valores de diagnóstico**.
1. Configure un Centro de eventos o una cuenta de almacenamiento a los que emitir datos.

	![Configuración de Diagnósticos de Azure](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

### Agregar alertas de Azure

Después de que se han configurado los diagnósticos, puede agregar alertas de Azure para que se activen cuando se sobrepasen determinados umbrales. En la hoja **Diagnósticos**, seleccione el icono **Alertas** y **Agregar alerta**. Aquí podrá configurar una alerta según una serie de umbrales y métricas.

![Métricas de alertas de Azure](./media/app-service-logic-monitor-your-logic-apps/alerts.png)

Puede configurar la **condición**, el **umbral** y el **período** tal como se desee. Por último, puede configurar una dirección de correo electrónico a la que enviar notificaciones o un webhook. Puede usar el [desencadenador de solicitud](../connectors/connectors-native-reqres.md) en una aplicación lógica para que se ejecute también en una alerta (para, por ejemplo, [publicar en Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app), [enviar un texto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app) o [agregar un mensaje a una cola](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)).

### Configuración de Diagnósticos de Azure

Cada uno de estos eventos contiene detalles sobre la aplicación lógica, como el estado. Este es un ejemplo de un evento *ActionCompleted*:

```javascript
{
			"time": "2016-07-09T17:09:54.4773148Z",
			"workflowId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
			"resourceId": "/SUBSCRIPTIONS/80D4FE69-ABCD-EFGH-A938-9250F1C8AB03/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
			"category": "WorkflowRuntime",
			"level": "Information",
			"operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
			"properties": {
				"$schema": "2016-06-01",
				"startTime": "2016-07-09T17:09:53.4336305Z",
				"endTime": "2016-07-09T17:09:53.5430281Z",
				"status": "Succeeded",
				"code": "OK",
				"resource": {
					"subscriptionId": "80d4fe69-ABCD-EFGH-a938-9250f1c8ab03",
					"resourceGroupName": "MyResourceGroup",
					"workflowId": "cff00d5458f944d5a766f2f9ad142553",
					"workflowName": "MyLogicApp",
					"runId": "08587361146922712057",
					"location": "eastus",
					"actionName": "Http"
				},
				"correlation": {
					"actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
					"clientTrackingId": "my-custom-tracking-id"
				},
				"trackedProperties": {
					"myProperty": "<value>"
				}
			}
		}
```

Las dos propiedades que son especialmente útiles para el seguimiento y la supervisión son *clientTrackingId* y *trackedProperties*.

#### Id. de seguimiento de cliente

El id. de seguimiento de cliente es un valor que realiza una correlación entre los eventos de una ejecución de aplicación lógica. Esto incluye los flujos de trabajo anidados a los que se llama como parte de una aplicación lógica. Si no se proporciona, este id. se genera automáticamente, pero puede especificar manualmente el id. de seguimiento del cliente desde un desencadenador si pasa un encabezado `x-ms-client-tracking-id` con el valor de id. en la solicitud del desencadenador (desencadenador de solicitud, desencadenador de HTTP o desencadenador de webhook).

#### Propiedades controladas

Se pueden agregar propiedades controladas a acciones en la definición del flujo de trabajo para realizar el seguimiento de las entradas o salidas de los datos de diagnóstico. Esto puede ser útil si desea realizar un seguimiento de los datos, por ejemplo, un "id. de pedido" en los datos de telemetría. Para agregar una propiedad controlada, incluya la propiedad `trackedProperties` en una acción. Las propiedades controladas solo pueden realizar el seguimiento de entradas y salidas de acciones individuales; sin embargo, puede usar las propiedades `correlation` de los eventos para crear correlaciones entre las acciones de una ejecución.

```javascript
{
	"myAction": {
		"type": "http",
		"inputs": {
			"uri": "http://uri",
			"headers": {
				"Content-Type": "application/json"
			},
			"body": "@triggerBody()"
		},
		"trackedProperties":{
			"myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
			"myActionHTTPValue": "@action()['outputs']['body']['foo']",
			"transactionId": "@action()['inputs']['body']['bar']"
		}
	}
}
```

### Ampliación de las soluciones

Puede aprovechar esta información de telemetría del Centro de eventos o del almacenamiento en otros servicios, por ejemplo, [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite), [Análisis de transmisiones de Azure](https://azure.microsoft.com/services/stream-analytics/) y [Power BI](https://powerbi.com) para supervisar en tiempo real los flujos de trabajo de integración.

## Pasos siguientes
- [Ejemplos y escenarios habituales de las aplicaciones lógicas](app-service-logic-examples-and-scenarios.md)
- [Creación de una implementación de aplicación lógica](app-service-logic-create-deploy-template.md)
- [Características de Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md)

<!---HONumber=AcomDC_0727_2016-->