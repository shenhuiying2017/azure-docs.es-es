<properties
	pageTitle="Uso de la interfaz de la línea de comandos (CLI) multiplataforma para crear alertas de los servicios de Azure | Microsoft Azure"
	description="Use la interfaz de la línea de comandos para crear alertas de Azure, que pueden desencadenar notificaciones o automatización cuando se cumplen las condiciones especificadas."
	authors="rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/23/2016"
	ms.author="robb"/>

# Uso de la interfaz de la línea de comandos (CLI) multiplataforma para crear alertas de los servicios de Azure 

> [AZURE.SELECTOR]
- [Portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLI](insights-alerts-command-line-interface.md)

## Información general

En este artículo se muestra cómo configurar alertas de Azure con la interfaz de la línea de comandos (CLI).

Puede recibir una alerta basada en las métricas de supervisión para los servicios de Azure o los eventos sobre ellos.

- **Valores de métrica**: la alerta se desencadena cuando el valor de una métrica específica cruza un umbral asignado en cualquier dirección. Es decir, se desencadena tanto la primera vez que se cumple la condición como después, cuando dicha condición ya deja de cumplirse.
- **Eventos de registro de actividades**: una alerta puede desencadenarse en *cada* evento o solo cuando se produce una serie de eventos.

Puede configurar una alerta para hacer lo siguiente cuando se desencadena:

- Enviar notificaciones de correo electrónico al administrador y los coadministradores del servicio.
- Enviar un correo electrónico a direcciones de correo electrónico adicionales que especifique.
- Llamar a un webhook.
- Iniciar la ejecución de un runbook de Azure (en este momento, solo desde Azure Portal).

Puede obtener información sobre las reglas de alerta y configurarlas mediante:

- [Portal de Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [Interfaz de la línea de comandos (CLI)](insights-alerts-command-line-interface.md)
- [API de REST de Azure Insights](https://msdn.microsoft.com/library/azure/dn931945.aspx)


Siempre puede escribir un comando y agregar -help al final para obtener ayuda sobre dicho comando. Por ejemplo:

	azure insights alerts -help
	azure insights alerts actions email create -help


## Creación de reglas de alerta mediante la CLI

1. Cumpla los requisitos previos e inicie sesión en Azure. Consulte [Ejemplos de CLI de Azure Insights](insights-cli-samples.md). En pocas palabras, instale la CLI y ejecute estos comandos. Con estos comandos, puede iniciar sesión, ver la suscripción que usa y prepararse para ejecutar comandos de Insights.


	```console
	azure login
	azure account show
	azure config mode arm 

	```

2.  Para ver las reglas existentes en un grupo de recursos, use el formato siguiente **azure insights alerts rule list** *[opciones] &lt;GrupoRecursos&gt;*

	```console
	azure insights alerts rule list myresourcegroupname

	```
3. Para crear una regla, primero debe contar con varios datos importantes.
	- El **identificador de recurso** del recursos para el que desea establecer una alerta.
	- Las **definiciones de métricas** disponibles para ese recurso.
	
    Una forma de obtener el identificador de recurso es usar Azure Portal. Seleccione el recurso en el portal, suponiendo que ya existe. En la hoja siguiente, seleccione *Propiedades* en la sección *Configuración*. El campo *RESOURCE ID* está en la hoja siguiente. Otra forma que puede usar es el [Explorador de recursos de Azure](https://resources.azure.com/).

    El siguiente es un ejemplo de identificador de recurso de una aplic. web:
  
	```
	/subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
	```

    Para obtener una lista de las métricas disponibles y las unidades de esas métricas para el ejemplo de recurso anterior, use el siguiente comando de CLI:

	```console
	azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M 
  	```

	_PT1M_ es la granularidad de la medida disponible (intervalos de 1 minuto). El uso de distintas granularidades le brinda opciones de métricas diferentes.

 
4. Para crear una regla de alerta basada en métricas, use un comando con el siguiente formato:
 
	**azure insights alerts rule metric set** *[opciones] &lt;nombreRegla&gt; &lt;ubicación&gt; &lt;grupoRecursos&gt; &lt;tamañoVentana&gt; &lt;operador&gt; &lt;umbral&gt; &lt;identificadorRecursoDestino&gt; &lt;nombreMétrica&gt; &lt;operadorAgregaciónTiempo&gt;*
	
	En el ejemplo siguiente se configura una alerta en un recurso de sitio web. La alerta se desencadena cada vez que se recibe constantemente cualquier tráfico durante 5 minutos y nuevamente cuando no se recibe tráfico durante 5 minutos.

    ```console
	azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
   
	```

5. Para crear un webhook o enviar un correo electrónico cuando se active una alerta, debe crear el correo electrónico o los webhooks. Cree la regla inmediatamente después. No puede asociar webhooks o correos electrónicos con reglas ya creadas mediante la CLI.
 
	```console
	azure insights alerts actions email create --customEmails myemail@contoso.com

	azure insights alerts actions webhook create https://www.contoso.com

	azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
	```


6. Para crear una alerta que se activa según una condición específica en el registro de actividades, use este formato:
 
	**insights alerts rule log set** *[opciones] &lt;nombreRegla&gt; &lt;ubicación&gt; &lt;grupoRecursos&gt; &lt;nombreOperación&gt;*

	Por ejemplo:

	```console
	azure insights alerts rule log set myActivityLogRule eastus myresourceGroupName Microsoft.Storage/storageAccounts/listKeys/action
	```

    El valor nombreOperación corresponde a un tipo de evento de una entrada del registro de actividades. Los ejemplos incluyen *Microsoft.Compute/virtualMachines/delete* y *microsoft.insights/diagnosticSettings/write*.

    Puede usar el comando [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) de PowerShell para obtener una lista de nombresOperación posibles. De manera alternativa, puede usar Azure Portal para consultar el registro de actividades y encontrar operaciones anteriores específicas para las que desea crear una alerta. Las operaciones se muestran en la vista gráfica de registro de los nombres descriptivos. Observe el código JSON de la entrada y extraiga el valor de nombreOperación.

7. Observe una regla individual para comprobar que las alertas se crearon correctamente.

	```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
	```

8. Para eliminar reglas, use un comando con este formato:

	**insights alerts rule delete** [opciones] &lt;grupoRecursos&gt; &lt;nombreRegla&gt;

	Estos comandos eliminan las reglas que se crearon anteriormente en este artículo.

	```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
	```



## Pasos siguientes

* [Obtenga información general sobre la supervisión de Azure](monitoring-overview.md), incluidos los tipos de información que puede recopilar y supervisar.
* Obtenga más información sobre cómo [configurar webhooks en las alertas](insights-webhooks-alerts.md).
* Obtenga más información sobre los [runbooks de Azure Automation](..\automation\automation-starting-a-runbook.md).
* Obtenga [información general sobre la colección de registros de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para recopilar métricas detalladas de alta frecuencia sobre el servicio.
* Obtenga [información general sobre la colección de métricas](insights-how-to-customize-monitoring.md) para garantizar que el servicio está disponible y que responder adecuadamente.

<!---HONumber=AcomDC_0928_2016-->