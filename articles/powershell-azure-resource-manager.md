<properties 
	pageTitle="Azure PowerShell con el Administrador de recursos | Microsoft Azure" 
	description="Introducción al uso de Azure PowerShell para implementar varios recursos como un grupo de recursos en Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/11/2016" 
	ms.author="tomfitz"/>

# Uso de Azure PowerShell con Azure Resource Manager

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [CLI de Azure](xplat-cli-azure-resource-manager.md)

Administrador de recursos de Azure presenta un concepto totalmente nuevo acerca de los recursos de Azure. En lugar de crear y administrar recursos individuales, empiece por imaginar una solución entera, como un blog, una galería de fotos, un portal de SharePoint o un wiki. Use una plantilla, una representación declarativa de la solución, para crear un grupo de recursos que contiene todos los recursos que necesita para respaldar la solución. Luego, administre e implemente ese grupo de recursos como una unidad lógica.

En este tutorial, aprenderá a usar Azure PowerShell con el Administrador de recursos de Azure. Le guía por el proceso para implementar una solución y trabajar con esa solución. Se usará una plantilla de Resource Manager y Azure PowerShell para la implementación:

- Servidor SQL: para hospedar la base de datos.
- Base de datos SQL: para almacenar los datos.
- Reglas de firewall: para permitir que la aplicación web se conecte a la base de datos.
- Plan de servicio de aplicaciones: para definir las capacidades y el costo de la aplicación web.
- Sitio web: para ejecutar la aplicación web.
- Configuración web: para almacenar la cadena de conexión en la base de datos. 
- Reglas de alerta: para supervisar el rendimiento y los errores
- Application Insights: para configurar el escalado automático

## Requisitos previos

Para completar este tutorial, necesita:

- Una cuenta de Azure
  + Puede [abrir una cuenta de Azure de manera gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) - Obtiene crédito que puede usar para probar los servicios de Azure de pago, e incluso una vez agotado este, podrá mantener la cuenta y usar servicios gratuitos de Azure, como Sitios web. Nunca se la hará ningún cargo en la tarjeta de crédito, a menos que cambie explícitamente la configuración y pida que se le realice algún cargo.
  
  + Puede [activar las ventajas de suscriptor de MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - Su suscripción a MSDN le proporciona crédito todos los meses que puede usar con servicios de Azure de pago.
- Azure PowerShell 1.0. Para más información sobre esta versión y cómo instalarla, consulte [Cómo instalar y configurar Azure PowerShell](powershell-install-configure.md).

Este tutorial está diseñado para los principiantes de PowerShell, pero se asume que se conocen los conceptos básicos, como los módulos, los cmdlets y las sesiones.

## Obtención de ayuda sobre los cmdlets

Para obtener ayuda detallada con cualquier cmdlet que aparezca en este tutorial, use el cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Por ejemplo, para obtener ayuda con el cmdlet Get-AzureRmResource, escriba:

    Get-Help Get-AzureRmResource -Detailed

Para obtener una lista de los cmdlets del módulo Recursos con una sinopsis de ayuda, escriba:

    Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

El resultado será similar al siguiente extracto:

	Name                                   Synopsis
	----                                   --------
	Find-AzureRmResource                   Searches for resources using the specified parameters.
	Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
	Get-AzureRmADGroup                     Filters active directory groups.
	Get-AzureRmADGroupMember               Get a group members.
	...

Para obtener toda la ayuda posible para un cmdlet, escriba un comando con el formato:

    Get-Help <cmdlet-name> -Full
  
## Inicio de sesión en la cuenta de Azure

Antes de trabajar en la solución, debe iniciar sesión en su cuenta.

Para iniciar sesión en su cuenta de Azure, use el cmdlet **Add-AzureRmAccount**.

    Add-AzureRmAccount

El cmdlet pide las credenciales de inicio de sesión para la cuenta de Azure. Después de iniciar la sesión, se descarga la configuración de la cuenta a fin de que esté disponible para Azure PowerShell.

La configuración de la cuenta caduca, por lo que necesita actualizarla ocasionalmente. Para actualizar la configuración de la cuenta, vuelva a ejecutar **Add-AzureRmAccount**.

>[AZURE.NOTE] Los módulos de Resource Manager requieren Add-AzureRmAccount. No basta con un archivo de configuración de publicación.

Si tiene más de una suscripción, proporcione el identificador de suscripción que quiera usar para la implementación con el cmdlet **Set-AzureRmContext**.

    Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

## Crear un grupo de recursos

Antes de implementar los recursos en su suscripción, debe crear un grupo de recursos que contendrá los recursos.

Para crear un grupo de recursos, use el cmdlet **New-AzureRmResourceGroup**.

El comando usa el parámetro **Name** para especificar un nombre para el grupo de recursos y el parámetro **Location** para definir su ubicación. Según lo que hemos descubierto en la sección anterior, usaremos "Oeste de EE. UU." para la ubicación.

    New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
La salida debe ser similar a:

    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

Su grupo de recursos se ha creado correctamente.

## Implementación de la solución

En este tema no se muestra cómo crear una plantilla ni se analiza su estructura. Para más información, consulte [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md) y [Tutorial de la plantilla de Azure Resource Manager](resource-manager-template-walkthrough.md). Implementará la plantilla predefinida [Aprovisionamiento de una aplicación web con una base de datos SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) de [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).

Tiene el grupo de recursos y tiene la plantilla, así que ahora está listo para implementar en el grupo de recursos la infraestructura definida en la plantilla. Los recursos se implementan con el cmdlet **New-AzureRmResourceGroupDeployment**. La plantilla especifica muchos valores predeterminados, que se usarán para que no tenga que proporcionar valores para esos parámetros. La sintaxis básica se parece a esta:

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -administratorLogin exampleadmin -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

Especifique el grupo de recursos y la ubicación de la plantilla. Si la plantilla es un archivo local, use el parámetro **-TemplateFile** y especifique la ruta de acceso a la plantilla. Puede establecer el parámetro **-Mode** en **Incremental** o **Complete**. De forma predeterminada, el Administrador de recursos realiza una actualización incremental durante la implementación; por lo tanto, no es esencial establecer **-Mode** cuando desee **Incremental**. Para comprender las diferencias entre estos modos de implementación, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).

