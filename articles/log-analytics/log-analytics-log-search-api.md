<properties
	pageTitle="API de REST de búsqueda de registros de Log Analytics | Microsoft Azure"
	description="Esta guía proporciona un tutorial básico en el que se describe cómo puede usar la API de REST de búsqueda de Log Analytics en Operations Management Suite (OMS) y, además, brinda ejemplos que muestran cómo usar los comandos."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>


# API de REST de búsqueda de registros de Log Analytics

Esta guía proporciona un tutorial básico en el que se describe cómo puede usar la API de REST de búsqueda de Log Analytics en Operations Management Suite (OMS) y, además, brinda ejemplos que muestran cómo usar los comandos. Algunos de los ejemplos que aparecen en este artículo hacen referencia a Visión operativa, el nombre de la versión anterior de Log Analytics.

## Información general sobre la API de REST de búsqueda de registros

La API de REST de búsqueda de Log Analytics es de tipo RESTful y se puede obtener acceso a ella a través de la API de Azure Resource Manager. En este documento encontrará ejemplos donde se tiene acceso a la API a través de [ARMClient](https://github.com/projectkudu/ARMClient), una herramienta de línea de comandos de código abierto que simplifica la invocación de la API del Administrador de recursos de Azure. El uso de ARMClient y PowerShell es una de las muchas opciones para tener acceso a la API de búsqueda de Log Analytics. Otra opción es utilizar el módulo Azure PowerShell para Visión operativa, que incluye cmdlets para obtener acceso a la búsqueda. Con estas herramientas, puede usar la API de Azure Resource Manager de RESTful para realizar llamadas a las áreas de trabajo de OMS y ejecutar comandos de búsqueda dentro de ellas. La API generará resultados de búsqueda, en formato JSON, lo que le permite usar los resultados de búsqueda de muchas formas distintas mediante programación.

El Administrador de recursos de Azure se puede usar mediante una [Biblioteca para .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) o una [API de REST](https://msdn.microsoft.com/library/azure/mt163658.aspx). Revise las páginas web vinculadas para obtener más información.

## Tutorial básico de la API de REST de búsqueda de Log Analytics

### Para usar ARMClient

1. Instale [Chocolatey](https://chocolatey.org/), que es un administrador de paquetes de código abierto para Windows. Abra una ventana de símbolo del sistema como administrador y luego ejecute el siguiente comando:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

2. Instale ARMClient con el siguiente comando:

    ```
    choco install armclient
    ```

### Para realizar una búsqueda simple mediante ARMClient

1. Inicie sesión en su cuenta Microsoft o OrgID:

    ```
    armclient login
    ```

    Si la sesión se ha iniciado correctamente, se muestran todas las suscripciones vinculadas a la cuenta especificada.

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. Obtenga las áreas de trabajo de Operations Management Suite:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Una llamada Get correcta generaría todas las áreas de trabajo vinculadas a la suscripción:

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Cree la variable de búsqueda:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Realice búsquedas con la nueva variable de búsqueda:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## Ejemplos de referencia de la API de REST de búsqueda de Log Analytics
Los siguientes ejemplos muestran cómo se puede usar la API de búsqueda.

### Search - Action/Read

**Dirección Url de ejemplo:**

```
	/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Solicitud:**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
En la tabla siguiente se describen las propiedades que están disponibles.

|**Propiedad**|**Descripción**|
|---|---|
|top|El número máximo de resultados que se devolverán.|
|highlight|Contiene parámetros pre y post, usados normalmente para resaltar los campos coincidentes|
|pre|Agrega la cadena especificada delante de los campos coincidentes.|
|post|Anexa la cadena especificada a los campos coincidentes.|
|query|La consulta de búsqueda que se usa para recopilar y devolver resultados.|
|start|El comienzo de la ventana de tiempo en la que desea encontrar resultados.|
|end|El final de la ventana de tiempo en la que desea encontrar resultados.|


**Respuesta:**

```
	{
	  "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	  "__metadata" : {
	    "resultType" : "raw",
	    "total" : 1455,
	    "top" : 150,
	    "StartTime" : "2015-02-11T21:09:07.0345815Z",
	    "Status" : "Successful",
	    "LastUpdated" : "2015-02-11T21:09:07.331463Z",
	    "CoreResponses" : [],
	    "sort" : [{
	      "name" : "TimeGenerated",
	      "order" : "desc"
	    }],
	    "requestTime" : 450
	  },
	  "value": [{
	    "SourceSystem" : "OpsManager",
	    "TimeGenerated" : "2015-02-07T14:07:33Z",
	    "Source" : "SideBySide",
	    "EventLog" : "Application",
	    "Computer" : "BAMBAM",
	    "EventCategory" : 0,
	    "EventLevel" : 1,
	    "EventLevelName" : "Error",
	    "UserName" : "N/A",
	    "EventID" : 78,
	    "MG" : "00000000-0000-0000-0000-000000000001",
	    "TimeCollected" : "2015-02-07T14:10:04.69Z",
	    "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
	    "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	    "Type" : "Event",
	    "__metadata" : {
	      "Type" : "Event",
	      "TimeGenerated" : "2015-02-07T14:07:33Z",
	      "highlighting" : {
	      "EventLevelName" : ["{[hl]}Error{[/hl]}"]
	    }
	  }]
	],
	        "start" : "2015-02-04T21:03:29.231Z",
	        "end" : "2015-02-11T21:03:29.231Z"
	      }
	    }
	  }]
	}
