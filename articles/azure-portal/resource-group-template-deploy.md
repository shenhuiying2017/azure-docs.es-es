<properties
   pageTitle="Implementación de una aplicación con la plantilla del Administrador de recursos de Azure"
	services="azure-resource-manager"
	description="Uso de Administrador de recursos de Azure para implementar una aplicación en Azure Una plantilla es un archivo JSON y puede usarse desde el Portal, PowerShell, la interfaz de la línea de comandos de Azure para Mac, Linux y Windows o REST."
	documentationCenter="na"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
   ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="08/20/2015"
	ms.author="tomfitz"/>

# Implementación de una aplicación con la plantilla del Administrador de recursos de Azure

En este tema se explica cómo utilizar las plantillas de Administrador de recursos de Azure para implementar su aplicación en Azure. Se muestra cómo implementar la aplicación mediante el uso de PowerShell de Azure, CLI de Azure, API de REST o el Portal de vista previa de Microsoft Azure.

Las plantillas de Administrador de recursos de Azure permiten aprovisionar de manera rápida y fácil las aplicaciones de Azure a través de JSON declarativo. En una sola plantilla JSON, puede implementar varios servicios, como Máquinas virtuales, Redes virtuales, Almacenamiento, Servicios de aplicaciones y bases de datos. Use la misma plantilla para implementar su aplicación de forma repetida y consistente durante cada fase de su ciclo de vida.

Para simplificar la administración de la aplicación, puede organizar todos los recursos que comparten un ciclo de vida comunes en un único grupo de recursos. Los grupos de recursos facilitan la implementación, actualización y eliminación de todos los recursos relacionados de forma conjunta. En la mayoría de los casos, un grupo de recursos se asigna a una única aplicación o a una capa de aplicación (para aplicaciones mayores). El recurso que se implementa a través de una plantilla va a residir dentro de los grupos de un único recurso, pero puede incluir dependencias en otros grupos de recursos.

Dentro de un grupo de recursos, puede realizar un seguimiento de la ejecución de una implementación y ver el estado de la implementación y cualquier resultado de la ejecución de la plantilla.

Al implementar una aplicación con una plantilla, puede proporcionar valores de parámetros para personalizar cómo se crean los recursos. Especifique los valores para estos parámetros, ya sea en línea o en un archivo de parámetros.

## Conceptos

- Grupo de recursos: conjunto de entidades que comparten un ciclo de vida común
- Plantilla de Administrador de recursos: archivo JSON declarativo que define el estado objetivo de una implementación
- Implementación: operación que realiza el seguimiento de la ejecución de una plantilla de Administrador de recursos
- Parámetros: valores proporcionados por el usuario que ejecuta la implementación para personalizar recursos implementados
- Archivo de parámetros: archivo JSON que almacena los valores y nombres de parámetros 

## Escenarios

Con las plantillas de Administrador de recursos, puede:

- Implementar complejas aplicaciones de varios niveles, como Microsoft SharePoint.
- Implementar de manera repetida y consistente las aplicaciones.
- Dar soporte a los entornos de desarrollo, prueba y producción.
- Vea el estado de las implementaciones.
- Solucione los problemas de implementación mediante los registros de auditoría de implementación.

## Implementación con el Portal de vista previa

¿Sabe qué? Las aplicaciones que cree mediante el [portal de vista previa](https://portal.azure.com/) están respaldadas por una plantilla del Administrador de recursos de Azure Con solo crear una Máquina virtual, Red virtual, cuenta de almacenamiento, Servicio de aplicaciones o base de datos a través del portal, ya está obteniendo los beneficios del Administrador de recursos de Azure sin esfuerzo adicional. Simplemente, seleccione el icono **Nuevo** y estará en camino a implementar una aplicación mediante el Administrador de recursos de Azure.

![Nuevo](./media/resource-group-template-deploy/new.png)

Para obtener más información sobre el uso del portal con el Administrador de recursos de Azure, consulte [Uso del Portal de vista previa de Azure para administrar los recursos de Azure](resource-group-portal.md).


## Implementación con PowerShell

Si todavía no ha utilizado Azure PowerShell con el Administrador de recursos, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md)

1. Inicie sesión en su cuenta de Azure. Después de proporcionar sus credenciales, el comando devuelve información acerca de su cuenta.

        PS C:\> Add-AzureAccount

        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

2. Si tiene varias suscripciones, proporcione el identificador de suscripción que desea usar para la implementación.

        PS C:\> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Cambie al módulo Administrador de recursos de Azure.

        PS C:\> Switch-AzureMode AzureResourceManager