###Parámetros dinámicos de la plantilla

Si está familiarizado con PowerShell, sabe que puede recorrer los parámetros disponibles para un cmdlet; basta con que escriba un signo menos (-) y luego presione la tecla TAB. Esta misma funcionalidad también sirve para los parámetros que se definen en la plantilla. En cuanto escribe el nombre de la plantilla, el cmdlet captura la plantilla, la analiza y agrega los parámetros al comando de manera dinámica. De esta forma, resulta muy fácil especificar los valores de los parámetros de la plantilla.

Al escribir el comando, se le pide el parámetro obligatorio que falta, que es **administratorLoginPassword**. Y, al escribir la contraseña, el valor de cadena segura se oscurece. Esta estrategia elimina el riesgo de ofrecer una contraseña en texto sin formato.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Si la plantilla incluye un parámetro con un nombre que coincide con el de uno de los parámetros del comando usado para implementar la plantilla (como cuando incluye un parámetro denominado **ResourceGroupName** en la plantilla y este es el mismo que el parámetro **ResourceGroupName** del cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx)), se le pedirá que proporcione un valor para un parámetro con el sufijo **FromTemplate** (como **ResourceGroupNameFromTemplate**). Por lo general, debe evitar esta confusión no nombrando los parámetros con el mismo nombre de los parámetros utilizados para operaciones de implementación.

El comando se ejecuta y devuelve mensajes cuando se crean los recursos. En última instancia, verá el resultado de la implementación.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 4/11/2016 7:26:11 PM
    Mode              : Incremental
    TemplateLink      :
                Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json
                ContentVersion : 1.0.0.0
    Parameters        :
                Name             Type                       Value
                ===============  =========================  ==========
                skuName          String                     F1
                skuCapacity      Int                        1
                administratorLogin  String                  exampleadmin
                administratorLoginPassword  SecureString
                databaseName     String                     sampledb
                collation        String                     SQL_Latin1_General_CP1_CI_AS
                edition          String                     Basic
                maxSizeBytes     String                     1073741824
                requestedServiceObjectiveName  String       Basic

    Outputs           :
                Name             Type                       Value
                ===============  =========================  ==========
                siteUri          String                     websites5wdai7p2k2g4.azurewebsites.net
                sqlSvrFqdn       String                     sqlservers5wdai7p2k2g4.database.windows.net
                    
    DeploymentDebugLogLevel :

Con tan solo unos pasos, hemos creado e implementado los recursos necesarios para un sitio web complejo.

### Registro de información de depuración

Al implementar una plantilla, puede registrar información adicional sobre la solicitud y la respuesta especificando el parámetro **- DeploymentDebugLogLevel** al ejecutar **New-AzureRmResourceGroupDeployment**. Esta información puede ayudarle a solucionar errores de implementación. El valor predeterminado es **None**, lo que significa que no se registra ningún contenido de la solicitud o la respuesta. Puede especificar que se registre el contenido de la solicitud, la respuesta o ambos. Para más información sobre cómo solucionar problemas de implementación y registrar información de depuración, consulte [Solución de problemas de implementaciones de grupo de recursos con Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md). En el ejemplo siguiente, se registra el contenido de la solicitud y el contenido de la respuesta para la implementación.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -DeploymentDebugLogLevel All -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-sql-database/azuredeploy.json 

