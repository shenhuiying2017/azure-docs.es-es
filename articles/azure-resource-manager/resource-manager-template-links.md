---
title: "Plantilla de Resource Manager para la vinculación de recursos | Microsoft Docs"
description: "Muestra el esquema del Administrador de recursos para implementar vínculos entre recursos relacionados mediante una plantilla."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 48a13b1a-3208-4f91-ba85-bda2a0e22605
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: dcac45042b2511ae2b82ecf8278e9873c6f8c9b9


---
# <a name="resource-links-template-schema"></a>Vínculos de recursos: esquema de plantilla
Crea un vínculo entre dos recursos. El vínculo se aplica a un recurso conocido como recurso de origen. El segundo recurso del vínculo se conoce como recurso de destino.

## <a name="schema-format"></a>Formato de esquema
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



## <a name="values"></a>Valores
Las tablas siguientes describen los valores que debe establecer en el esquema.

| Nombre | Valor |
| --- | --- |
| type |Enum<br />Obligatorio<br />**{namespace}/{type}/providers/links**<br /><br />Tipo de recurso que se creará. Los valores {namespace} y {type} hacen referencia al espacio de nombres del proveedor y al tipo de recurso del recurso de origen. |
| apiVersion |Enum<br />Obligatorio<br />**2015-01-01**<br /><br />Versión de la API que se usará para crear el recurso. |
| Nombre |Cadena<br />Obligatorio<br />**{resouce}/Microsoft.Resources/{linkname}**<br /> hasta 64 caracteres y no puede incluir <, >, % &,? o cualquier carácter de control.<br /><br />Un valor que especifica tanto el nombre del recurso de origen como un nombre para el vínculo. |
| dependsOn |Matriz<br />Opcional<br />Una lista separada por comas de nombres de recursos o identificadores de recursos únicos.<br /><br />Colección de recursos de los que depende este vínculo. Si los recursos que está vinculando se implementan en la misma plantilla, incluya esos nombres de los recursos en este elemento para asegurarse de que se implementan en primer lugar. |
| propiedades |Objeto<br />Obligatorio<br />[properties object](#properties)<br /><br />Un objeto que identifica el recurso al que se vincula y notas sobre el vínculo. |

<a id="properties" />

### <a name="properties-object"></a>properties object
| Nombre | Valor |
| --- | --- |
| targetId |String<br />Obligatorio<br />**{resource id}**<br /><br />Identificador del recurso de destino al que se vinculará. |
| HDInsight |Cadena<br />Opcional<br />hasta 512 caracteres<br /><br />Descripción del bloqueo. |

## <a name="how-to-use-the-link-resource"></a>Uso del recurso de vinculación
Se aplica un vínculo entre dos recursos cuando los recursos tienen una dependencia que continúa después de la implementación. Por ejemplo, una aplicación puede conectarse a una base de datos en un grupo de recursos distinto. Puede definir esa dependencia creando un vínculo desde la aplicación a la base de datos. Los vínculos permiten documentar la relación entre dos recursos. Más adelante, usted u otra persona de su organización puede consultar los vínculos de un recurso para descubrir cómo funciona el recurso con otros recursos.

Todos los recursos vinculados deben pertenecer a la misma suscripción. Cada recurso puede vincularse con otros 50. Si uno de los recursos vinculados se elimina o modifica, el propietario del vínculo debe borrar el vínculo restante.

Para trabajar con vínculos a través de REST, vea [Recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Utilice el siguiente comando de Azure PowerShell para ver todos los vínculos de la suscripción. Puede proporcionar otros parámetros para limitar los resultados.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## <a name="examples"></a>Ejemplos
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

## <a name="quickstart-templates"></a>Plantillas de inicio rápido
Las siguientes plantillas de inicio rápido implementan recursos con un vínculo.

* [Alert to queue with Logic app (Alerta a cola con aplicaciones lógicas)](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
* [Alert to Slack with Logic app (Alerta al margen de demora con aplicaciones lógicas).](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
* [Aprovisionamiento de una aplicación de API con una puerta de enlace existente](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
* [Aprovisionamiento de una aplicación de API con una nueva puerta de enlace](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
* [Creación de una aplicación lógica y una aplicación de API mediante una plantilla](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
* [Logic app that sends a text message when an alert fires (Aplicación lógica que envía un mensaje de texto cuando se desencadena una alerta)](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre la estructura de la plantilla, consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).




<!--HONumber=Nov16_HO3-->


