
## <a name="start-your-powershell-session"></a>Inicio de una sesión de PowerShell
Primero, debe tener la versión más reciente de [Azure PowerShell](http://msdn.microsoft.com/library/mt619274.aspx) instalada y ejecutándose. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](../articles/powershell-install-configure.md).

> [!NOTE]
> En los ejemplos de este tema se usa el [modelo de implementación de Azure Resource Manager](../articles/resource-group-overview.md) y, por consiguiente, usaremos los [cmdlets de Azure Resource Manager](http://msdn.microsoft.com/library/azure/mt125356.aspx). 
> 
> 

Ejecute el cmdlet [**Add-AzureRmAccount**](http://msdn.microsoft.com/library/mt619267.aspx) y aparecerá una pantalla de inicio de sesión para especificar sus credenciales. Use las mismas credenciales que las utilizadas para iniciar sesión en el Portal de Azure.

    Add-AzureRmAccount

Si tiene varias suscripciones, use el cmdlet [**Set-AzureRmContext**](http://msdn.microsoft.com/library/mt619263.aspx) para seleccionar la suscripción que se debe usar en la sesión de PowerShell. Para ver la suscripción que se usa en la sesión actual de PowerShell, ejecute [**Get-AzureRmContext**](http://msdn.microsoft.com/library/mt619265.aspx). Para ver todas las suscripciones, ejecute [**Get-AzureRmSubscription**](http://msdn.microsoft.com/library/mt619284.aspx).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'



<!--HONumber=Oct16_HO2-->


