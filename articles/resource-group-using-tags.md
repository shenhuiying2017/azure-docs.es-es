<properties
	pageTitle="Uso de etiquetas para organizar los recursos de Azure | Microsoft Azure"
	description="Muestra cómo aplicar etiquetas para organizar los recursos para la facturación y administración."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="AzurePortal"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="tomfitz"/>


# Uso de etiquetas para organizar los recursos de Azure

El Administrador de recursos le permite organizar recursos de manera lógica mediante la aplicación de etiquetas. Las etiquetas constan de pares clave-valor que identifican los recursos con las propiedades que define. Si desea marcar los recursos como pertenecientes a la misma categoría, aplique la misma etiqueta a esos recursos.

Cuando consulta los recursos con una etiqueta determinada, puede ver recursos de todos los grupos de recursos. No esta limitado únicamente a los recursos del mismo grupo de recursos, lo que le permite organizar los recursos de manera independiente de las relaciones de implementación. Las etiquetas pueden resultar especialmente útiles si necesita organizar recursos para facturación o administración.

Cada etiqueta que agrega a un recurso o a un grupo de recursos se agrega automáticamente a la taxonomía en toda la suscripción. También puede rellenar previamente la taxonomía de la suscripción con los nombres y los valores de etiquetas que desearía usar cuando los recursos se etiqueten en un futuro.

Cada recurso o grupo de recursos puede tener un máximo de 15 etiquetas. El nombre de etiqueta está limitado a 512 caracteres y el valor de la etiqueta, a 256.

> [AZURE.NOTE] Solo puede aplicar etiquetas a recursos que admiten operaciones del Administrador de recursos. Si creó una máquina virtual, una red virtual o un almacenamiento mediante el modelo de implementación clásica (por ejemplo, a través del Portal clásico o la API de administración de servicios), no podrá aplicar una etiqueta a ese recurso. Debe volver a implementar estos recursos mediante el Administrador de recursos para admitir el etiquetado. Todos los demás recursos admiten el etiquetado.

## Etiquetas de plantillas

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

Actualmente, el administrador de recursos no admite el procesamiento de un objeto para los valores y los nombres de etiqueta. En su lugar, puede pasar un objeto para los valores de etiqueta, pero debe especificar los nombres de etiqueta, como se muestra a continuación.

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


## Etiquetas en el portal

No es posible agregar etiquetas a los recursos y los grupos de recursos existentes a través del portal. Use el centro de exploración para navegar al recurso o grupo de recursos que le gustaría etiquetar y haga clic en la parte Etiquetas en la sección Información general que se encuentra en la parte superior de la hoja.

![Parte de etiquetas en hojas de recursos y grupos de recursos](./media/resource-group-using-tags/tag-icon.png)

Se abrirá una hoja con la lista de etiquetas que ya se han aplicado. Si se trata de la primera etiqueta, la lista estará vacía. Para agregar una etiqueta, especifique un nombre y valor y presione Entrar. Después de agregar algunas etiquetas, verá opciones de Autocompletar según los valores y nombres de etiquetas preexistentes para asegurar mejor una taxonomía coherente entre los recursos y evitar errores comunes, como por ejemplo los ortográficos.

![Recursos de etiqueta con pares de nombre/valor](./media/resource-group-using-tags/tag-resources.png)

Para ver la taxonomía de etiquetas en el portal, use el centro de exploración para ver todo y, a continuación, seleccione Etiquetas.

![Buscar etiquetas mediante el centro de exploración](./media/resource-group-using-tags/browse-tags.png)

Ancle las etiquetas más importantes en el Panel de inicio para un acceso rápido y estar preparado para trabajar. ¡Diviértase!

![Anclar etiquetas al Panel de inicio](./media/resource-group-using-tags/pin-tags.png)

## Etiquetas y PowerShell

