<properties 
	pageTitle="Vinculación de recursos en el Administrador de recursos de Azure" 
	description="Cree un vínculo entre los recursos en distintos grupos de recursos en el Administrador de recursos de Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="tomfitz"/>

# Vinculación de recursos en el Administrador de recursos de Azure

Después de la implementación es posible que desee consultar las relaciones o vínculos entre recursos. Las dependencias informan a la implementación, pero el ciclo de vida finaliza en la implementación. Una vez completada la implementación, no hay ninguna relación identificada entre los recursos dependientes.

En su lugar, el Administrador de recursos de Azure proporciona una nueva característica denominada vinculación de recursos para establecer y consultar las relaciones entre los recursos. Puede determinar qué recursos están vinculados a un recurso o qué recursos están vinculados a partir de un recurso.

El ámbito de un vínculo de recurso puede ser una suscripción, un grupo de recursos o un recurso específico. Esto significa que los vínculos de recursos pueden documentar las relaciones que abarcan los grupos de recursos. Cuando comienza a descomponer la solución en varias plantillas y varios grupos de recursos, resulta útil contar con un mecanismo para identificar estos vínculos de recursos. Por ejemplo, es frecuente que una base de datos con su propio ciclo de vida resida en un grupo de recursos, y una aplicación con un ciclo de vida diferente en un grupo de recursos distinto. La aplicación se conecta a la base de datos, por lo que hay un vínculo entre los recursos en distintos grupos de recursos.

Todos los recursos vinculados deben pertenecer a la misma suscripción. Cada recurso puede vincularse con otros 50. Si uno de los recursos vinculados se elimina o modifica, el propietario del vínculo debe borrar el vínculo restante.

## Vinculación de plantillas

El ejemplo siguiente muestra una plantilla que crea un recurso de tipo "Microsoft.AppService/apiapps" con un conjunto de relaciones unidireccionales para un sitio web, un centro de notificaciones y bases de datos SQL.

    {
        "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "$system": {
                "type": "Object"
            }
        },
        "resources": [
            {
                "apiVersion": "2014-11-01",
                "type": "Microsoft.Web/sites",
                "name": "[parameters('$system').siteName]",
                "location": "[parameters('$system').location]",
                "resources": [
                    {
                        "apiVersion": "2014-11-01",
                        "name": "appsettings",
                        "type": "config",
                        "dependsOn": [
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "MS_MobileServiceName": "[parameters('$system').apiAppName]",
                            "MS_NotificationHubName": "[variables('notificationHubName')]",
                            "MS_NotificationHubConnectionString": "[listkeys(resourceId('Microsoft.NotificationHubs/namespaces/notificationHubs/authorizationRules', variables('notificationHubNamespace'), variables('notificationHubName'), 'DefaultFullSharedAccessSignature'), '2014-09-01').primaryConnectionString]"
                        }
                    }
                ]
            },
            {
                "apiVersion": "[parameters('$system').apiVersion]",
                "type": "Microsoft.AppService/apiapps",
                "name": "[parameters('$system').apiAppName]",
                "properties": {
                    "accessLevel": "PublicAnonymous"
                },
                "resources": [
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-codesite",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.Web/Sites', variables('userSiteName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.Web/sites', variables('userSiteName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-notificationhub",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqlserver",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName)]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqldb",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName, '/databases/', parameters('userDatabase').databaseName)]"
                        }
                    }
                ]
            }
        ]
    }

## Vinculación con la API de REST

Para definir un vínculo entre recursos implementados, ejecute:

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Reemplace {subscription-id} por el identificador de suscripción. Reemplace {resource-group}, {provider-namespace, {resource-type} y {resource-name} por los valores que identifican el primer recurso en el vínculo. Reemplace {link-name} por el nombre del vínculo que crear. Use 2015-01-01 para la versión de la API.

En la solicitud, incluya un objeto que defina al segundo recurso del vínculo:

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

El elemento de propiedades contiene el identificador para el segundo recurso.

Para obtener más ejemplos, incluido cómo recuperar información acerca de los vínculos, consulte [Recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## Pasos siguientes

- También puede organizar los recursos con etiquetas. Para obtener información sobre el etiquetado de recursos, consulte [Uso de etiquetas para organizar los recursos](resource-group-using-tags.md).
- Para obtener una descripción sobre cómo crear plantillas y definir los recursos que se van a implementar, consulte [Creación de plantillas](resource-group-authoring-templates.md).

<!---HONumber=July15_HO3-->