---
title: "Implementación de recursos con la plantilla y PowerShell | Microsoft Docs"
description: Use Azure Resource Manager y Azure PowerShell para implementar recursos en Azure. Los recursos se definen en una plantilla de Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 7b4988ecc1594b5c9fba8554173ccbf167260898


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [CLI de Azure](resource-group-template-deploy-cli.md)
> * [Portal](resource-group-template-deploy-portal.md)
> * [API DE REST](resource-group-template-deploy-rest.md)
> 
> 

En este tema se explica cómo usar Azure PowerShell con plantillas de Resource Manager para implementar sus recursos en Azure. La plantilla puede ser un archivo local o en un archivo externo que está disponible a través de un identificador URI. Cuando la plantilla se encuentra en una cuenta de almacenamiento, puede restringir el acceso a la plantilla y proporcionar un token de firma de acceso compartido (SAS) durante la implementación.

> [!TIP]
> Para obtener ayuda con la depuración de un error durante la implementación, consulte:
> 
> * [Visualización de operaciones de implementación con Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md) para obtener información que le ayude a solucionar el error
> * [Solución de problemas comunes al implementar recursos en Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md) para obtener información sobre cómo resolver errores comunes de implementación
> 
> 

## <a name="quick-steps-to-deployment"></a>Pasos rápidos para la implementación
Para empezar a trabajar rápidamente con la implementación, use los siguientes comandos:

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json -TemplateParameterFile c:\MyTemplates\example.params.json
```

Estos comandos crean un grupo de recursos e implementan una plantilla en dicho grupo de recursos. El archivo de plantilla y parámetros son locales. Si funciona, tiene todo lo que necesita para implementar recursos. Sin embargo, hay más opciones disponibles para especificar los recursos que se van a implementar. En el resto de este artículo se describen todas las diferentes opciones disponibles durante la implementación. 

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy"></a>Implementación
1. Inicio de sesión en la cuenta de Azure

   ```powershell
   Add-AzureRmAccount
   ```

    Se devuelve un resumen de la cuenta.
    
   ```powershell
   Environment           : AzureCloud
   Account               : someone@example.com
   TenantId              : {guid}
   SubscriptionId        : {guid}
   SubscriptionName      : Example Subscription
   CurrentStorageAccount :
   ```

2. Si tiene varias suscripciones, proporcione el identificador de suscripción que quiera usar para la implementación con el comando **Set-AzureRmContext**. 
   
   ```powershell
   Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
   ```
3. Al implementar una plantilla, debe especificar un grupo de recursos que contendrá los recursos implementados. Si ya tiene un grupo de recursos en el que desea realizar la implementación, puede omitir este paso y usar ese grupo. 
   
     Para crear un grupo de recursos, proporcione un nombre y una ubicación para el grupo de recursos. Debe proporcionar una ubicación para el grupo de recursos porque este almacena metadatos sobre los recursos. Por motivos de cumplimiento, debería especificar dónde se almacenan esos metadatos. Por lo general, se recomienda especificar una ubicación en la que vayan a residir la mayoría de los recursos. Si usa la misma ubicación, puede simplificar la plantilla.
   
   ```powershell
   New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   ```
   
     Se devuelve un resumen del grupo de recursos nuevo.
4. Antes de ejecutar la implementación, puede validar la configuración de la implementación. El cmdlet **Test-AzureRmResourceGroupDeployment** le permite buscar problemas antes de crear los recursos reales. En el ejemplo siguiente se muestra cómo validar una implementación.
   
   ```powershell
   Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate>
   ```
5. Para implementar recursos en el grupo de recursos, ejecute el comando **New-AzureRmResourceGroupDeployment** y especifique los parámetros necesarios. Los parámetros incluyen un nombre para la implementación, el nombre del grupo de recursos, la ruta de acceso o dirección URL de la plantilla y cualquier otro parámetro necesario para el escenario. Si no se especifica el parámetro **Modo**, se usa el valor predeterminado **Incremental**. Para ejecutar una implementación completa, establezca el **modo** en **Completo**. Tenga cuidado al usar este modo, ya que puede eliminar accidentalmente los recursos que no estén en la plantilla.
   
     Para implementar una plantilla local, use el parámetro **TemplateFile**:
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json
   ```
   
     Para implementar una plantilla externa, use el parámetro **TemplateUri**:
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.json
   ```
   
     Los dos ejemplos anteriores no incluyen valores de parámetro. Obtendrá información sobre las opciones para pasar valores de parámetro en la sección [Parámetros](#parameters). Por ahora, se le pide que proporcione los valores de parámetro con la sintaxis siguiente:

   ```poweshell  
   cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
   Supply values for the following parameters:
   (Type !? for Help.)
   firstParameter: <type here>
   ```
     
     Una vez implementados los recursos, verá un resumen de la implementación. El resumen incluye un valor **ProvisioningState**, que indica si la implementación se realizó correctamente.

   ```poweshell   
   DeploymentName    : ExampleDeployment
   ResourceGroupName : ExampleResourceGroup
   ProvisioningState : Succeeded
   Timestamp         : 4/14/2015 7:00:27 PM
   Mode              : Incremental
   ```
      
6. Si desea registrar más información sobre la implementación que pueda ayudarle a solucionar los errores de implementación, use el parámetro **DeploymentDebugLogLevel**. Puede especificar que se registren el contenido de la solicitud y el de la respuesta, o ambos, con la operación de implementación.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
   ```
   
     Para más información sobre cómo usar este contenido de depuración a fin de solucionar problemas en implementaciones, consulte [Solución de problemas de implementaciones de grupo de recursos con Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md).

