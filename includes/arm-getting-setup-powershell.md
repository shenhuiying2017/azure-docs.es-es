## Configuración de PowerShell para plantillas del Administrador de recursos
 
Para poder usar Azure PowerShell con el Administrador de recursos, debe tener las versiones adecuadas de Windows PowerShell y PowerShell de Azure.

### Comprobar las versiones de PowerShell

Compruebe que tiene Windows PowerShell, versión 3.0 o 4.0. Para buscar la versión de Windows PowerShell, escriba este comando en un símbolo del sistema de Windows PowerShell.

	$PSVersionTable

Recibirá el siguiente tipo de información:

	Name                           Value
	----                           -----
	PSVersion                      3.0
	WSManStackVersion              3.0
	SerializationVersion           1.1.0.1
	CLRVersion                     4.0.30319.18444
	BuildVersion                   6.2.9200.16481
	PSCompatibleVersions           {1.0, 2.0, 3.0}
	PSRemotingProtocolVersion      2.2


Compruebe que el valor de **PSVersion** es 3.0 o 4.0. Si no es así, consulte [Windows Management Framework](http://www.microsoft.com/download/details.aspx?id=34595) 3.0 o [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

También debe disponer de Azure PowerShell, versión 0.9.0 o posterior. Si no ha instalado y configurado Azure PowerShell, haga clic en [aquí](powershell-install-configure.md) para obtener instrucciones.

Puede comprobar la versión de Azure PowerShell que ha instalado con este comando en el símbolo del sistema de Azure PowerShell.

	Get-Module azure | format-table version

Recibirá el siguiente tipo de información:

	Version
	-------
	0.9.0

Si no tiene la versión 0.9.0 o posterior, debe quitar Azure PowerShell mediante la opción Programas y características del Panel de control y, a continuación, instalar la versión más reciente. Para obtener más información, vea [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).

### Establecer su cuenta y suscripción de Azure

Si todavía no tiene una suscripción de Azure, puede activar sus [beneficios de suscripción a MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o bien registrarse para obtener una [evaluación gratuita](http://azure.microsoft.com/pricing/free-trial/).

Abra un símbolo del sistema de Azure PowerShell e inicie sesión en Azure con este comando.

	Add-AzureAccount

Si tiene varias suscripciones de Azure, puede enumerarlas con este comando.

	Get-AzureSubscription

Recibirá el siguiente tipo de información:

	SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
	SubscriptionName          : Visual Studio Ultimate with MSDN
	Environment               : AzureCloud
	SupportedModes            : AzureServiceManagement,AzureResourceManager
	DefaultAccount            : johndoe@contoso.com
	Accounts                  : {johndoe@contoso.com}
	IsDefault                 : True
	IsCurrent                 : True
	CurrentStorageAccountName : 
	TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Puede establecer la suscripción actual de Azure ejecutando estos comandos en el símbolo del sistema de Azure PowerShell. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por el nombre correcto.

	$subscr="<SubscriptionName from the display of Get-AzureSubscription>"
	Select-AzureSubscription -SubscriptionName $subscr -Current	

Para obtener más información acerca de las cuentas y suscripciones de Azure, vea [Conexión a su suscripción](powershell-install-configure.md#Connect).

### Cambiar al módulo Administrador de recursos de Azure

Para poder usar el módulo Administrador de recursos de Azure necesitará cambiar desde el conjunto predeterminado de comandos de Azure al conjunto de comandos de Administrador de recursos de Azure. Ejecute este comando.

	Switch-AzureMode AzureResourceManager

> [AZURE.NOTE]Puede volver al conjunto predeterminado de comandos con el comando **Switch-AzureMode AzureServiceManagement**.

<!---HONumber=62-->