<properties
    pageTitle="Creación de un espacio de nombres de Event Hubs con un Centro de eventos y habilitación del Archivado mediante una plantilla de Azure Resource Manager | Microsoft Azure"
    description="Creación de un espacio de nombres de Event Hubs con un Centro de eventos y habilitación del Archivado mediante una plantilla de Azure Resource Manager"
    services="event-hubs"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="09/14/2016"
    ms.author="ShubhaVijayasarathy"/>

# Creación de un espacio de nombres de Event Hubs con un Centro de eventos y habilitación del Archivado mediante una plantilla de Azure Resource Manager

En este artículo se muestra cómo utilizar una plantilla de Azure Resource Manager que crea un espacio de nombres de Event Hubs con un Centro de eventos y habilita el Archivado en el Centro de eventos. Aprenderá a definir los recursos que se implementan y los parámetros que se especifican cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarla para satisfacer sus necesidades.

Para más información sobre la creación de plantillas, consulte [Creación de plantillas de Azure Resource Manager][].

Para obtener más información sobre prácticas y patrones de convenciones de nomenclatura de recursos de Azure, consulte [Recommended naming conventions for Azure resources][] (Convenciones de nomenclatura recomendadas para los recursos de Azure).

Para ver la plantilla completa, consulte [Event Hub and enable Archive template][] (Plantilla de Centro de eventos y habilitación de Archivado) en GitHub.

>[AZURE.NOTE]
Para buscar las últimas plantillas, visite la galería de [Plantillas de inicio rápido de Azure][] y busque Centros de eventos.

## ¿Qué va a implementar?

Con esta plantilla, implementará un espacio de nombres de Event Hubs con un Centro de eventos y se habilitará el Archivado.

[Centros de eventos](../event-hubs/event-hubs-what-is-event-hubs.md) es un servicio de procesamiento de eventos que se usa para ofrecer la entrada de telemetría y eventos en Azure a escala masiva, con una latencia baja y una alta confiabilidad. El Archivado de Event Hubs le permite entregar automáticamente los datos de transmisión de sus Centros de eventos en un servicio Azure Blob Storage de su elección a la hora especificada o el intervalo de tamaños que prefiera.

Para ejecutar automáticamente la implementación, haga clic en el botón siguiente:

[![Implementación en Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-archive%2Fazuredeploy.json)

## Parámetros

Con el Administrador de recursos de Azure, se definen los parámetros de los valores que desea especificar al implementar la plantilla. La plantilla incluye una sección denominada `Parameters` que contiene todos los valores de los parámetros. Debe definir un parámetro para los valores que variarán según el proyecto que vaya a implementar o según el entorno en el que vaya a realizar la implementación. No defina parámetros para valores que vayan a permanecer igual. Cada valor de parámetro se usa en la plantilla para definir los recursos que se implementan.

La plantilla define los parámetros siguientes.

### eventHubNamespaceName

El nombre del espacio de nombres de Centros de eventos que se creará.

```
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### eventHubName

El nombre del centro de eventos creado en el espacio de nombres de Centros de eventos.

```
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the Event Hub"
    }
}
```

### messageRetentionInDays

El número de días que desea que los mensajes se conserven en el Centro de eventos.

```
"messageRetentionInDays":{
	"type":"int",
	"defaultValue": 1,
	"minValue":"1",
	"maxValue":"7",
	"metadata":{
	   "description":"How long to retain the data in Event Hub"
	 }
 }
```

### partitionCount

El número de particiones que desea en el Centro de eventos.

```
"partitionCount":{
	"type":"int",
	"defaultValue":2,
	"minValue":2,
	"maxValue":32,
	"metadata":{
		"description":"Number of partitions chosen"
	}
 }
```

### archiveEnabled

Habilitar el Archivado en el Centro de eventos.

```
"archiveEnabled":{
	"type":"string",
	"defaultValue":"true",
	"allowedValues": [
	"false",
	"true"],
	"metadata":{
		"description":"Enable or disable the Archive for your Event Hub"
	}
 }
```
### archiveEncodingFormat

El formato de codificación que especifica para serializar los datos de eventos.

```
"archiveEncodingFormat":{
	"type":"string",
	"defaultValue":"Avro",
	"allowedValues":[
	"Avro"],
	"metadata":{
		"description":"The encoding format Archive serializes the EventData"
	}
}
```

### archiveTime

El intervalo de tiempo en que el Archivado empieza a archivar datos en Azure Blob Storage.

```
"archiveTime":{
	"type":"int",
	"defaultValue":300,
	"minValue":60,
	"maxValue":900,
	"metadata":{
		 "description":"the time window in seconds for the archival"
	}
}
```

### archiveSize

El intervalo de tamaño en que el Archivado empieza a archivar datos en Azure Blob Storage.

```
"archiveSize":{
	"type":"int",
	"defaultValue":314572800,
	"minValue":10485760,
	"maxValue":524288000,
	"metadata":{
		"description":"the size window in bytes for archival"
	}
}
```

### destinationStorageAccountResourceId

El Archivado requerirá un identificador de recurso de la cuenta de almacenamiento para habilitar el archivado en la instancia de Azure Storage que desee.

```
 "destinationStorageAccountResourceId":{
	"type":"string",
	"metadata":{
		"description":"Your existing storage Account resource id where you want the blobs be archived"
	}
 }
```

### blobContainerName

El contenedor del blob en el que desea que se archiven los datos de eventos.

```
 "blobContainerName":{
	"type":"string",
	"metadata":{
		"description":"Your existing storage Container that you want the blobs archived in"
	}
}
```


### apiVersion

La versión de API de la plantilla.

```
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

## Recursos para implementar

Crea un espacio de nombres de tipo **Event Hubs** con un Centro de eventos y habilita el Archivado.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
				  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
				  "PartitionCount":"[parameters('partitionCount')]",
				  "ArchiveDescription":{
						"enabled":"[parameters('archiveEnabled')]",
						"encoding":"[parameters('archiveEncodingFormat')]",
						"intervalInSeconds":"[parameters('archiveTime')]",
						"sizeLimitInBytes":"[parameters('archiveSize')]",
						"destination":{
							"name":"EventHubArchive.AzureBlockBlob",
							"properties":{
								"StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
								"BlobContainer":"[parameters('blobContainerName')]"
							}
						} 
				  }
				  
               }
               
            }
         ]
      }
   ]
```

## Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json
```

## Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json][]
```

## Pasos siguientes

Ahora que ha creado e implementado recursos con Azure Resource Manager, estos artículos le enseñarán como administrarlos:

- [Administración de recursos de Centros de eventos con el Explorador de Bus de servicio](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Creación de plantillas de Azure Resource Manager]: ../resource-group-authoring-templates.md
  [Plantillas de inicio rápido de Azure]: https://azure.microsoft.com/documentation/templates/?term=event+hubs
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Event Hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-eventhubs-create-namespace-and-enable-archive/
  [Recommended naming conventions for Azure resources]: https://azure.microsoft.com/es-ES/documentation/articles/guidance-naming-conventions/
  [Event Hub and enable Archive template]: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-archive]

<!---HONumber=AcomDC_0914_2016-->