```

### Search/{ID} - Action/Read

**Solicitar el contenido de una búsqueda guardada:**

```
	armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

>[AZURE.NOTE] Si la búsqueda se devuelve con el estado 'Pendiente', el sondeo de los resultados actualizados puede realizarse a través de esta API. Después de seis minutos, el resultado de la búsqueda se eliminará de la caché y se devolverá HTTP Ya no existe. Si la solicitud de búsqueda inicial devolvió inmediatamente un estado 'Correcto', no se agregará a la caché, lo que provocará que esta API devuelva HTTP Ya no existe si se consulta. El contenido de un resultado HTTP 200 estará en el mismo formato que la solicitud de búsqueda inicial, solo que con valores actualizados.

### Búsquedas guardadas: solo REST

**Solicitar lista de búsquedas guardadas:**

```
	armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Métodos admitidos: GET PUT DELETE

Métodos de colección admitidos: GET

En la tabla siguiente se describen las propiedades que están disponibles.

|Propiedad|Descripción|
|---|---|
|Id|Identificador único.|
|Etag|**Obligatoria para la revisión**. Actualizada por el servidor en cada escritura. El valor debe ser igual al valor actual almacenado o ' *' para actualizar. 409 devuelto para valores antiguos o no válidos.|
|properties.query|**Obligatoria**. Consulta de la búsqueda.|
|properties.displayName|**Obligatoria**. El nombre para mostrar definido por el usuario de la consulta. Si se modela como un recurso de Azure, esto sería una etiqueta.|
|properties.category|**Obligatoria**. Categoría de la consulta definida por el usuario. Si se modela como un recurso de Azure sería una etiqueta.|

>[AZURE.NOTE] Actualmente, la API de búsqueda de Log Analytics devuelve las búsquedas guardadas creadas por el usuario cuando se realiza un sondeo de búsquedas guardadas en un área de trabajo. En este momento, la API no devolverá las búsquedas guardadas proporcionadas por soluciones. Esta funcionalidad se agregará en una fecha posterior.

### Eliminación de búsquedas guardadas

**Solicitud:**

```
	armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### Actualización de búsquedas guardadas

 **Solicitud:**

```
	$savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
	armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### Metadatos: solo JSON

Aquí se proporciona una manera de ver los campos de todos los tipos de registro para los datos recopilados en el área de trabajo. Por ejemplo, si desea saber si el tipo de evento tiene un campo denominado Computer, esta es una manera de buscar y confirmar.

**Solicitud de campos:**

```
	armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Respuesta:**

```
	{
	  "__metadata" : {
	    "schema" : {
	      "name" : "Example Name",
	      "version" : 2
	    },
	    "resultType" : "schema",
	    "requestTime" : 35
	  },
	  "value" : [{
	      "name" : "MG",
	      "displayName" : "MG",
	      "type" : "Guid",
	      "facetable" : true,
	      "display" : false,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }, {
	      "name" : "ManagementGroupName",
	      "displayName" : "ManagementGroupName",
	      "type" : "String",
	      "facetable" : true,
	      "display" : true,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }
	  ]
	}
```

En la tabla siguiente se describen las propiedades que están disponibles.