Existen etiquetas directamente en los recursos y grupos de recursos, por lo que para ver qué etiquetas ya se aplican, podemos simplemente obtener un recurso o grupo de recursos con **Get-AzureRmResource** o **Get-AzureRmResourceGroup**. Comencemos con un grupo de recursos.

    PS C:\> Get-AzureRmResourceGroup -Name tag-demo-group

    ResourceGroupName : tag-demo-group
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production


Este cmdlet devuelve varios bits de metadatos en el grupo de recursos, incluso qué etiquetas se han aplicado, si las hay.

Al obtener los metadatos de un recurso, las etiquetas no se muestran directamente. A continuación verá que las etiquetas solo se muestran como objeto Hashtable.

    PS C:\> Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : tag-demo-group
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {System.Collections.Hashtable}

Las etiquetas reales se pueden mediante la recuperación de la propiedad **Tags**.

    PS C:\> (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group).Tags | %{ $_.Name + ": " + $_.Value }
    Dept: Finance
    Environment: Production

En lugar de ver las etiquetas de un recurso o grupo de recursos concretos, a menudo se desea recuperar todos los recursos o grupos de recursos que tienen una etiqueta y un valor concretos. Para obtener grupos de recursos con una etiqueta específica, use el cmdlet **Find-AzureRmResourceGroup** con el parámetro **-Tag**.

    PS C:\> Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }
    tag-demo-group
    web-demo-group

Para obtener todos los recursos con una etiqueta y un valor concretos, use el cmdlet **Find-AzureRmResource**.

    PS C:\> Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }
    tfsqlserver
    tfsqldatabase

Para agregar una etiqueta a un grupo de recursos que no tiene etiquetas existentes, basta con usar el comando **Set-AzureRmResourceGroup** y especificar un objeto etiqueta.

    PS C:\> Set-AzureRmResourceGroup -Name test-group -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} )

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name          Value
                    =======       =====
                    Dept          IT
                    Environment   Test
                    
Para agregar etiquetas a un recurso que no tenga etiquetas existentes, utilice el comando **SetAzureRmResource**

    PS C:\> Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

Las etiquetas se actualizan como un todo, por lo que si va a agregar una etiqueta a un recurso que ya se ha etiquetado, deberá usar una matriz con todas las etiquetas que desee conservar. Para ello, seleccione las etiquetas existentes, agregue una nuevo al conjunto y vuelva a aplicar todas las etiquetas.

    PS C:\> $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    PS C:\> $tags += @{Name="status";Value="approved"}
    PS C:\> Set-AzureRmResourceGroup -Name test-group -Tag $tags

Para quitar una o varias etiquetas, simplemente guarde la matriz sin la que desea quitar.

El proceso es el mismo para los recursos, excepto en el hecho que usará los cmdlets **Get-AzureRmResource** y **Set-AzureRmResource**.

Para obtener una lista de todas las etiquetas dentro de una suscripción usando PowerShell, use el cmdlet **Get-AzureRmTag**.

    PS C:/> Get-AzureRmTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

Puede ver las etiquetas que comienzan con hidden-" y "link:". Se trata de etiquetas internas, que se deben omitir y evitar cambiar.

Use el cmdlet **New-AzureRmTag**para agregar nuevas etiquetas a la taxonomía. Estas etiquetas se incluirán en la característica Autocompletar, aunque todavía no se hayan aplicado a los recursos o grupos de recursos. Para quitar un nombre o valor de etiqueta, quite primero la etiqueta de los recursos con los que se pueda usar y, a continuación, use el cmdlet **Remove-AzureRmTag** para quitarla de la taxonomía.

## CLI de Azure y etiquetas

Existen etiquetas directamente en los recursos y grupos de recursos, por lo que para ver qué etiquetas están ya aplicadas, no es preciso más que obtener un grupo de recursos y sus recursos con **azure group show**.

    azure group show -n tag-demo-group
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
    {
      "Dept": "Finance",
      "Environment": "Production" 
    }

Para ver las etiquetas de un recurso concreto, utilice **azure resource show**.

    azure resource show -g tag-demo-group -n tfsqlserver -r Microsoft.Sql/servers -o 2014-04-01-preview --json | jq ".tags"
    {
      "Dept": "Finance",
      "Environment": "Production"
    }
    
