<properties
   pageTitle="API de búsqueda de registros de Visión operativa"
   description="Proporciona información general sobre la API de búsqueda de Visión operativa e incluye ejemplos que muestran cómo se usa."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/21/2015"
   ms.author="banders" />



# Información general y referencia de la API de búsqueda de Visión operativa

Esta guía proporciona un tutorial básico en el que se describe cómo puede usar la API de búsqueda de registros de Visión operativa y se incluyen ejemplos que le muestran cómo usar los comandos.

## Información general sobre la API de búsqueda de registros

La API de búsqueda de registros de Visión operativa es RESTful y se puede tener acceso a ella a través de la API del Administrador de recursos de Azure. En este documento encontrará ejemplos donde se tiene acceso a la API a través de [ARMClient](https://github.com/projectkudu/ARMClient), una herramienta de línea de comandos de código abierto que simplifica la invocación de la API del Administrador de recursos de Azure. El uso de ARMClient y PowerShell es una de las muchas opciones para tener acceso a la API de búsqueda de registros de Visión operativa. Con estas herramientas, puede usar la API del Administrador de recursos de Azure, RESTful, para realizar llamadas a las áreas de trabajo de Visión operativa y ejecutar comandos de búsqueda dentro de ellas. La API generará resultados de búsqueda, en formato JSON, lo que le permite usar los resultados de búsqueda de muchas formas distintas mediante programación.

El Administrador de recursos de Azure se puede usar mediante una [Biblioteca para .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) o una [API de REST](https://msdn.microsoft.com/library/azure/mt163658.aspx). Revise las páginas web que ha indicado que le gustan para obtener más información.

## Tutorial básico de la API de búsqueda

### Para usar ARMClient

1. Instale [Chocolatey](https://chocolatey.org/), que es un administrador de paquetes de máquina de origen abierto para Windows.
2. Abra una ventana de PowerShell como administrador y luego ejecute el siguiente comando:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

3. Instale ARMClient abriendo un nuevo símbolo del sistema y ejecute el siguiente comando:

    ```
    choco install armclient
    ```

### Para realizar una búsqueda simple mediante ARMClient

1. Inicie sesión en su cuenta Microsoft o OrgID:

    ```
    armclient login
```
  Si la sesión se ha iniciado correctamente, se muestran todas las suscripciones vinculadas a la cuenta especificada. Por ejemplo:

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. Obtenga las áreas de trabajo del conjunto de aplicaciones de administración de operaciones. Por ejemplo:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2014-10-10
    ```

    Una llamada Get correcta generaría todas las áreas de trabajo vinculadas a la suscripción. Por ejemplo:

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
3. Cree la variable de búsqueda. Por ejemplo:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}”;
    ```
4. Realice búsquedas con la nueva variable de búsqueda. Por ejemplo:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2014-10-10 $mySearch
    ```

## Ejemplos de referencia de la API de búsqueda
Los siguientes ejemplos muestran cómo se puede usar la API de búsqueda.

### Search - Action/Read

**Dirección Url de ejemplo:**

```
	/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2014-10-10
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
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2014-10-10 $searchParametersJson
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
	armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2014-10-10
```

>[AZURE.NOTE]Si la búsqueda se devuelve con el estado 'Pendiente', el sondeo de los resultados actualizados puede realizarse a través de esta API. Después de seis minutos, el resultado de la búsqueda se eliminará de la caché y se devolverá Http Ya no existe. Si la solicitud de búsqueda inicial devuelve inmediatamente un estado 'Correcto', no se agregará a la caché, lo que provocará que esta API devuelva Http Ya no existe si se consulta. El contenido de un resultado Http 200 estará en el mismo formato que la solicitud de búsqueda inicial, solo que con valores actualizados.

### Búsquedas guardadas: solo REST

**Solicitar lista de búsquedas guardadas:**

```
	armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2014-10-10
```

Métodos admitidos: GET PUT DELETE

Métodos de colección admitidos: GET

En la tabla siguiente se describen las propiedades que están disponibles.

|Propiedad|Descripción|
|---|---|
|Id|Identificador único.|
|Etag|**Obligatoria para la revisión**. Actualizada por el servidor en cada escritura. El valor debe ser igual al valor actual almacenado o ' *' para actualizar. 409 devuelto para valores antiguos o no válidos.|
|properties.query|**Obligatorio**. Consulta de la búsqueda.|
|properties.displayName|**Obligatorio**. El nombre para mostrar definido por el usuario de la consulta. Si se modela como un recurso de Azure, esto sería una etiqueta.|
|properties.category|**Obligatorio**. Categoría de la consulta definida por el usuario. Si se modela como un recurso de Azure sería una etiqueta.|

>[AZURE.NOTE]Actualmente, la API de búsqueda de Visión operativa devuelve las búsquedas guardadas creadas por el usuario cuando se realiza un sondeo de búsquedas guardadas en un área de trabajo. En este momento, la API no devolverá las búsquedas guardadas proporcionadas por soluciones. Esta funcionalidad se agregará en una fecha posterior.

### Eliminación de búsquedas guardadas

**Solicitud:**

```
	armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2014-10-10
```

### Actualización de búsquedas guardadas

 **Solicitud:**

```
	$savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
	armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2014-10-10 $savedSearchParametersJson
```

### Metadatos: solo JSON

Aquí se proporciona una manera de ver los campos de todos los tipos de registro para los datos recopilados en el área de trabajo. Por ejemplo, si desea saber si el tipo de evento tiene un campo denominado Computer, esta es una manera de buscar y confirmar.

**Solicitud de campos:**

```
	armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2014-10-10
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
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2014-10-10 $searchParametersJson
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

<!---HONumber=August15_HO6-->