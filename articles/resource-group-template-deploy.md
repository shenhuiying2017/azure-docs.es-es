<properties
   pageTitle="Implementación de recursos con la plantilla del Administrador de recursos | Microsoft Azure"
   services="azure-resource-manager"
   description="Uso de Administrador de recursos de Azure para implementar recursos en Azure Una plantilla es un archivo JSON y puede usarse desde el Portal, PowerShell, la interfaz de la línea de comandos de Azure para Mac, Linux y Windows o REST."
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
   ms.date="04/11/2016"
   ms.author="tomfitz"/>

# Implementación de recursos con plantillas de Azure Resource Manager

En este tema se explica cómo usar las plantillas de Administrador de recursos de Azure para implementar sus recursos en Azure. Muestra cómo implementar los recursos mediante el uso de Azure PowerShell, CLI de Azure, API de REST o el Portal de Azure.

Al implementar la definición de una aplicación con una plantilla, puede proporcionar valores de parámetros para personalizar cómo se crean los recursos. Especifique los valores para estos parámetros, ya sea en línea o en un archivo de parámetros.

## Implementaciones de incrementales y completadas

De forma predeterminada, el Administrador de recursos controla las implementaciones como las actualizaciones incrementales al grupo de recursos. Con la implementación incremental, el Administrador de recursos:

- **deja sin modificar** recursos que existen en el grupo de recursos, pero que no se especifican en la plantilla
- **agrega** recursos que se especifican en la plantilla, pero que no existen en el grupo de recursos 
- **no vuelve a aprovisionar** recursos que existen en el grupo de recursos en la misma condición definida en la plantilla

Con la implementación completa, el Administrador de recursos:

- **elimina** recursos que existen en el grupo de recursos, pero que no se especifican en la plantilla
- **agrega** recursos que se especifican en la plantilla, pero que no existen en el grupo de recursos 
- **no vuelve a aprovisionar** recursos que existen en el grupo de recursos en la misma condición definida en la plantilla
 
El tipo de implementación se especifica a través de la propiedad **Mode**, como se muestra en los ejemplos siguientes.

## Implementación con PowerShell

1. Inicie sesión en su cuenta de Azure. Después de proporcionar sus credenciales, el comando devuelve información acerca de su cuenta.

        Add-AzureRmAccount

     Se devuelve un resumen de la cuenta.

        Environment : AzureCloud
        Account    : someone@example.com
        ...


2. Si tiene varias suscripciones, proporcione el identificador de suscripción que quiera usar para la implementación con el comando **Set-AzureRmContext**.

        Set-AzureRmContext -SubscriptionID <YourSubscriptionId>

3. Si no tiene un grupo de recursos existente, cree uno nuevo con el comando **New-AzureRmResourceGroup**. Proporcione el nombre del grupo de recursos y la ubicación que necesita para la solución.

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

4. Valide la implementación antes de ejecutarla. Para ello, ejecute el cmdlet **Test-AzureRmResourceGroupDeployment**. Al probar la implementación, proporcione los parámetros exactamente como lo haría al ejecutar la implementación (como se muestra en el paso siguiente).

        Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

5. Para crear otra implementación del grupo de recursos, ejecute el comando **New-AzureRmResourceGroupDeployment** y especifique los parámetros necesarios. Los parámetros incluirán un nombre para la implementación, el nombre del grupo de recursos, la ruta de acceso o dirección URL a la plantilla que creó y cualquier otro parámetro necesario para el escenario. Si no se especifica el parámetro **Mode**, se usa el valor predeterminado de **Incremental**.
   
     Tiene las siguientes tres opciones para proporcionar valores de parámetro:
   
     1. Use parámetros en línea.

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     2. Use un objeto de parámetro.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     3. Uso de un archivo de parámetro. Para obtener información sobre el archivo de plantilla, consulte [Archivo de parámetros](./#parameter-file).

            New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     Una vez implementados los recursos a través de uno de los 3 métodos anteriores, verá un resumen de la implementación.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Para ejecutar una implementación completa, establezca el **Modo** en **Completo**.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -Mode Complete -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> 
        
     Se le pide que confirme que quiere usar el modo Completo, lo que puede implicar la eliminación de recursos.
        
        Confirm
        Are you sure you want to use the complete deployment mode? Resources in the resource group 'ExampleResourceGroup' which are not
        included in the template will be deleted.
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

     Si la plantilla incluye un parámetro con un nombre que coincide con el de uno de los parámetros del comando utilizado para implementar la plantilla (por ejemplo, incluye un parámetro denominado **ResourceGroupName** en la plantilla y este parámetro es el mismo que el parámetro **ResourceGroupName** del cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx)), se le pedirá que proporcione un valor para un parámetro con el sufijo **FromTemplate** (como **ResourceGroupNameFromTemplate**). Por lo general, debe evitar esta confusión no nombrando los parámetros con el mismo nombre que los parámetros utilizados para operaciones de implementación.