## <a name="deploy-private-template-with-sas-token"></a>Implementación de una plantilla privada con el token de SAS
Puede agregar las plantillas a una cuenta de almacenamiento y establecer vínculos a ellas durante la implementación con un token de SAS.

> [!IMPORTANT]
> Siguiendo estos pasos, el blob que contiene la plantilla solo es accesible para el propietario de la cuenta. Sin embargo, cuando se crea un token de SAS para el blob, el blob es accesible para cualquier persona con ese URI. Si otro usuario intercepta el URI, ese usuario podrá tener acceso a la plantilla. El uso de un token de SAS supone un buen método para limitar el acceso a las plantillas, pero no debe incluir información confidencial como contraseñas directamente en estas.
> 
> 

### <a name="add-private-template-to-storage-account"></a>Adición de plantilla privada a la cuenta de almacenamiento
Con los pasos siguientes se configura una cuenta de almacenamiento para plantillas:

1. Cree un grupo de recursos.
   
   ```powershell
   New-AzureRmResourceGroup -Name ManageGroup -Location "West US"
   ```
2. Cree una cuenta de almacenamiento. El nombre de la cuenta de almacenamiento debe ser único en Azure, así que indique su propio nombre para la cuenta.
   
   ```powershell
   New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates -Type Standard_LRS -Location "West US"
   ```
3. Configure la cuenta de almacenamiento actual.
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
4. Cree un contenedor. El permiso está establecido en **Desactivado** , lo que significa que el contenedor solo es accesible para el propietario.
   
   ```powershell
   New-AzureStorageContainer -Name templates -Permission Off
   ```
5. Agregue la plantilla al contenedor.
   
   ```powershell
   Set-AzureStorageBlobContent -Container templates -File c:\Azure\Templates\azuredeploy.json
   ```

### <a name="provide-sas-token-during-deployment"></a>Provisión del token de SAS durante la implementación
Para implementar una plantilla privada en una cuenta de almacenamiento, recupere un token de SAS e inclúyalo en el URI de la plantilla.

1. Si ha cambiado la cuenta de almacenamiento actual, establezca como cuenta de almacenamiento actual la que contiene las plantillas.
   
   ```powershell
   Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
   ```