4. Si no tiene un grupo de recursos existente, cree uno nuevo. Proporcione el nombre del grupo de recursos y la ubicación que necesita para la solución. Se devuelve un resumen del grupo de recursos nuevo.

        PS C:\> New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Para crear una implementación nueva para el grupo de recursos, ejecute el comando **New-AzureResourceGroupDeployment** y proporcione los parámetros necesarios. Los parámetros incluirán un nombre para la implementación, el nombre del grupo de recursos, la ruta de acceso o dirección URL a la plantilla que creó y cualquier otro parámetro necesario para el escenario.
   
     Tiene las opciones siguientes para proporcionar valores de parámetro:
   
     - Use parámetros en línea.

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - Use un objeto de parámetro.

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - Uso de un archivo de parámetro. Para obtener información sobre el archivo de plantilla, consulte [Archivo de parámetros](./#parameter-file).

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     Una vez implementado el grupo de recursos, verá un resumen de la implementación.

          DeploymentName    : ExampleDeployment
          ResourceGroupName : ExampleResourceGroup
          ProvisioningState : Succeeded
          Timestamp         : 4/14/2015 7:00:27 PM
          Mode              : Incremental
          ...

6. Para obtener información acerca de los errores de la implementación.

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. Para obtener información detallada acerca de los errores de la implementación.

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput
        
### Vídeo

En este vídeo se muestra cómo trabajar con plantillas del Administrador de recursos con PowerShell.

[AZURE.VIDEO deploy-an-application-with-azure-resource-manager-template]


## Implementación con CLI de Azure para Mac, Linux y Windows

Si todavía no ha usado la CLI de Azure con Administrador de recursos, consulte [Uso de la interfaz de la línea de comandos de Azure con Administrador de recursos](../xplat-cli-azure-resource-manager.md).

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

5. Para crear una implementación nueva para el grupo de recursos, ejecute el siguiente comando y proporcione los parámetros necesarios. Los parámetros incluirán un nombre para la implementación, el nombre del grupo de recursos, la ruta de acceso o dirección URL a la plantilla que creó y cualquier otro parámetro necesario para el escenario.
   
     Tiene las opciones siguientes para proporcionar valores de parámetro:

     - Use parámetros en línea y una plantilla local.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - Use parámetros en línea y un vínculo a una plantilla.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - Use un archivo de parámetro. Para obtener información sobre el archivo de plantilla, consulte [Archivo de parámetros](./#parameter-file).
    
             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Una vez implementado el grupo de recursos, verá un resumen de la implementación.
  
           info:    Executing command group deployment create
           + Initializing template configurations and parameters
           + Creating a deployment
           ...
           info:    group deployment create command OK


6. Para obtener información acerca de la implementación más reciente.

         azure group log show -l ExampleResourceGroup

7. Para obtener información detallada acerca de los errores de la implementación.
      
         azure group log show -l -v ExampleResourceGroup

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
   
3. Cree una nueva implementación del grupo de recursos Proporcione el identificador de suscripción, el nombre del grupo de recursos para implementar, el nombre de la implementación y la ubicación de la plantilla. Para obtener información sobre el archivo de plantilla, consulte [Archivo de parámetros](./#parameter-file). Para obtener más información acerca de la API de REST para crear un grupo de recursos, consulte [Creación de una implementación de plantilla](https://msdn.microsoft.com/library/azure/dn790564.aspx).
    
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
   
4. Obtenga el estado de la implementación de la plantilla. Para obtener más información, consulte [Obtener información acerca de una implementación de plantilla](https://msdn.microsoft.com/library/azure/dn790565.aspx).

         GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

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
            }
       }
    }

El tamaño del archivo de parámetros no puede ser superior a 64 KB.

## Pasos siguientes
- Para obtener un ejemplo de cómo implementar los recursos a través de la biblioteca de clientes de .NET, consulte [Implementación de recursos mediante bibliotecas de .NET y una plantilla](../arm-template-deployment.md).
- Para obtener un ejemplo detallado de la implementación de una aplicación, consulte [Aprovisionamiento e implementación predecibles de microservicios en Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
- Para obtener información sobre las secciones de la plantilla del Administrador de recursos de Azure, consulte [Creación de plantillas](../resource-group-authoring-templates.md).
- Para obtener una lista de las funciones que puede usar en una plantilla del Administrador de recursos de Azure, consulte [Funciones de plantillas](../resource-group-template-functions.md).

 

<!---HONumber=August15_HO9-->