A continuación se muestra cómo recuperar todos los recursos con una etiqueta y un valor concretos.

    azure resource list --json | jq ".[] | select(.tags.Dept == "Finance") | .name"
    "tfsqlserver"
    "tfsqlserver/tfsqldata"

Las etiquetas se actualizan como un todo, por lo que si va a agregar una etiqueta a un recurso que ya se ha etiquetado, será preciso que recupere todas las etiquetas existentes que desee conservar. Para establecer los valores de las etiqueta de un grupo de recursos, utilice **azure group set** y proporcione todas las etiquetas del grupo de recursos.

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
    info:    Executing command group set
    ...
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production;Project=Upgrade
    ...
    
Para enumerar las etiquetas existentes en una suscripción, utilice **azure tag list**, mientras que para agregar una etiqueta nueva, utilice **azure tag create**. Para quitar una etiqueta de la taxonomía de una suscripción, quítela primero de los recursos con los que se pueda usar y, luego, quítela con **azure tag delete**.

## Etiquetas y API de REST

Tanto el portal como PowerShell usan la [API de REST del Administrador de recursos](https://msdn.microsoft.com/library/azure/dn848368.aspx) en segundo plano. Si necesita integrar el etiquetado en otro entorno, puede obtener etiquetas con un comando GET en el identificador de recurso y actualizar el conjunto de etiquetas con una llamada PATCH.


## Etiquetas y facturación

Para los servicios compatibles, puede usar etiquetas a fin de agrupar los datos de facturación. Por ejemplo, [las máquinas virtuales integradas con el Administrador de recursos de Azure](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md) le permiten definir y aplicar etiquetas para organizar el uso de facturación en las máquinas virtuales. Si va a ejecutar varias máquinas virtuales para organizaciones diferentes, puede usar etiquetas para agrupar el uso por centro de costo. También puede usar etiquetas para clasificar los costos por entorno de tiempo de ejecución; por ejemplo, el uso de facturación en máquinas virtuales que se ejecutan en el entorno de producción.

Puede recuperar información sobre las etiquetas a través de las [API de RateCard y de uso de recursos de Azure](billing-usage-rate-card-overview.md), o mediante el archivo de valores separados por coma (CSV) que puede descargar desde el [Portal de cuentas de Azure](https://account.windowsazure.com/) o el [portal de EA](https://ea.azure.com). Para obtener más información sobre el acceso a información de facturación mediante programación, vea [Obtención de información sobre el consumo de recursos de Microsoft Azure](billing-usage-rate-card-overview.md). Para las operaciones de API de REST, vea [Referencia de API de REST de facturación de Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Al descargar el CSV de uso correspondiente a los servicios que admiten etiquetas con facturación, las etiquetas aparecen en la columna **Etiquetas**. Para obtener más información, consulte [Información sobre la factura en Microsoft Azure](billing-understand-your-bill.md).

![Ver etiquetas en la facturación](./media/resource-group-using-tags/billing_csv.png)

## Pasos siguientes

- Puede aplicar restricciones y convenciones a través de su suscripción con directivas personalizadas. La directiva que defina podría requerir que se establezca una etiqueta específica para todos los recursos. Para obtener más información, consulte [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).
- Para obtener información sobre cómo usar Azure PowerShell al implementar recursos, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](./powershell-azure-resource-manager.md).
- Para obtener información sobre cómo usar la interfaz de la línea de comandos de Azure al implementar recursos, consulte [Uso de la interfaz de la línea de comandos de Azure para Mac, Linux y Windows con el Administrador de recursos de Azure](./xplat-cli-azure-resource-manager.md).
- Para obtener información sobre cómo usar el portal, consulte [Uso del Portal de Azure para administrar los recursos de Azure](./azure-portal/resource-group-portal.md)  

<!---HONumber=AcomDC_0309_2016-->