2. Cree un token de SAS con permisos de lectura y un tiempo de expiración para limitar el acceso. Recupere el URI completo de la plantilla que incluye el token de SAS.
   
   ```powershell
   $templateuri = New-AzureStorageBlobSASToken -Container templates -Blob azuredeploy.json -Permission r -ExpiryTime (Get-Date).AddHours(2.0) -FullUri
   ```
3. Implemente la plantilla proporcionando el URI que incluye el token de SAS.
   
   ```powershell
   New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateuri
   ```

Para obtener un ejemplo del uso de un token de SAS con plantillas vinculadas, consulte [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).

## <a name="parameters"></a>Parámetros

Tiene las opciones siguientes para proporcionar valores de parámetro: 
   
- Parámetros en línea. Incluya nombres de parámetro individuales en el cmdlet (por ejemplo, **-myParameterName**).

   ```poweshell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -myParameterName "parameterValue"
   ```
- Objeto de parámetro. Incluya el parámetro **- emplateParameterObject**.

   ```powershell   
   $parameters = @{"<ParameterName>"="<Parameter Value>"}
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterObject $parameters
   ```
- Archivo de parámetro local. Incluya el parámetro **-TemplateParameterFile**.

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathToTemplate> -TemplateParameterFile c:\MyTemplates\example.params.json
   ```
- Archivo de parámetro externo. Incluya el parámetro **-TemplateParameterUri**.

   ```powershell   
   New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri <LinkToTemplate> -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.params.json
   ```
      
[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)] 

Si la plantilla incluye un parámetro con el mismo nombre que uno de los parámetros en el comando de PowerShell, se le pedirá que proporcione un valor para ese parámetro. Azure PowerShell presenta el parámetro de la plantilla incluirá postfijo **FromTemplate**. Por ejemplo, un parámetro denominado **ResourceGroupName** de su plantilla entra en conflicto con el parámetro **ResourceGroupName** del cmdlet [AzureRmResourceGroupDeployment nuevo](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment). Se le pedirá que proporcione un valor para **ResourceGroupNameFromTemplate**. Por lo general, debe evitar esta confusión no nombrando los parámetros con el mismo nombre que los parámetros utilizados para operaciones de implementación.

## <a name="parameter-precedence"></a>Prioridad de parámetros

Puede usar parámetros en línea y un archivo de parámetros local en la misma operación de implementación. Por ejemplo, puede especificar algunos valores en el archivo de parámetros local y agregar otros valores en línea durante la implementación. Si proporciona valores para un parámetro en el archivo de parámetros local y en línea, el valor en línea tiene prioridad.

Sin embargo, cuando se utiliza un archivo de parámetros externo, no se pueden pasar otros valores ya sea en línea o desde un archivo local. Cuando se especifica un archivo de parámetros en el parámetro **TemplateParameterUri**, se omiten todos los parámetros en línea. Proporcione todos los valores de parámetro en el archivo externo. Si la plantilla incluye un valor confidencial que no puede incluir en el archivo de parámetros, agregue ese valor a un almacén de claves o proporcione dinámicamente todos los valores de parámetro en línea.

## <a name="next-steps"></a>Pasos siguientes
* Para ver un ejemplo de cómo implementar los recursos mediante la biblioteca cliente de .NET, consulte [Deploy Azure resources using .NET libraries and a template](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Implementación de recursos de Azure mediante bibliotecas de .NET y una plantilla).
* Para definir parámetros de plantilla, consulte [Creación de plantillas](resource-group-authoring-templates.md#parameters).
* Para obtener instrucciones sobre cómo implementar la solución en diferentes entornos, vea [Entornos de desarrollo y pruebas en Microsoft Azure](solution-dev-test-environments.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).
* Para obtener una serie de cuatro partes acerca de cómo automatizar la implementación, vea [Automatización de implementaciones de aplicaciones en Azure Virtual Machines](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Esta serie cubre la arquitectura de la aplicación, el acceso y la seguridad, la disponibilidad y la escala, y la implementación de aplicaciones.




<!--HONumber=Nov16_HO3-->


