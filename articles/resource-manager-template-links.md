<properties
   pageTitle="Plantilla del Administrador de recursos para la vinculación de recursos | Microsoft Azure"
   description="Muestra el esquema de la plantilla del administrador de recursos para crear vínculos entre recursos relacionados."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/19/2015"
   ms.author="tomfitz"/>

# Vínculos de recursos: esquema de plantilla

Crea un vínculo entre dos recursos. El vínculo se aplica a un recurso conocido como recurso de origen. El segundo recurso del vínculo se conoce como recurso de destino.

## Formato de esquema

Para crear un vínculo, agregue el siguiente esquema a la sección de recursos de la plantilla.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## Valores

Las tablas siguientes describen los valores que debe establecer en el esquema.

| Nombre | Tipo | Obligatorio | Valores permitidos | Descripción |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Sí | **{namespace}/{type}/providers/links** | El tipo de recurso que se creará. Los valores {namespace} y {type} hacen referencia al espacio de nombres del proveedor y al tipo de recurso del recurso de origen. |
| apiVersion | enum | Sí | **2015-01-01** | La versión de la API que se usará para crear el recurso. |  
| name | cadena | Sí | **{resouce}/Microsoft.Resources/{linkname}**<br /><br />hasta 64 caracteres<br />No puede contener <, > %, &, ? ni ningún carácter de control. | Un valor que especifica tanto el nombre del recurso de origen como un nombre para el vínculo. |
| dependsOn | array | No | Una lista separada por comas de nombres de recursos o identificadores de recursos únicos. | La colección de recursos de la que depende este vínculo. Si los recursos que está vinculando se implementan en la misma plantilla, incluya esos nombres de los recursos en este elemento para asegurarse de que se implementan en primer lugar. | 
| propiedades | objeto | Sí | (se muestra a continuación) | Un objeto que identifica el recurso al que se vincula y notas sobre el vínculo. |  

### properties object

| Nombre | Tipo | Obligatorio | Valores permitidos | Descripción |
| ------- | ---- | ---------------- | -------- | ----------- |
| targetId | cadena | Sí | | El identificador del recurso de destino al que se va a vincular. |
| HDInsight | cadena | No | 512 caracteres | Descripción del bloqueo. |


## Uso del recurso de vinculación

Se aplica un vínculo entre dos recursos cuando los recursos tienen una dependencia que continúa después de la implementación. Por ejemplo, una aplicación puede conectarse a una base de datos en un grupo de recursos distinto. Puede definir esa dependencia creando un vínculo desde la aplicación a la base de datos. Los vínculos permiten documentar la relación entre dos recursos. Más adelante, usted u otra persona de su organización puede consultar los vínculos de un recurso para descubrir cómo funciona el recurso con otros recursos.

Todos los recursos vinculados deben pertenecer a la misma suscripción. Cada recurso puede vincularse con otros 50. Si uno de los recursos vinculados se elimina o modifica, el propietario del vínculo debe borrar el vínculo restante.

Para trabajar con vínculos a través de REST, vea [Recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Utilice el siguiente comando de Azure PowerShell para ver todos los vínculos de la suscripción. Puede proporcionar otros parámetros para limitar los resultados.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -OutputObjectFormat New

## Ejemplos

El ejemplo siguiente aplica un bloqueo de solo lectura a una aplicación web.

    {
	"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"hostingPlanName": {
      			"type": "string"
   		}
	},
	"variables": {
		"siteName": "[concat('site',uniqueString(resourceGroup().id))]"
	},
	"resources": [
	    {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
	    {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
	        "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
	    },
	    {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
    	    }
	],
	"outputs": {}
    }


## Pasos siguientes

- Para obtener más información sobre la estructura de la plantilla, consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_1125_2015-->