|**Propiedad**|**Descripción**|
|---|---|
|name|Nombre del campo.|
|DisplayName|Nombre para mostrar del campo.|
|type|Tipo del valor del campo.|
|facetable|Combinación de las propiedades actuales ‘indexed’, ‘stored ‘y ‘facet’.|
|display|Propiedad 'display' actual. True si el campo está visible en la búsqueda.|
|ownerType|Se reduce a solo los tipos que pertenecen a las IP incorporadas.|


## Parámetros opcionales
La siguiente información describe los parámetros opcionales disponibles.

### Resaltado

El parámetro "Highlight" es un parámetro opcional que se puede usar para solicitar al subsistema de búsqueda que incluya un conjunto de marcadores en la respuesta.

Estos marcadores indican el inicio y fin del texto resaltado que coincide con los términos que se proporcionan en la consulta de búsqueda. Puede especificar los marcadores de inicio y final que se usarán en la búsqueda para envolver el término resaltado.

**Ejemplo de consulta de la búsqueda**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Resultado de ejemplo:**

```
	{
	    "TimeGenerated":
	    "2015-05-18T23:55:59Z", "Source":
	    "EventLog": "Application",
	    "Computer": "smokedturkey.net",
	    "EventCategory": 0,
	    "EventLevel":1,
	    "EventLevelName":
	    "Error"
	    "Manager ", "__metadata":
	    {
	        "Type": "Event",
	        "TimeGenerated": "2015-05-18T23:55:59Z",
	        "highlighting": {
	            "EventLevelName":
	            ["{[hl]}Error{[/hl]}"]
	        }
	    }
	}
```

Observe que el resultado anterior contiene un mensaje de error que se ha incluido como prefijo y se ha anexado.

## Grupos de equipos

Los grupos de equipos son búsquedas guardadas especiales que devuelven un conjunto de equipos. Puede usar un grupo de equipos en otras consultas para limitar los resultados a los equipos del grupo. Un grupo de equipos se implementa como una búsqueda guardada con una etiqueta Grupo con un valor de Equipo.

La siguiente es una respuesta de ejemplo para un grupo de equipos.

	"etag": "W/"datetime'2016-04-01T13%3A38%3A04.7763203Z'"",
	"properties": {
		"Category": "My Computer Groups",
		"DisplayName": "My Computer Group",
		"Query": "srv* | Distinct Computer",
		"Tags": [{
        	"Name": "Group",
        	"Value": "Computer"
      	}],
    "Version": 1
	}

### Recuperación de grupos de equipos

Use el método Get con el identificador de grupo para recuperar un grupo de equipos.

```
armclient get /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Group ID}`?api-version=2015-03-20
```

### Creación o actualización de un grupo de equipos

Use el método Put con un identificador de búsqueda guardada único para crear un grupo de equipos nuevo. Si usa un identificador de grupo de equipos existente, se modificará. Cuando crea un grupo de equipos en la consola de OMS, se crea el identificador a partir del grupo y el nombre.

La consulta que se usa para la definición de grupo debe devolver un conjunto de equipos para que el grupo funcione correctamente. Se recomienda que la consulta finalice con *| equipo distinto* para asegurarse de que se devuelvan los datos correctos.

La definición de la búsqueda guardada debe incluir una etiqueta denominada Grupo con un valor de Equipo para que la búsqueda se clasifique como un grupo de equipos.

	$etag=Get-Date -Format yyyy-MM-ddThh:mm:ss.msZ
	$groupName="My Computer Group"
	$groupQuery = "Computer=srv* | Distinct Computer"
	$groupCategory="My Computer Groups"
	$groupID = "My Computer Groups | My Computer Group"

	$groupJson = "{'etag': 'W/`"datetime\'" + $etag + "\'`"', 'properties': { 'Category': '" + $groupCategory + "', 'DisplayName':'"  + $groupName + "', 'Query':'" + $groupQuery + "', 'Tags': [{'Name': 'Group', 'Value': 'Computer'}], 'Version':'1'  }"

	armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20 $groupJson

### Eliminación de grupos de equipos

Use el método Delete con el identificador de grupo para eliminar un grupo de equipos.

```
armclient delete /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/$groupId`?api-version=2015-03-20
```


## Pasos siguientes

- Si desea crear consultas con campos personalizados en función de unos criterios, obtenga más información acerca de las [búsquedas de registros](log-analytics-log-searches.md).

<!---HONumber=AcomDC_0518_2016-->