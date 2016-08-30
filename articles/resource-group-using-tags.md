<properties
	pageTitle="Uso de etiquetas para organizar los recursos de Azure | Microsoft Azure"
	description="Muestra cómo aplicar etiquetas para organizar los recursos para la facturación y administración."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="AzurePortal"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="tomfitz"/>


# Uso de etiquetas para organizar los recursos de Azure

El Administrador de recursos le permite organizar recursos de manera lógica mediante la aplicación de etiquetas. Las etiquetas constan de pares clave-valor que identifican los recursos con las propiedades que define. Si desea marcar los recursos como pertenecientes a la misma categoría, aplique la misma etiqueta a esos recursos.

Cuando consulta los recursos con una etiqueta determinada, puede ver recursos de todos los grupos de recursos. No esta limitado únicamente a los recursos del mismo grupo de recursos, lo que le permite organizar los recursos de manera independiente de las relaciones de implementación. Las etiquetas pueden resultar útiles si necesita organizar recursos para facturación o administración.

Cada etiqueta que agrega a un recurso o a un grupo de recursos se agrega automáticamente a la taxonomía en toda la suscripción. También puede rellenar previamente la taxonomía de la suscripción con los nombres y los valores de etiquetas que desearía usar cuando los recursos se etiqueten en un futuro.

Cada recurso o grupo de recursos puede tener un máximo de 15 etiquetas. El nombre de etiqueta está limitado a 512 caracteres y el valor de la etiqueta, a 256.

> [AZURE.NOTE] Solo puede aplicar etiquetas a recursos que admiten operaciones del Administrador de recursos. Si creó una máquina virtual, una red virtual o un almacenamiento mediante el modelo de implementación clásica (por ejemplo, a través del Portal clásico), no podrá aplicar una etiqueta a ese recurso. Para admitir el etiquetado, vuelva a implementar estos recursos mediante Resource Manager. Todos los demás recursos admiten el etiquetado.

## Plantillas

Para etiquetar un recurso durante su implementación, basta con agregar el elemento **tags** al recurso que se va a implementar y especificar el nombre y valor de la etiqueta. No es necesario que el nombre y el valor de la etiqueta existan previamente en su suscripción. Puede proporcionar hasta 15 etiquetas para cada recurso.

En el ejemplo siguiente se muestra una cuenta de almacenamiento con una etiqueta.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

Actualmente, el administrador de recursos no admite el procesamiento de un objeto para los valores y los nombres de etiqueta. En su lugar, pase un objeto para los valores de etiqueta, pero de todos modos debe especificar los nombres de etiqueta, tal como se muestra en el ejemplo siguiente.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## Portal

[AZURE.INCLUDE [resource-manager-tag-resource](../includes/resource-manager-tag-resources.md)]

## PowerShell

[AZURE.INCLUDE [resource-manager-tag-resources](../includes/resource-manager-tag-resources-powershell.md)]

## Azure CLI

Las etiquetas existen directamente en los recursos y grupos de recursos. Para ver las etiquetas existentes, solo debe obtener un grupo de recursos y sus recursos con **azure group show**.

    azure group show -n tag-demo-group
    
Que devuelve metadatos sobre el grupo de recursos, incluidas las etiquetas aplicadas al mismo.
    
    info:    Executing command group show
    + Listing resource groups
    + Listing resources for the group
    data:    Id:                  /subscriptions/{guid}/resourceGroups/tag-demo-group
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production
    data:    Resources:
    data:
    data:      Id      : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    data:      Name    : tfsqlserver
    data:      Type    : servers
    data:      Location: eastus2
    data:      Tags    : Dept=Finance;Environment=Production
    ...

