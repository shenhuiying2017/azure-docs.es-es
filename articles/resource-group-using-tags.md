<properties 
	pageTitle="Uso de etiquetas para organizar los recursos de Azure" 
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
	ms.date="08/07/2015" 
	ms.author="tomfitz"/>


# Uso de etiquetas para organizar los recursos de Azure

El Administrador de recursos le permite organizar recursos de manera lógica mediante la aplicación de etiquetas. Las etiquetas constan de pares clave-valor que identifican los recursos con las propiedades que define. Si desea marcar los recursos como pertenecientes a la misma categoría, aplique la misma etiqueta a esos recursos.

Cuando consulta los recursos con una etiqueta determinada, puede ver recursos de todos los grupos de recursos. No esta limitado únicamente a los recursos del mismo grupo de recursos, lo que le permite organizar los recursos de manera independiente de las relaciones de implementación. Las etiquetas pueden resultar especialmente útiles si necesita organizar recursos para facturación o administración.

Cada etiqueta que agrega a un recurso o a un grupo de recursos se agrega automáticamente a la taxonomía en toda la suscripción. También puede rellenar previamente la taxonomía de la suscripción con los nombres y los valores de etiquetas que desearía usar cuando los recursos se etiqueten en un futuro.

> [AZURE.NOTE]Solo puede aplicar etiquetas a recursos que admiten operaciones del Administrador de recursos. Si creó una máquina virtual, una red virtual o un almacenamiento a través del modelo de implementación clásica (por ejemplo, a través del Portal de Azure o la [API de administración de servicios](https://msdn.microsoft.com/library/azure/dn948465.aspx)), no puede aplicar una etiqueta a ese recurso. Debe volver a implementar estos recursos mediante el Administrador de recursos para admitir el etiquetado. Todos los demás recursos admiten el etiquetado.


## Etiquetas del Portal de vista previa

Etiquetar recursos y grupos de recursos es fácil en el portal de vista previa. Use el centro de exploración para navegar al recurso o grupo de recursos que le gustaría etiquetar y haga clic en la parte Etiquetas en la sección Información general que se encuentra en la parte superior de la hoja.

![Parte de etiquetas en hojas de recursos y grupos de recursos](./media/resource-group-using-tags/tag-icon.png)

Se abrirá una hoja con la lista de etiquetas que ya se han aplicado. Si se trata de la primera etiqueta, la lista estará vacía. Para agregar una etiqueta, especifique un nombre y valor y presione Entrar. Después de agregar algunas etiquetas, verá opciones de Autocompletar según los valores y nombres de etiquetas preexistentes para asegurar mejor una taxonomía coherente entre los recursos y evitar errores comunes, como por ejemplo los ortográficos.

![Recursos de etiqueta con pares de nombre/valor](./media/resource-group-using-tags/tag-resources.png)

Para ver la taxonomía de etiquetas en el portal, use el centro de exploración para ver todo y, a continuación, seleccione Etiquetas.

![Buscar etiquetas mediante el centro de exploración](./media/resource-group-using-tags/browse-tags.png)

Ancle las etiquetas más importantes en el Panel de inicio para un acceso rápido y estar preparado para trabajar. ¡Diviértase!

![Anclar etiquetas al Panel de inicio](./media/resource-group-using-tags/pin-tags.png)

## Etiquetado con PowerShell

Si todavía no ha utilizado Azure PowerShell con el Administrador de recursos, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md) Para la finalidad de este artículo, supondremos que ya se ha agregado una cuenta y ha seleccionado una suscripción con los recursos que desea etiquetar.

El etiquetado solo está disponible para recursos y grupos de recursos disponibles en el [Administrador de recursos](http://msdn.microsoft.com/library/azure/dn790568.aspx), por lo que lo siguiente que debemos hacer es cambiar para usar dicho administrador.

    Switch-AzureMode AzureResourceManager

Existen etiquetas directamente en los recursos y grupos de recursos, por lo que para ver qué etiquetas se aplican ya, podemos simplemente obtener un recurso o grupo de recursos con `Get-AzureResource` o `Get-AzureResourceGroup`, respectivamente. Comencemos con un grupo de recursos.

    PS C:\> Get-AzureResourceGroup tag-demo

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    Resources         :
                    Name                             Type                                  Location
                    ===============================  ====================================  ==============
                    CPUHigh ExamplePlan              microsoft.insights/alertrules         eastus
                    ForbiddenRequests tag-demo-site  microsoft.insights/alertrules         eastus
                    LongHttpQueue ExamplePlan        microsoft.insights/alertrules         eastus
                    ServerErrors tag-demo-site       microsoft.insights/alertrules         eastus
                    ExamplePlan-tag-demo             microsoft.insights/autoscalesettings  eastus
                    tag-demo-site                    microsoft.insights/components         centralus
                    ExamplePlan                      Microsoft.Web/serverFarms             southcentralus
                    tag-demo-site                    Microsoft.Web/sites                   southcentralus


Este cmdlet devuelve varios bits de metadatos en el grupo de recursos, incluso qué etiquetas se han aplicado, si las hay. Para etiquetar un grupo de recursos, simplemente use el comando `Set-AzureResourceGroup` y especifique un nombre y valor de etiqueta.

    PS C:\> Set-AzureResourceGroup tag-demo -Tag @( @{ Name="project"; Value="tags" }, @{ Name="env"; Value="demo"} )

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name     Value
                    =======  =====
                    project  tags
                    env      demo

Las etiquetas se actualizan como un todo, por lo que si va a agregar una etiqueta a un recurso que ya se ha etiquetado, deberá usar una matriz con todas las etiquetas que desee conservar. Para ello, puede seleccionar primero las etiquetas existentes y agregar una nueva.

    PS C:\> $tags = (Get-AzureResourceGroup -Name tag-demo).Tags
    PS C:\> $tags += @{Name="status";Value="approved"}
    PS C:\> Set-AzureResourceGroup tag-demo -Tag $tags

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name     Value
                    =======  ========
                    project  tags
                    env      demo
                    status   approved


Para quitar una o varias etiquetas, simplemente guarde la matriz sin la que desea quitar.

El proceso es el mismo para los recursos, excepto en que va a usar los cmdlets `Get-AzureResource` y `Set-AzureResource`. Para obtener recursos o grupos de recursos con una etiqueta específica, use el cmdlet `Get-AzureResource` o `Get-AzureResourceGroup` con el parámetro `-Tag`.

    PS C:\> Get-AzureResourceGroup -Tag @{ Name="env"; Value="demo" } | %{ $_.ResourceGroupName }
    rbacdemo-group
    tag-demo
    PS C:\> Get-AzureResource -Tag @{ Name="env"; Value="demo" } | %{ $_.Name }
    rbacdemo-web
    rbacdemo-docdb
    ...

Para obtener una lista de todas las etiquetas dentro de una suscripción usando PowerShell, use el cmdlet `Get-AzureTag`.

    PS C:/> Get-AzureTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

Puede ver las etiquetas que comienzan con hidden-" y "link:". Se trata de etiquetas internas, que se deben omitir y evitar cambiar.

Use el cmdlet `New-AzureTag` para agregar nuevas etiquetas a la taxonomía. Estas etiquetas se incluirán en la característica Autocompletar, aunque todavía no se hayan aplicado a los recursos o grupos de recursos. Para quitar un nombre o valor de etiqueta, quite primero la etiqueta de los recursos con los que se pueda usar y, a continuación, use el cmdlet `Remove-AzureTag` para quitarla de la taxonomía.

## Etiquetado con API de REST

Tanto el portal como PowerShell usan la [API de REST del Administrador de recursos](http://msdn.microsoft.com/library/azure/dn790568.aspx) en segundo plano. Si necesita integrar el etiquetado en otro entorno, puede obtener etiquetas con un comando GET en el identificador de recurso y actualizar el conjunto de etiquetas con una llamada PATCH.


## Etiquetado y facturación

Para los servicios compatibles, puede usar etiquetas a fin de agrupar los datos de facturación. Por ejemplo, [las máquinas virtuales integradas con el Administrador de recursos de Azure](/virtual-machines/virtual-machines-azurerm-versus-azuresm.md) le permiten definir y aplicar etiquetas para organizar el uso de facturación en las máquinas virtuales. Si va a ejecutar varias máquinas virtuales para organizaciones diferentes, puede usar etiquetas para agrupar el uso por centro de costo. También puede usar etiquetas para clasificar los costos por entorno de tiempo de ejecución; por ejemplo, el uso de facturación en máquinas virtuales que se ejecutan en el entorno de producción.

Puede recuperar información sobre las etiquetas a través de la [API de uso](billing-usage-rate-card-overview.md) o el archivo de valores separados por comas (CSV) de uso que puede descargar en el [portal de cuentas de Azure](https://account.windowsazure.com/) o en el [portal EA](https://ea.azure.com). Para obtener más información sobre el acceso a información de facturación mediante programación, vea [Obtención de información sobre el consumo de recursos de Microsoft Azure](billing-usage-rate-card-overview.md).

Al descargar el CSV de uso correspondiente a los servicios que admiten etiquetas con facturación, las etiquetas aparecen en la columna **Etiquetas**. Para obtener más información, consulte [Información sobre la factura en Microsoft Azure](billing-understand-your-bill.md).

![Ver etiquetas en la facturación](./media/resource-group-using-tags/billing_csv.png)

## Pasos siguientes

- Para obtener información sobre cómo usar Azure PowerShell cuando se implementan recursos, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](./powershell-azure-resource-manager.md).
- Para obtener información sobre cómo usar la interfaz de línea de comandos de Azure cuando se implementan recursos, consulte [Uso de la línea de comandos de Azure para Mac, Linux y Windows con el Administrador de recursos de Azure](./xplat-cli-azure-resource-manager.md).
- Para obtener información sobre cómo usar el Portal de vista previa, consulte [Uso del Portal de vista previa de Azure para administrar los recursos de Azure](./resource-group-portal.md)  
  

  

<!---HONumber=August15_HO7-->