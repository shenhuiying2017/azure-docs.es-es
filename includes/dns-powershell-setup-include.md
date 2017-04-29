## <a name="set-up-azure-powershell-for-azure-dns"></a>Configuración de Azure PowerShell para Azure DNS

### <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar con la configuración, compruebe que dispone de los elementos siguientes.

* Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Necesitará instalar la versión más reciente de los cmdlets de PowerShell de Azure Resource Manager. Para más información, vea [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="sign-in-to-your-azure-account"></a>Inicio de sesión en la cuenta de Azure.

Abre la consola de PowerShell y conéctate a tu cuenta. Para más información, consulte [Uso de PowerShell con Resource Manager](../articles/azure-resource-manager/powershell-azure-resource-manager.md).

```powershell
Login-AzureRmAccount
```

### <a name="select-the-subscription"></a>Selección de la suscripción
 
Compruebe las suscripciones para la cuenta.

```powershell
Get-AzureRmSubscription
```

Elección de la suscripción de Azure que se va a usar.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta ubicación se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Sin embargo, puesto que todos los recursos DNS son globales y no regionales, la elección de la ubicación del grupo de recursos no incide en DNS de Azure.

Puede omitir este paso si utiliza un grupo de recursos existente.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Registro del proveedor de recursos

El proveedor de recursos Microsoft.Network administra el servicio DNS de Azure. La suscripción a Azure debe estar registrada para usar este proveedor de recursos antes de utilizar Azure DNS. Se trata de una operación única para cada suscripción.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```