Para obtener las etiquetas solo para el grupo de recursos, use una utilidad de JSON como [jq](http://stedolan.github.io/jq/download/).

    azure group show -n tag-demo-group --json | jq ".tags"
    
Que devuelve las etiquetas de ese grupo de recursos.
    
    {
      "Dept": "Finance",
      "Environment": "Production" 
    }

Para ver las etiquetas de un recurso específico, use **azure resource show**.

    azure resource show -g tag-demo-group -n tfsqlserver -r Microsoft.Sql/servers -o 2014-04-01-preview --json | jq ".tags"
    
Que devuelve las etiquetas de ese recurso.
    
    {
      "Dept": "Finance",
      "Environment": "Production"
    }
    
En el ejemplo siguiente se muestra cómo recuperar todos los recursos que tienen un nombre y valor de etiqueta.

    azure resource list --json | jq ".[] | select(.tags.Dept == "Finance") | .name"
    
Que devuelve los nombres de los recursos con esa etiqueta.
    
    "tfsqlserver"
    "tfsqlserver/tfsqldata"

Las etiquetas se actualizan en conjunto. Para agregar una etiqueta a un recurso con etiquetas existentes, recupere todas las etiquetas existentes que desea conservar. Para establecer los valores de las etiquetas de un grupo de recursos, use **azure group set** y proporcione todas las etiquetas del grupo de recursos.

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
    
Se devuelve un resumen del grupo de recursos con las nuevas etiquetas.
    
    info:    Executing command group set
    ...
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production;Project=Upgrade
    ...
    
Para enumerar las etiquetas existentes en una suscripción, use **azure tag list**, mientras que para agregar una etiqueta nueva, use **azure tag create**. Para quitar una etiqueta de la taxomonía de la suscripción, primero debe quitar la etiqueta de los recursos. Luego, quite la etiqueta con **azure tag delete**.

## API de REST

Tanto el portal como PowerShell usan la [API de REST del Administrador de recursos](https://msdn.microsoft.com/library/azure/dn848368.aspx) en segundo plano. Si necesita integrar el etiquetado en otro entorno, puede obtener etiquetas con un comando GET en el identificador de recurso y actualizar el conjunto de etiquetas con una llamada PATCH.


## Etiquetas y facturación

Para los servicios compatibles, puede usar etiquetas a fin de agrupar los datos de facturación. Por ejemplo, [las máquinas virtuales integradas con el Administrador de recursos de Azure](./virtual-machines/virtual-machines-windows-compare-deployment-models.md) le permiten definir y aplicar etiquetas para organizar el uso de facturación en las máquinas virtuales. Si va a ejecutar varias máquinas virtuales para organizaciones diferentes, puede usar etiquetas para agrupar el uso por centro de costo. También puede usar etiquetas para clasificar los costos por entorno de tiempo de ejecución; por ejemplo, el uso de facturación en máquinas virtuales que se ejecutan en el entorno de producción.

Puede recuperar información sobre las etiquetas a través de las [API de RateCard y de uso de recursos de Azure](billing-usage-rate-card-overview.md) o mediante el archivo de valores separados por coma (CSV). Puede descargar el archivo de uso en el [Portal de cuentas de Azure](https://account.windowsazure.com/) o el [portal EA](https://ea.azure.com). Para obtener más información sobre el acceso a información de facturación mediante programación, vea [Obtención de información sobre el consumo de recursos de Microsoft Azure](billing-usage-rate-card-overview.md). Para las operaciones de API de REST, vea [Referencia de API de REST de facturación de Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Al descargar el CSV de uso correspondiente a los servicios que admiten etiquetas con facturación, las etiquetas aparecen en la columna **Etiquetas**. Para obtener más información, consulte [Información sobre la factura en Microsoft Azure](billing-understand-your-bill.md).

![Ver etiquetas en la facturación](./media/resource-group-using-tags/billing_csv.png)

## Pasos siguientes

- Puede aplicar restricciones y convenciones a través de su suscripción con directivas personalizadas. La directiva que define podría requerir que todos los recursos tengan un valor para una etiqueta determinada. Para más información, vea [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).
- Para obtener información sobre cómo usar Azure PowerShell al implementar recursos, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](./powershell-azure-resource-manager.md).
- Para obtener información sobre cómo usar la interfaz de la línea de comandos (CLI) de Azure al implementar recursos, consulte [Uso de la CLI de Azure para Mac, Linux y Windows con el Administrador de recursos de Azure](./xplat-cli-azure-resource-manager.md).
- Para obtener información sobre cómo usar el portal, consulte [Uso del Portal de Azure para administrar los recursos de Azure](./azure-portal/resource-group-portal.md)

<!---HONumber=AcomDC_0817_2016-->