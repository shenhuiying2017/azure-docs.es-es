<properties
   pageTitle="Plantilla del Administrador de recursos para la vinculación de recursos | Microsoft Azure"
   description="Muestra el esquema del Administrador de recursos para implementar vínculos entre recursos relacionados mediante una plantilla."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
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

| Nombre | Valor |
| ---- | ---- |
| type | Enum<br />Obligatorio<br />**{namespace}/{type}/providers/links**<br /><br />Tipo de recurso que se creará. Los valores {namespace} y {type} hacen referencia al espacio de nombres del proveedor y al tipo de recurso del recurso de origen. |
| apiVersion | Enum<br />Obligatorio<br />**2015-01-01**<br /><br />Versión de la API que se usará para crear el recurso. |  
| name | Cadena<br />Obligatorio<br />**{resouce}/Microsoft.Resources/{linkname}****<br /> Hasta 64 caracteres y no puede incluir <, > %, &, ? ni ningún carácter de control.<br /><br />Valor que especifica el nombre del recurso y el del vínculo. | | dependsOn | Matriz<br />Opcional<br />Lista separada por comas de nombres o identificadores únicos de recursos.<br /><br />Colección de recursos de los que depende este vínculo. Si el recurso que está vinculando se implementa en la misma plantilla, incluya los nombres de esos recursos en este elemento para garantizar que se implementen primero. | | properties | Objeto<br />Obligatorio<br />[Objeto de propiedades](#properties)<br /><br />Objeto que identifica el recurso que se bloqueará y notas sobre el vínculo. | 

<a id="properties" />
### properties object

| Nombre | Valor |
| ------- | ---- |
| targetId | Cadena<br />Obligatorio<br />**{resource id}****<br /><br />Identificador del recurso de destino al que se vinculará. || notes | Cadena<br />Opcional<br />Hasta 512 caracteres<br /><br />Descripción del bloqueo. |


## Uso del recurso de vinculación

Se aplica un vínculo entre dos recursos cuando los recursos tienen una dependencia que continúa después de la implementación. Por ejemplo, una aplicación puede conectarse a una base de datos en un grupo de recursos distinto. Puede definir esa dependencia creando un vínculo desde la aplicación a la base de datos. Los vínculos permiten documentar la relación entre dos recursos. Más adelante, usted u otra persona de su organización puede consultar los vínculos de un recurso para descubrir cómo funciona el recurso con otros recursos.

Todos los recursos vinculados deben pertenecer a la misma suscripción. Cada recurso puede vincularse con otros 50. Si uno de los recursos vinculados se elimina o modifica, el propietario del vínculo debe borrar el vínculo restante.

Para trabajar con vínculos a través de REST, vea [Recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Utilice el siguiente comando de Azure PowerShell para ver todos los vínculos de la suscripción. Puede proporcionar otros parámetros para limitar los resultados.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

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

## Plantillas de inicio rápido

Las siguientes plantillas de inicio rápido implementan recursos con un vínculo.

- [Alert to queue with Logic app (Alerta a cola con aplicaciones lógicas)](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
- [Alert to Slack with Logic app (Alerta al margen de demora con aplicaciones lógicas).](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
- [Aprovisionamiento de una aplicación de API con una puerta de enlace existente](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
- [Aprovisionamiento de una aplicación de API con una nueva puerta de enlace](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
- [Creación de una aplicación lógica y una aplicación de API mediante una plantilla](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
- [Logic app that sends a text message when an alert fires (Aplicación lógica que envía un mensaje de texto cuando se desencadena una alerta)](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)


## Pasos siguientes

- Para obtener más información sobre la estructura de la plantilla, consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0406_2016-->