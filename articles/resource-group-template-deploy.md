<properties
   pageTitle="Implementación de recursos con PowerShell y plantilla | Microsoft Azure"
   description="Use Azure Resource Manager y Azure PowerShell para implementar recursos en Azure. Los recursos se definen en una plantilla de Resource Manager."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [CLI de Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [API DE REST](resource-group-template-deploy-rest.md)
- [.NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Nodo](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)


En este tema se explica cómo usar Azure PowerShell con plantillas de Resource Manager para implementar sus recursos en Azure.

> [AZURE.TIP] Para obtener ayuda con la depuración de un error durante la implementación, consulte:
>
> - [Visualización de operaciones de implementación con Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md) para obtener información que le ayude a solucionar el error
> - [Solución de problemas comunes al implementar recursos en Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md) para obtener información sobre cómo resolver errores comunes de implementación

La plantilla puede ser un archivo local o en un archivo externo que está disponible a través de un identificador URI. Cuando la plantilla se encuentra en una cuenta de almacenamiento, puede restringir el acceso a la plantilla y proporcionar un token de firma de acceso compartido (SAS) durante la implementación.

## Pasos rápidos para la implementación

En este artículo se describen todas las diferentes opciones disponibles durante la implementación. Sin embargo, a menudo solo necesita dos sencillos comandos. Para empezar a trabajar rápidamente con la implementación, use los siguientes comandos:

    New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
    New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

Para obtener más información acerca de opciones de implementación que podrían ser más adecuadas para su escenario, siga leyendo este artículo.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## Implementación con PowerShell

1. Inicio de sesión en la cuenta de Azure

        Add-AzureRmAccount

     Se devuelve un resumen de la cuenta.

        Environment : AzureCloud
        Account     : someone@example.com
        ...

2. Si tiene varias suscripciones, proporcione el identificador de suscripción que quiera usar para la implementación con el comando **Set-AzureRmContext**.

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. Normalmente, al implementar una nueva plantilla, desea crear un grupo de recursos para contener los recursos. Si ya tiene un grupo de recursos en el que desea realizar la implementación, puede omitir este paso y simplemente usar ese grupo de recursos.

     Para crear un grupo de recursos, proporcione un nombre y una ubicación para el grupo de recursos.

        New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
     Se devuelve un resumen del grupo de recursos nuevo.
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
             Actions  NotActions
             =======  ==========
             *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

4. Antes de ejecutar la implementación, puede validar la configuración de la implementación. El cmdlet **Test-AzureRmResourceGroupDeployment** le permite buscar problemas antes de crear los recursos reales. En el ejemplo siguiente se muestra cómo validar una implementación.

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

5. Para implementar recursos en el grupo de recursos, ejecute el comando **New-AzureRmResourceGroupDeployment** y especifique los parámetros necesarios. Los parámetros incluyen un nombre para la implementación, el nombre del grupo de recursos, la ruta de acceso o dirección URL a la plantilla que creó y cualquier otro parámetro necesario para el escenario. Si no se especifica el parámetro **Mode**, se usa el valor predeterminado **Incremental**. Para ejecutar una implementación completa, establezca el **Modo** en **Completo**. Tenga cuidado al usar este modo, ya que puede eliminar accidentalmente los recursos que no estén en la plantilla.

     Para implementar una plantilla local, use el parámetro **TemplateFile**:

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>

     Para implementar una plantilla externa, use el parámetro **TemplateUri**:

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate>
   
     Tiene las opciones siguientes para proporcionar valores de parámetro:
   
     1. Use parámetros en línea.

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"

     2. Use un objeto de parámetro.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters

     3. Use un archivo de parámetro local. Para obtener información sobre el archivo de plantilla, consulte [Archivo de parámetros](#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile <PathToParameterFile>

     4. Use un archivo de parámetro externo. Para obtener información sobre el archivo de plantilla, consulte [Archivo de parámetros](#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri <LinkToParameterFile>

        Cuando se utiliza un archivo de parámetros externo, no se pueden pasar otros valores ya sea en línea o desde un archivo local. Para más información, consulte [Prioridad de parámetros](#parameter-precendence).

     Una vez implementados los recursos, verá un resumen de la implementación.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Si la plantilla incluye un parámetro con el mismo nombre que uno de los parámetros en el comando de PowerShell para implementar la plantilla, se le pedirá que proporcione un valor para ese parámetro con el sufijo **FromTemplate**. Por ejemplo, un parámetro denominado **ResourceGroupName** de su plantilla entra en conflicto con el parámetro **ResourceGroupName** del cmdlet [AzureRmResourceGroupDeployment nuevo](https://msdn.microsoft.com/library/azure/mt679003.aspx). Se le pedirá que proporcione un valor para **ResourceGroupNameFromTemplate**. Por lo general, debe evitar esta confusión no nombrando los parámetros con el mismo nombre que los parámetros utilizados para operaciones de implementación.

6. Si desea registrar más información sobre la implementación que pueda ayudarle a solucionar los errores de implementación, use el parámetro **DeploymentDebugLogLevel**. Puede especificar que se registren el contenido de la solicitud y el de la respuesta, o ambos, con la operación de implementación.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     Para más información sobre cómo usar este contenido de depuración a fin de solucionar problemas en implementaciones, consulte [Solución de problemas de implementaciones de grupo de recursos con Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md).

## Implementación de plantilla desde el almacenamiento con token de SAS

Puede agregar las plantillas a una cuenta de almacenamiento y establecer vínculos a ellas durante la implementación con un token de SAS.

> [AZURE.IMPORTANT] Siguiendo estos pasos, el blob que contiene la plantilla solo es accesible para el propietario de la cuenta. Sin embargo, cuando se crea un token de SAS para el blob, el blob es accesible para cualquier persona con ese URI. Si otro usuario intercepta el URI, ese usuario podrá tener acceso a la plantilla. El uso de un token de SAS supone un buen método para limitar el acceso a las plantillas, pero no debe incluir información confidencial como contraseñas directamente en estas.

### Adición de plantilla privada a la cuenta de almacenamiento

Con los pasos siguientes se configura una cuenta de almacenamiento para plantillas:

1. Cree un grupo de recursos.

        New-AzureRmResourceGroup -Name ManageGroup -Location "West US"

2. Cree una cuenta de almacenamiento. El nombre de la cuenta de almacenamiento debe ser único en Azure, así que indique su propio nombre para la cuenta.

        New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"

3. Configure la cuenta de almacenamiento actual.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

4. Cree un contenedor. El permiso está establecido en **Desactivado**, lo que significa que el contenedor solo es accesible para el propietario.

        New-AzureStorageContainer -Name templates -Permission Off
        
5. Agregue la plantilla al contenedor.

        Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
        
### Provisión del token de SAS durante la implementación

Para implementar una plantilla privada en una cuenta de almacenamiento, recupere un token de SAS e inclúyalo en el URI de la plantilla.

1. Si ha cambiado la cuenta de almacenamiento actual, establezca como cuenta de almacenamiento actual la que contiene las plantillas.

        Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates

2. Cree un token de SAS con permisos de lectura y un tiempo de expiración para limitar el acceso. Recupere el URI completo de la plantilla que incluye el token de SAS.

        $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

3. Implemente la plantilla proporcionando el URI que incluye el token de SAS.

        New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri

Para obtener un ejemplo del uso de un token de SAS con plantillas vinculadas, consulte [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## Prioridad de parámetros

Puede usar parámetros en línea y un archivo de parámetros local en la misma operación de implementación. Por ejemplo, puede especificar algunos valores en el archivo de parámetros local y agregar otros valores en línea durante la implementación. Si proporciona valores para un parámetro en el archivo de parámetros local y en línea, el valor en línea tiene prioridad.

Sin embargo, no puede utilizar parámetros en línea con un archivo de parámetros externo. Cuando se especifica un archivo de parámetros en el parámetro **TemplateParameterUri**, se omiten todos los parámetros en línea. Debe proporcionar todos los valores de parámetro en el archivo externo. Si la plantilla incluye un valor confidencial que no puede incluir en el archivo de parámetros, agregue ese valor a un almacén de claves y haga referencia al almacén en el archivo de parámetros externo o proporcione dinámicamente todos los valores de parámetro en línea.

Para más información sobre el uso de una referencia KeyVault para pasar valores seguros, consulte [Paso de valores seguros durante la implementación](resource-manager-keyvault-parameter.md).

## Pasos siguientes
- Para ver un ejemplo de cómo implementar los recursos mediante la biblioteca cliente de .NET, consulte [Deploy resources using .NET libraries and a template](virtual-machines/virtual-machines-windows-csharp-template.md) (Implementación de recursos de Azure mediante bibliotecas de .NET y una plantilla).
- Para definir parámetros de plantilla, consulte [Creación de plantillas](resource-group-authoring-templates.md#parameters).
- Para ver instrucciones sobre cómo implementar la solución en diferentes entornos, consulte [Entornos de desarrollo y pruebas en Microsoft Azure](solution-dev-test-environments.md).

<!---HONumber=AcomDC_0817_2016-->