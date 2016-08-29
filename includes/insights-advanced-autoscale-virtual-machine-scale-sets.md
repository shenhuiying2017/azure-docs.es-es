# Configuración avanzada de escalado automático con plantillas de Resource Manager para conjuntos de escala de máquina virtual

Puede escalar y reducir horizontalmente los conjuntos de escala de máquina virtual según umbrales de métricas de rendimiento, siguiendo una programación periódica o por una fecha determinada. También puede configurar notificaciones de correo electrónico y webhook para las acciones de escalado. Este tutorial muestra un ejemplo de configuración de todo lo anterior utilizando una plantilla de Resource Manager en un conjunto de escala de máquina virtual.

>[AZURE.NOTE] Aunque este tutorial explica los pasos para los conjuntos de escala de máquina virtual, lo mismo se puede aplicar al escalado automático de los servicios en la nube y las aplicaciones web. Para tener un valor simple de escalado y reducción horizontal en un conjunto de escala de máquina virtual basado en una métrica de rendimiento simple como CPU, consulte los documentos sobre [Linux](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) y [Windows](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).



## Tutorial
En este tutorial, usaremos el [Explorador de recursos de Azure](https://resources.azure.com/) para configurar y actualizar el valor de escalado automático para un conjunto de escala. El Explorador de recursos de Azure es una manera fácil de administrar los recursos de Azure mediante las plantillas de Resource Manager. Si no está familiarizado con la herramienta Explorador de recursos de Azure, lea [esta introducción](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Implemente un nuevo conjunto de escala con un valor de escalado automático básico. Este artículo utiliza uno de la Galería de inicio rápido de Azure, que tiene un conjunto de escala de Windows con una plantilla básica de escalado automático. Los conjuntos de escala de Linux funcionan del mismo modo.

2. Una vez creado el conjunto de escala, navegue al recurso del mismo desde el Explorador de recursos de Azure. En el nodo de Microsoft.Insights aparece lo siguiente.

	![Azure Explorer](./media/insights-advanced-autoscale-vmss/azure_explorer_navigate.png)

	La ejecución de la plantilla crea un valor de escalado automático de forma predeterminada con el nombre **'autoscalewad'**. En el lado derecho puede ver la definición completa de este valor de escalado automático. En este caso, el valor de escalado automático predeterminado viene con una regla de escalado horizontal y otra de reducción horizontal, ambas basadas en el % de CPU.

3. Ahora puede agregar más perfiles y reglas basadas en la programación o requisitos específicos. Creamos una configuración de escalado automático con tres perfiles. Para comprender los perfiles y las reglas de escalado automático, revise el artículo [Procedimientos recomendados de escalado automático](../articles/azure-portal/insights-autoscale-best-practices.md).

    | Perfiles y reglas | Description |
	|---------|-------------------------------------|
	| **Perfil** | **Basado en rendimiento/métrica** |
	| Regla | Recuento de mensajes de cola de Bus de servicio > x |
	| Regla | Recuento de mensajes de cola de Bus de servicio > y |
	| Regla | % de CPU,< n |
	| Regla | % de CPU < p |
	| **Perfil** | **Horas de la mañana días de semana, (no hay reglas)** |
	| **Perfil** | **Día de lanzamiento de producto (no hay reglas)** |

4. Este es un escenario hipotético de escalado que se utiliza para este tutorial.
	- _**Basado en la carga**: quisiera escalar o reducir horizontalmente según la carga en la aplicación hospedada en mi conjunto de escala._
	- _**Tamaño de cola de mensajes**: uso una cola de Bus de servicio para los mensajes entrantes a la aplicación. Uso el % de CPU y el recuento de la cola de mensajes y configuro un perfil predeterminado para desencadenar una acción de escalado si el número de mensajes o la CPU alcanzan el umbral._
	- _**Horario de la semana y el día**: quiero un perfil periódico semanal basado en la "hora del día" llamado "Horas de la mañana en semana". Según los datos históricos, ya sé que es mejor tener cierto número de instancias de máquina virtual para administrar la carga de la aplicación durante este tiempo._
	- _**Fechas especiales**: agrego un perfil de "Día de lanzamiento de producto". Planeo con antelación para fechas específicas, de forma que mi aplicación esté preparada para controlar la carga que se produce con los anuncios de marketing y cuando se coloca un nuevo producto en la aplicación._
	- _Los dos últimos perfiles también pueden contener otras reglas basadas en métricas de rendimiento. En este caso, decidí no tener ninguno y confiar en las reglas basadas en las métricas de rendimiento predeterminadas. Las reglas son opcionales para los perfiles periódico y basado en la fecha._

	La asignación de prioridades de los perfiles y las reglas que realiza el motor de escalado automático también se explica en el artículo [Procedimientos recomendados de escalado automático en Azure Insights](../articles/azure-portal/insights-autoscale-best-practices.md). Para ver una lista de las métricas más comunes para el escalado automático, consulte [Métricas comunes de escalado automático de Azure Insights](../articles/azure-portal/insights-autoscale-common-metrics.md).

5. Asegúrese de que se encuentra en el modo de **lectura y escritura** en el Explorador de recursos

	![Autoscalewad, valor de escalado automático predeterminado](./media/insights-advanced-autoscale-vmss/autoscalewad.png)

6. Haga clic en Editar. **Reemplace** el elemento "perfiles" en la configuración de escalado automático con lo siguiente:

	![perfiles](./media/insights-advanced-autoscale-vmss/profiles.png)

	```
	{
	        "name": "Perf_Based_Scale",
	        "capacity": {
	          "minimum": "2",
	          "maximum": "12",
	          "default": "2"
	        },
	        "rules": [
	          {
	            "metricTrigger": {
	              "metricName": "MessageCount",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT5M",
	              "timeAggregation": "Average",
	              "operator": "GreaterThan",
	              "threshold": 10
	            },
	            "scaleAction": {
	              "direction": "Increase",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          },
	          {
	            "metricTrigger": {
	              "metricName": "MessageCount",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT5M",
	              "timeAggregation": "Average",
	              "operator": "LessThan",
	              "threshold": 3
	            },
	            "scaleAction": {
	              "direction": "Decrease",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          },
	          {
	            "metricTrigger": {
	              "metricName": "\\Processor(_Total)\\% Processor Time",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT30M",
	              "timeAggregation": "Average",
	              "operator": "GreaterThan",
	              "threshold": 85
	            },
	            "scaleAction": {
	              "direction": "Increase",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          },
	          {
	            "metricTrigger": {
	              "metricName": "\\Processor(_Total)\\% Processor Time",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT30M",
	              "timeAggregation": "Average",
	              "operator": "LessThan",
	              "threshold": 60
	            },
	            "scaleAction": {
	              "direction": "Increase",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          }
	        ]
	      },
	      {
	        "name": "Weekday_Morning_Hours_Scale",
	        "capacity": {
	          "minimum": "4",
	          "maximum": "12",
	          "default": "4"
	        },
	        "rules": [],
	        "recurrence": {
	          "frequency": "Week",
	          "schedule": {
	            "timeZone": "Pacific Standard Time",
	            "days": [
	              "Monday",
	              "Tuesday",
	              "Wednesday",
	              "Thursday",
	              "Friday"
	            ],
	            "hours": [
	              6
	            ],
	            "minutes": [
	              0
	            ]
	          }
	        }
	      },
	      {
	        "name": "Product_Launch_Day",
	        "capacity": {
	          "minimum": "6",
	          "maximum": "20",
	          "default": "6"
	        },
	        "rules": [],
	        "fixedDate": {
	          "timeZone": "Pacific Standard Time",
	          "start": "2016-06-20T00:06:00Z",
	          "end": "2016-06-21T23:59:00Z"
	        }
	      }
	```
	Para los campos compatibles y sus valores, consulte [Creación o actualización de una configuración de escalado automático](https://msdn.microsoft.com/es-ES/library/azure/dn931928.aspx).

	La configuración de escalado automático contiene ahora los tres perfiles explicados anteriormente.

7. 	Por último, echemos un vistazo a la sección de **notificación** del escalado automático. Las notificaciones de escalado automático le permiten hacer tres cosas cuando se desencadena correctamente un escalado o una reducción horizontal.

	1. Notificar a los administradores y coadministradores de su suscripción

	2. Enviar un correo electrónico a un conjunto de usuarios

	3. Desencadenar una llamada de webhook. Cuando se desencadena, este webhook envía metadatos sobre la condición de escalado automático y el recurso de conjunto de escala. Para más información acerca de la carga de escalado automático de webhook, consulte [Uso de acciones de escalado automático para enviar notificaciones de alerta por correo electrónico y Webhook en Azure Insights](../articles/azure-portal/insights-autoscale-to-webhook-email.md).

	Agregue lo siguiente al valor de escalado automático para reemplazar el elemento de **notificación** cuyo valor es null

	```
	"notifications": [
	      {
	        "operation": "Scale",
	        "email": {
	          "sendToSubscriptionAdministrator": true,
	          "sendToSubscriptionCoAdministrators": false,
	          "customEmails": [
	              "user1@mycompany.com",
	              "user2@mycompany.com"
	              ]
	        },
	        "webhooks": [
	          {
	            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
	            "properties": {
	              "optional_key1": "optional_value1",
	              "optional_key2": "optional_value2"
	            }
	          }
	        ]
	      }
	    ]

	```

	Presione el botón **Put** botón en el Explorador de recursos para actualizar el valor de escalado automático.

Ha actualizado un valor de escalado automático en un conjunto de escala de máquina virtual para incluir varios perfiles y notificaciones de escalado.

## Pasos siguientes

Siga estos vínculos para más información sobre el escalado automático:

[Métricas comunes de escalado automático de Azure Insights](../articles/azure-portal/insights-autoscale-common-metrics.md)

[Procedimientos recomendados de escalado automático en Azure Insights](../articles/azure-portal/insights-autoscale-best-practices.md)

[Creación y administración de la configuración de escalado automático](../articles/azure-portal/insights-powershell-samples.md#create-and-manage-autoscale-settings)

[Administración del escalado automático con CLI](../articles/azure-portal/insights-cli-samples.md#autoscale)

[Uso de acciones de escalado automático para enviar notificaciones de alerta por correo electrónico y Webhook en Azure Insights](../articles/azure-portal/insights-autoscale-to-webhook-email.md)

<!---HONumber=AcomDC_0817_2016-->