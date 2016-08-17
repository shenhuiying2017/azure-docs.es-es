<properties 
	pageTitle="Vinculación de recursos en Azure Resource Manager | Microsoft Azure" 
	description="Creación de un vínculo entre los recursos en distintos grupos de recursos en Azure Resource Manager." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/01/2016" 
	ms.author="tomfitz"/>

# Vinculación de recursos en el Administrador de recursos de Azure

Durante la implementación, puede marcar un recurso como dependiente de otro, pero dicho ciclo de vida finaliza en la implementación. Después de la implementación, no hay ninguna relación identificada entre los recursos dependientes. Resource Manager proporciona una característica denominada vinculación de recursos para establecer relaciones persistentes entre los recursos.

Con la vinculación de recursos se pueden documentar las relaciones que abarcan varios grupos de recursos. Por ejemplo, es frecuente que una base de datos con su propio ciclo de vida resida en un grupo de recursos, y una aplicación con un ciclo de vida diferente en un grupo de recursos distinto. La aplicación se conecta a la base de datos, por lo que desea marcar un vínculo entre la aplicación y la base de datos.

Todos los recursos vinculados deben pertenecer a la misma suscripción. Cada recurso puede vincularse con otros 50. La única manera de consultar los recursos relacionados es a través de la API de REST. Si uno de los recursos vinculados se elimina o modifica, el propietario del vínculo debe borrar el vínculo restante. **No** se recibe ningún tipo de advertencia al eliminar un recurso que está vinculado a otros recursos.

## Vinculación de plantillas

Para definir un vínculo en una plantilla, se incluye un tipo de recurso que combina el espacio de nombres del proveedor de recursos y el tipo del recurso de origen con **/providers/links**. El nombre debe incluir el nombre del recurso de origen. Especifique el identificador de recurso del recurso de destino. En el ejemplo siguiente se establece un vínculo entre un sitio web y una cuenta de almacenamiento.

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


Para una descripción completa del formato de la plantilla, consulte [Vínculos de recursos: esquema de plantilla](resource-manager-template-links.md).

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

Puede consultar los vínculos de la suscripción con:

    https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Resources/links?api-version={api-version}

Para obtener más ejemplos, incluido cómo recuperar información acerca de los vínculos, consulte [Recursos vinculados](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## Pasos siguientes

- También puede organizar los recursos con etiquetas. Para obtener información sobre el etiquetado de recursos, consulte [Uso de etiquetas para organizar los recursos](resource-group-using-tags.md).
- Para obtener una descripción sobre cómo crear plantillas y definir los recursos que se van a implementar, consulte [Creación de plantillas](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0803_2016-->