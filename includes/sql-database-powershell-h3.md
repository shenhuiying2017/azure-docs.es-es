
### <a name="start-your-powershell-session"></a>Inicio de una sesión de PowerShell
Primero, debe tener la versión más reciente de [Azure PowerShell](https://msdn.microsoft.com/library/mt619274\(v=azure.300\).aspx) instalada y ejecutándose. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Muchas de las nuevas características de SQL Database solo se admiten cuando se utiliza el [modelo de implementación de Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md), por lo que los ejemplos emplean los [cmdlets de PowerShell de Azure SQL Database](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx) para Resource Manager. Se admiten los [cmdlets de administración de servicios de Azure SQL Database](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) del modelo de implementación clásica de administración de servicios por compatibilidad con versiones anteriores, pero se recomienda usar los cmdlets de Resource Manager.
> 
> 

Ejecute el cmdlet [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/azure/mt619267\(v=azure.300\).aspx) y aparecerá una pantalla de inicio de sesión para especificar sus credenciales. Use las mismas credenciales que las utilizadas para iniciar sesión en el Portal de Azure.

    Add-AzureRmAccount

Si tiene varias suscripciones, use el cmdlet [**Set-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619263\(v=azure.300\).aspx) para seleccionar la suscripción que se debe usar en la sesión de PowerShell. Para ver la suscripción que se usa en la sesión actual de PowerShell, ejecute [**Get-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619265\(v=azure.300\).aspx). Para ver todas las suscripciones, ejecute [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/azure/mt619284\(v=azure.300\).aspx).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'


<!--HONumber=Dec16_HO2-->


