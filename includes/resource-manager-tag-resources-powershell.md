### Cambios en el cmdlet de las etiquetas en la versión más reciente de PowerShell
La versión de agosto de 2016 de [Azure PowerShell 2.0][powershell] incluye cambios importantes en relación con la manera de trabajar con las etiquetas. Antes de continuar, compruebe la versión del módulo AzureRm.Resources.

    Get-Module -ListAvailable -Name AzureRm.Resources | Select Version

Si actualizó por última vez Azure PowerShell antes de agosto de 2016, los resultados deben mostrar una versión inferior a 3.0.

    Version
    -------
    2.0.2

Si ha actualizado Azure PowerShell después de agosto de 2016, los resultados deben mostrar una versión 3.0.

    Version
    -------
    3.0.1

Si la versión del módulo es 3.0.1 o superior, ya cuenta con los cmdlets más recientes para trabajar con etiquetas. Esta versión del módulo de recursos de Azure se instala automáticamente al instalar o actualizar Azure PowerShell mediante la Galería de PowerShell, PowerShellGet o el Instalador de plataforma Web. Si su versión es anterior a la 3.0.1, puede seguir utilizando esa versión, pero debe considerar la actualización a la versión más reciente. La versión más reciente incluye cambios que facilitan el trabajo con las etiquetas. En este tema se muestran ambos enfoques.

### Actualización del script para los cambios de la versión más reciente
En la versión más reciente, el nombre de parámetro **Tags** cambió a **Tag**, y el tipo cambió de **Hashtable** a **Hashtable**. Ya no necesita proporcionar información en los campos **Name** y **Value** de cada entrada. En su lugar, proporcione pares clave-valor en el formato **Key = "Value"**; es decir, primero la clave y luego el valor entrecomillado.

Para actualizar el script existente, cambie el parámetro **Tags** a **Tag** y cambie el formato de etiqueta como se muestra en el ejemplo siguiente.

    # Old
    New-AzureRmResourceGroup -Tags @{ Name = "testtag"; Value = "testval" } -Name $resourceGroupName -Location $location

    # New
    New-AzureRmResourceGroup -Tag @{ testtag = "testval" } -Name $resourceGroupName -Location $location 

No obstante, tenga en cuenta que los grupos de recursos y los recursos siguen devolviendo una propiedad **Tags** en sus metadatos. Esta propiedad no se ha modificado.

### Versión 3.0.1 o posterior
Las etiquetas existen directamente en los recursos y grupos de recursos. Para ver las etiquetas existentes, visualice un recurso con **Get-AzureRmResource** o un grupo de recursos con **Get-AzureRmResourceGroup**.

Comencemos con un grupo de recursos.

    Get-AzureRmResourceGroup -Name testrg1

Este cmdlet devuelve varios bits de metadatos en el grupo de recursos, incluso qué etiquetas se han aplicado, si las hay.

    ResourceGroupName : testrg1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production

Para recuperar los metadatos de recursos, incluidas las etiquetas, utilice el siguiente ejemplo.

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1

Verá los nombres de etiqueta en los resultados.

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : testrg1
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {Dept, Environment}

Utilice la propiedad **Tags** para obtener los nombres y los valores de las etiquetas.

    (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1).Tags

Lo cual devuelve los siguientes resultados:

    Name                   Value
    ----                   -----
    Dept                   Finance
    Environment            Production

En lugar de ver las etiquetas de un recurso o un grupo de recursos concreto, a menudo se desea recuperar todos los recursos o grupos de recursos con una etiqueta y un valor concretos. Para obtener grupos de recursos con una etiqueta específica, use el cmdlet **Find-AzureRmResourceGroup** con el parámetro **-Tag**.