6. Si desea registrar más información sobre la implementación que pueda ayudarle a solucionar los errores de implementación, use el parámetro **DeploymentDebugLogLevel**. Puede especificar que se registren el contenido de la solicitud y el de la respuesta, o ambos, con la operación de implementación.

        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate>
        
     Para obtener más información sobre cómo usar este contenido de depuración para solucionar problemas en implementaciones, consulte [Solución de problemas de implementaciones de grupo de recursos con Azure PowerShell](resource-manager-troubleshoot-deployments-powershell.md).
       
        
### Vídeo

En este vídeo se muestra cómo trabajar con plantillas del Administrador de recursos con PowerShell.

[AZURE.VIDEO deploy-an-application-with-azure-resource-manager-template]


## Implementación con la CLI de Azure

Si todavía no ha usado la CLI de Azure con Administrador de recursos, consulte [Uso de la interfaz de la línea de comandos de Azure con Administrador de recursos](xplat-cli-azure-resource-manager.md).

1. Inicie sesión en su cuenta de Azure. Después de proporcionar las credenciales, el comando devuelve el resultado del inicio de sesión.

        azure login
  
        ...
        info:    login command OK

2. Si tiene varias suscripciones, proporcione el identificador de suscripción que desea usar para la implementación.

        azure account set <YourSubscriptionNameOrId>

3. Cambie al módulo de Administrador de recursos de Azure. Recibirá una confirmación del modo nuevo.

        azure config mode arm
   
        info:     New mode is arm

4. Si no tiene un grupo de recursos existente, cree uno nuevo. Proporcione el nombre del grupo de recursos y la ubicación que necesita para la solución. Se devuelve un resumen del grupo de recursos nuevo.

        azure group create -n ExampleResourceGroup -l "West US"
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Valide la implementación antes de ejecutarla. Para ello, ejecute el comando **azure group template validate**. Al probar la implementación, proporcione los parámetros exactamente como lo haría al ejecutar la implementación (como se muestra en el paso siguiente).

        azure group template validate -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup

5. Para crear una implementación nueva para el grupo de recursos, ejecute el siguiente comando y proporcione los parámetros necesarios. Los parámetros incluirán un nombre para la implementación, el nombre del grupo de recursos, la ruta de acceso o dirección URL a la plantilla que creó y cualquier otro parámetro necesario para el escenario.
   
     Tiene las siguientes tres opciones para proporcionar valores de parámetro:

     1. Use parámetros en línea y una plantilla local. Cada parámetro tiene el formato: `"ParameterName": { "value": "ParameterValue" }`. En el ejemplo siguiente se muestran los parámetros con caracteres de escape.

            azure group deployment create -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     2. Use parámetros en línea y un vínculo a una plantilla.

            azure group deployment create --template-uri <LinkToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     3. Use un archivo de parámetro. Para obtener información sobre el archivo de plantilla, consulte [Archivo de parámetros](./#parameter-file).
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Una vez implementados los recursos a través de uno de los 3 métodos anteriores, verá un resumen de la implementación.
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     Para ejecutar una implementación completa, establezca el **modo** en **Completo**.

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. Si desea registrar más información sobre la implementación que pueda ayudarle a solucionar los errores de implementación, use el parámetro **debug-setting**. Puede especificar que se registren el contenido de la solicitud y el de la respuesta, o ambos, con la operación de implementación.

        azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

## Implementación con la API de REST
1. Establezca los [encabezados y parámetros comunes](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), incluidos los tokens de autenticación.
2. Si no tiene un grupo de recursos existente, cree uno nuevo. Proporcione el identificador de la suscripción, el nombre del grupo de recursos y la ubicación que necesita para la solución. Para obtener más información, consulte [Crear un grupo de recursos](https://msdn.microsoft.com/library/azure/dn790525.aspx).

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. Valide la implementación antes de ejecutarla. Para ello, ejecute la operación de [validación de una implementación de plantilla](https://msdn.microsoft.com/library/azure/dn790547.aspx). Al probar la implementación, proporcione los parámetros exactamente como lo haría al ejecutar la implementación (como se muestra en el paso siguiente).

3. Cree una nueva implementación del grupo de recursos Proporcione el identificador de suscripción, el nombre del grupo de recursos para implementar, el nombre de la implementación y la ubicación de la plantilla. Para obtener información sobre el archivo de plantilla, consulte [Archivo de parámetros](./#parameter-file). Para obtener más información acerca de la API de REST para crear un grupo de recursos, consulte [Creación de una implementación de plantilla](https://msdn.microsoft.com/library/azure/dn790564.aspx). Observe que el **modo** se establece en **Incremental**. Para ejecutar una implementación completa, establezca el **modo** en **Completo**.
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",
              }
            }
          }
   
      Si desea registrar el contenido de la respuesta y el de la solicitud, o ambos, incluya **debugSetting** en la solicitud.

        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }


4. Obtenga el estado de la implementación de la plantilla. Para obtener más información, consulte [Obtener información acerca de una implementación de plantilla](https://msdn.microsoft.com/library/azure/dn790565.aspx).

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## Implementación con Visual Studio

Con Visual Studio, puede crear un proyecto del grupo de recursos e implementarlo en Azure a través de la interfaz de usuario. Seleccione el tipo de recursos que incluirá en su proyecto y esos recursos se agregarán automáticamente a la plantilla del Administrador de recursos. El proyecto también ofrece un script de PowerShell para implementar la plantilla.

Para ver una introducción sobre el uso de Visual Studio con grupos de recursos, consulte [Creación e implementación de grupos de recursos de Azure mediante Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Implementación con el portal

¿Sabe qué? Las aplicaciones que cree mediante el [portal](https://portal.azure.com/) están respaldadas por una plantilla del Administrador de recursos de Azure. Con solo crear una Máquina virtual, Red virtual, cuenta de almacenamiento, Servicio de aplicaciones o base de datos a través del portal, ya está obteniendo los beneficios del Administrador de recursos de Azure sin esfuerzo adicional. Simplemente, seleccione el icono **Nuevo** y estará en vías de implementar una aplicación mediante el Administrador de recursos de Azure.

![Nuevo](./media/resource-group-template-deploy/new.png)

Todas las implementaciones realizadas a través del portal se validan automáticamente antes de la ejecución. Para más información sobre el uso del portal con el Administrador de recursos de Azure, vea [Uso del Portal de Azure para administrar los recursos de Azure](azure-portal/resource-group-portal.md).


## Archivo de parámetros

Si utiliza un archivo de parámetros para pasar los valores de parámetro a la plantilla durante la implementación, tendrá que crear un archivo JSON con un formato similar del ejemplo siguiente.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webSiteName": {
                "value": "ExampleSite"
            },
            "webSiteHostingPlanName": {
                "value": "DefaultPlan"
            },
            "webSiteLocation": {
                "value": "West US"
            },
            "adminPassword": {
                "reference": {
                   "keyVault": {
                      "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
                   }, 
                   "secretName": "sqlAdminPassword" 
                }   
            }
       }
    }

El tamaño del archivo de parámetros no puede ser superior a 64 KB.

Para aprender a definir parámetros en una plantilla, consulte [Creación de plantillas](../resource-group-authoring-templates/#parameters). Para obtener más información sobre la referencia de KeyVault para pasar valores seguros, consulte [Paso de valores seguros durante la implementación](resource-manager-keyvault-parameter.md).

## Pasos siguientes
- Para un ejemplo de cómo implementar los recursos a través de la biblioteca cliente .NET, vea [Implementación de recursos mediante bibliotecas de .NET y una plantilla](virtual-machines/virtual-machines-windows-csharp-template.md).
- Para un ejemplo en profundidad de la implementación de una aplicación, vea [Aprovisionamiento e implementación predecibles de microservicios en Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Para ver instrucciones sobre cómo implementar la solución en diferentes entornos, consulte [Entornos de desarrollo y pruebas en Microsoft Azure](solution-dev-test-environments.md).
- Para información sobre las secciones de la plantilla del Administrador de recursos de Azure, vea [Creación de plantillas](resource-group-authoring-templates.md).
- Para obtener una lista de las funciones que puede usar en una plantilla del Administrador de recursos de Azure, vea [Funciones de plantillas](resource-group-template-functions.md).

 

<!---HONumber=AcomDC_0420_2016-->