> [AZURE.NOTE] Al especificar el parámetro DeploymentDebugLogLevel, considere cuidadosamente el tipo de información que se pasa durante la implementación. Al registrar información sobre la solicitud o la respuesta, se podrían exponer datos confidenciales que se recuperan en las operaciones de implementación.


## Obtención de información acerca de los grupos de recursos

Después de crear un grupo de recursos, puede usar los cmdlets del módulo Administrador de recursos para administrar los grupos de recursos.

- Para obtener un grupo de recursos de su suscripción, use el cmdlet **Get-AzureRmResourceGroup**:

		Get-AzureRmResourceGroup -ResourceGroupName TestRG1
	
	Que devuelve la siguiente información:

		ResourceGroupName : TestRG1
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
		
		...

	Si no especifica un nombre de grupo de recursos, el cmdlet devuelve todos los grupos de recursos de la suscripción.

- Para obtener los recursos del grupo de recursos, use el cmdlet **Get-AzureRmResource** y su parámetro **ResourceGroupNameContains**. Sin parámetros, Find-AzureRmResource obtiene todos los recursos de la suscripción de Azure.

        Find-AzureRmResource -ResourceGroupNameContains TestRG1
	
     Que devuelve una lista de recursos con un formato como el siguiente:
		
        Name              : sqlservers5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/sqlservers5wdai7p2k2g4
        ResourceName      : sqlservers5wdai7p2k2g4
        ResourceType      : Microsoft.Sql/servers
        Kind              : v2.0
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
        Tags              : {System.Collections.Hashtable}
        ...
	        
- Puede usar etiquetas para organizar lógicamente los recursos de su suscripción y recuperar recursos con los cmdlets **Find-AzureRmResource** y **Find-AzureRmResourceGroup**.

        Find-AzureRmResource -TagName displayName -TagValue Website

        Name              : webSites5wdai7p2k2g4
        ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Web/sites/webSites5wdai7p2k2g4
        ResourceName      : webSites5wdai7p2k2g4
        ResourceType      : Microsoft.Web/sites
        ResourceGroupName : TestRG1
        Location          : westus
        SubscriptionId    : {guid}
                
      Hay mucho más que puede hacer con las etiquetas. Para obtener más información, vea [Uso de etiquetas para organizar los recursos de Azure](resource-group-using-tags.md).

## Incorporación a un grupo de recursos

Para agregar un recurso al grupo de recursos, puede usar el cmdlet **New-AzureRmResource**. Sin embargo, agregar un recurso de esta manera puede dar pie a confusión en un futuro porque el nuevo recurso no existe en la plantilla. Si volviera a implementar la plantilla antigua, implementaría una solución incompleta. Si implementa con frecuencia, le resultará más fácil y más confiable agregar el recurso nuevo a la plantilla y volver a implementarla.

## Movimiento de un recurso

Puede mover recursos existentes a un nuevo grupo de recursos. Para ver ejemplos, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](resource-group-move-resources.md).

## Exportación de la plantilla

Para un grupo de recursos existente (que se implementa mediante PowerShell o uno de los otros métodos, como el portal), puede ver la plantilla de Resource Manager para el grupo de recursos. Exportar la plantilla ofrece dos ventajas:

1. Puede automatizar fácilmente las futuras implementaciones de la solución porque toda la infraestructura está definida en la plantilla.

2. Para familiarizarse con la sintaxis de la plantilla, consulte la notación de objetos JavaScript (JSON) que representa la solución.

Con PowerShell, puede generar una plantilla que representa el estado actual de su grupo de recursos o recuperar la plantilla que se usó para una implementación concreta.

Exportar la plantilla para un grupo de recursos resulta útil cuando se han realizado cambios en un grupo de recursos y necesita recuperar la representación JSON del estado actual. Sin embargo, la plantilla generada contiene solo un número mínimo de parámetros y ninguna variable. La mayoría de los valores de la plantilla está codificados. Antes de implementar la plantilla generada, quizás desee convertir más valores en parámetros para poder personalizar la implementación para distintos entornos.

Exportar la plantilla para una implementación concreta es útil si necesita ver la plantilla real que se usó para implementar recursos. La plantilla incluirá todos los parámetros y las variables definidas para la implementación original. Sin embargo, si alguien de su organización ha realizado cambios en el grupo de recursos fuera de lo que se define en la plantilla, esta plantilla no representará el estado actual del grupo de recursos.

> [AZURE.NOTE] La característica de exportación de la plantilla es una versión preliminar y no todos los tipos de recursos admiten actualmente la exportación de una plantilla. Al intentar exportar una plantilla, verá un error que indica que algunos recursos no se han exportado. Si es necesario, puede definir manualmente estos recursos en la plantilla después de descargarla.