Para recuperar grupos de recursos con un valor de etiqueta, utilice el formato siguiente.

    (Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 

Para obtener todos los recursos con una etiqueta y un valor concretos, use el cmdlet **Find-AzureRmResource**.

    (Find-AzureRmResource -TagName Dept -TagValue Finance).Name

Para agregar una etiqueta a un grupo de recursos que no tiene etiquetas existentes, use el comando **Set-AzureRmResourceGroup** y especifique un objeto de etiqueta.

    Set-AzureRmResourceGroup -Name test-group -Tag @{ Dept="IT"; Environment="Test" }

Que devuelve el grupo de recursos con sus nuevos valores de etiqueta.

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name          Value
                    =======       =====
                    Dept          IT
                    Environment   Test

Para agregar etiquetas a un recurso que no tenga etiquetas existentes, use el comando **Set-AzureRmResource**.

    Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

Las etiquetas se actualizan en conjunto. Si desea agregar una etiqueta a un recurso que tiene otras etiquetas, use una matriz con todas las etiquetas que desea conservar. En primer lugar, seleccione las etiquetas existentes, agregue una a ese conjunto y vuelva a aplicar todas las etiquetas.

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Status="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

Para quitar una o varias etiquetas, simplemente guarde la matriz sin la que desea quitar.

El proceso es el mismo para los recursos, excepto en el hecho de que usa los cmdlets **Get-AzureRmResource** y **Set-AzureRmResource**.

Para obtener una lista de todas las etiquetas dentro de una suscripción usando PowerShell, use el cmdlet **Get-AzureRmTag**.

    Get-AzureRmTag

Lo cual devuelve los nombres de las etiquetas y la cantidad de recursos y grupos de recursos con la etiqueta.

    Name                      Count
    ----                      ------
    Dept                       8
    Environment                8

Puede ver las etiquetas que comienzan con hidden-" y "link:". Se trata de etiquetas internas, las que debe omitir y evitar cambiar.

Use el cmdlet **New-AzureRmTag**para agregar nuevas etiquetas a la taxonomía. Estas etiquetas se incluyen en la característica Autocompletar, aunque todavía no se hayan aplicado a los recursos o grupos de recursos. Para quitar un nombre o valor de etiqueta, quite primero la etiqueta de los recursos con los que se pueda usar y, a continuación, use el cmdlet **Remove-AzureRmTag** para quitarla de la taxonomía.

### Versiones anteriores a 3.0.1
Las etiquetas existen directamente en los recursos y grupos de recursos. Para ver las etiquetas existentes, visualice un recurso con **Get-AzureRmResource** o un grupo de recursos con **Get-AzureRmResourceGroup**.

Comencemos con un grupo de recursos.

    Get-AzureRmResourceGroup -Name testrg1

Este cmdlet devuelve varios bits de metadatos en el grupo de recursos, incluso qué etiquetas se han aplicado, si las hay.

    ResourceGroupName : testrg1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production

Para recuperar los metadatos de recursos, utilice el siguiente ejemplo. Los metadatos de recursos no muestran etiquetas directamente.

    Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName testrg1

En los resultados puede ver que las etiquetas solo se muestran como objeto de tabla hash.

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : tag-demo-group
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {System.Collections.Hashtable}

Las etiquetas reales se pueden ver mediante la recuperación de la propiedad **Tags**.

    (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group).Tags | %{ $_.Name + ": " + $_.Value }

Que devuelve resultados con formato:

    Dept: Finance
    Environment: Production

En lugar de ver las etiquetas de un recurso o un grupo de recursos concreto, a menudo se desea recuperar todos los recursos o grupos de recursos con una etiqueta y un valor concretos. Para obtener grupos de recursos con una etiqueta específica, use el cmdlet **Find-AzureRmResourceGroup** con el parámetro **-Tag**.

Para recuperar grupos de recursos con un valor de etiqueta, utilice el formato siguiente.

    Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }

Para obtener todos los recursos con una etiqueta y un valor concretos, use el cmdlet Find-AzureRmResource.

    Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }

Para agregar una etiqueta a un grupo de recursos que no tiene etiquetas existentes, basta con usar el comando Set-AzureRmResourceGroup y especificar un objeto de etiqueta.

    Set-AzureRmResourceGroup -Name test-group -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} )

Que devuelve el grupo de recursos con sus nuevos valores de etiqueta.

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                Name          Value
                =======       =====
                Dept          IT
                Environment   Test

Para agregar etiquetas a un recurso que no tenga etiquetas existentes, use el comando Set-AzureRmResource.

    Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

Las etiquetas se actualizan en conjunto. Si desea agregar una etiqueta a un recurso que tiene otras etiquetas, use una matriz con todas las etiquetas que desea conservar. En primer lugar, seleccione las etiquetas existentes, agregue una a ese conjunto y vuelva a aplicar todas las etiquetas.

    $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    $tags += @{Name="status";Value="approved"}
    Set-AzureRmResourceGroup -Name test-group -Tag $tags

Para quitar una o varias etiquetas, simplemente guarde la matriz sin la que desea quitar.

El proceso es el mismo para los recursos, excepto en el hecho de que usa los cmdlets Get-AzureRmResource y Set-AzureRmResource.

Para obtener una lista de todas las etiquetas dentro de una suscripción usando PowerShell, use el cmdlet **Get-AzureRmTag**.

    Get-AzureRmTag

Lo cual devuelve los nombres de las etiquetas y la cantidad de recursos y grupos de recursos con la etiqueta.

    Name                      Count
    ----                      ------
    Dept                       8
    Environment                8

Puede ver las etiquetas que comienzan con hidden-" y "link:". Se trata de etiquetas internas, las que debe omitir y evitar cambiar.

Use el cmdlet **New-AzureRmTag**para agregar nuevas etiquetas a la taxonomía. Estas etiquetas se incluyen en la característica Autocompletar, aunque todavía no se hayan aplicado a los recursos o grupos de recursos. Para quitar un nombre o valor de etiqueta, quite primero la etiqueta de los recursos con los que se pueda usar y, a continuación, use el cmdlet **Remove-AzureRmTag** para quitarla de la taxonomía.

[powershell]: https://msdn.microsoft.com/library/mt619274(v=azure.200).aspx

<!---HONumber=AcomDC_0907_2016-->