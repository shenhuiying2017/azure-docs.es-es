
## Inicio de una sesión de PowerShell
Primero, debe tener la versión más reciente de [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) instalada y ejecutándose. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../articles/powershell-install-configure.md).

> [!NOTE]
> Muchas de las nuevas características de Base de datos SQL solo se admiten cuando utiliza el [modelo de implementación de Azure Resource Manager](../articles/resource-group-overview.md), por lo que los ejemplos utilizan los [cmdlets de PowerShell de Base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) para Resource Manager. Se admiten los [cmdlets de Base de datos SQL de Azure (clásicos)](https://msdn.microsoft.com/library/azure/dn546723.aspx) del modelo de implementación clásica existentes por compatibilidad con versiones anteriores, pero se recomienda usar los cmdlets de Resource Manager.
> 
> 

Ejecute el cmdlet [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/mt619267.aspx) y aparecerá una pantalla de inicio de sesión para especificar sus credenciales. Use las mismas credenciales que las utilizadas para iniciar sesión en el Portal de Azure.

    Add-AzureRmAccount

Si tiene varias suscripciones, use el cmdlet [**Set-AzureRmContext**](https://msdn.microsoft.com/library/mt619263.aspx) para seleccionar la suscripción que la sesión de PowerShell debe utilizar. Para ver la suscripción que la sesión actual de PowerShell utiliza, ejecute [**Get-AzureRmContext**](https://msdn.microsoft.com/library/mt619265.aspx). Para ver todas las suscripciones, ejecute [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/mt619284.aspx).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

<!---HONumber=AcomDC_0803_2016-->