###Exportación de la plantilla desde el grupo de recursos

Para ver la plantilla de un grupo de recursos, ejecute el cmdlet **Export-AzureRmResourceGroup**.

    Export-AzureRmResourceGroup -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\TestRG1.json
    
###Descarga de la plantilla de la implementación

Para descargar la plantilla usada para una implementación determinada, ejecute el cmdlet **Save-AzureRmResourceGroupDeploymentTemplate**.

    Save-AzureRmResourceGroupDeploymentTemplate -DeploymentName azuredeploy -ResourceGroupName TestRG1 -Path c:\Azure\Templates\Downloads\azuredeploy.json

## Eliminación de recursos o grupo de recursos

- Para eliminar un recurso del grupo de recursos, use el cmdlet **Remove-AzureRmResource**. Este cmdlet elimina el recurso, pero no el grupo de recursos.

	Este comando quita el sitio web TestSite del grupo de recursos TestRG1.

		Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- Para eliminar un grupo de recursos, use el cmdlet **Remove-AzureRmResourceGroup**. Este cmdlet elimina el grupo de recursos y sus recursos.

		Remove-AzureRmResourceGroup -Name TestRG1
		
	Se le pide que confirme la eliminación.
		
		Confirm
		Are you sure you want to remove resource group 'TestRG1'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

## Script de implementación

Los ejemplos de implementación anteriores en este tema solo mostraban los cmdlets individuales necesarios para implementar recursos en Azure. En el ejemplo siguiente se muestra un script de implementación que crea el grupo de recursos e implementa los recursos.

    <#
      .SYNOPSIS
      Deploys a template to Azure
      
      .DESCRIPTION
      Deploys an Azure Resource Manager template

      .PARAMETER subscriptionId
      The subscription id where the template will be deployed.

      .PARAMETER resourceGroupName
      The resource group where the template will be deployed. Can be the name of an existing or a new resource group.

      .PARAMETER resourceGroupLocation
      Optional, a resource group location. If specified, will try to create a new resource group in this location. If not specified, assumes resource group is existing.

      .PARAMETER deploymentName
      The deployment name.

      .PARAMETER templateFilePath
      Optional, path to the template file. Defaults to template.json.

      .PARAMETER parametersFilePath
      Optional, path to the parameters file. Defaults to parameters.json. If file is not found, will prompt for parameter values based on template.
    #>

    param(
      [Parameter(Mandatory=$True)]
      [string]
      $subscriptionId,

      [Parameter(Mandatory=$True)]
      [string]
      $resourceGroupName,

      [string]
      $resourceGroupLocation,

      [Parameter(Mandatory=$True)]
      [string]
      $deploymentName,

      [string]
      $templateFilePath = "template.json",

      [string]
      $parametersFilePath = "parameters.json"
    )

    #******************************************************************************
    # Script body
    # Execution begins here 
    #******************************************************************************
    $ErrorActionPreference = "Stop"

    # sign in
    Write-Host "Logging in...";
    Add-AzureRmAccount;

    # select subscription
    Write-Host "Selecting subscription '$subscriptionId'";
    Set-AzureRmContext -SubscriptionID $subscriptionId;

    #Create or check for existing resource group
    $resourceGroup = Get-AzureRmResourceGroup -Name $resourceGroupName -ErrorAction SilentlyContinue
    if(!$resourceGroup)
    {
      Write-Host "Resource group '$resourceGroupName' does not exist. To create a new resource group, please enter a location.";
      if(!$resourceGroupLocation) {
        $resourceGroupLocation = Read-Host "resourceGroupLocation";
      }
      Write-Host "Creating resource group '$resourceGroupName' in location '$resourceGroupLocation'";
      New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
    }
    else{
      Write-Host "Using existing resource group '$resourceGroupName'";
    }

    # Start the deployment
    Write-Host "Starting deployment...";
    if(Test-Path $parametersFilePath) {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -TemplateParameterFile $parametersFilePath;
    } else {
      New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath;
    }

## Pasos siguientes

- Para más información sobre la creación de plantillas del Administrador de recursos, consulte [Creación de plantillas del Administrador de recursos de Azure](./resource-group-authoring-templates.md).
- Para obtener información sobre cómo implementar plantillas, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](./resource-group-template-deploy.md).
- Para ver un ejemplo detallado de cómo implementar un proyecto, consulte [Implementación predecible de microservicios en Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Para información sobre la solución de problemas de una implementación que da error, consulte [Solución de problemas de implementaciones de grupos de recursos en Azure](./resource-manager-troubleshoot-deployments-powershell.md).

<!---HONumber=AcomDC_0525_2016-->