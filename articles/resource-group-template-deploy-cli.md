<properties
   pageTitle="Implementación de recursos con la CLI de Azure y plantilla | Microsoft Azure"
   description="Use Azure Resource Manager y la CLI de Azure para implementar recursos en Azure. Los recursos se definen en una plantilla de Resource Manager."
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

# Implementación de recursos con plantillas de Resource Manager y la CLI de Azure

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

En este tema se explica cómo usar la CLI de Azure con plantillas de Resource Manager para implementar sus recursos en Azure.

> [AZURE.TIP] Para obtener ayuda con la depuración de un error durante la implementación, consulte:
>
> - [Visualización de operaciones de implementación con la CLI de Azure](resource-manager-troubleshoot-deployments-cli.md) para obtener información que le ayude a solucionar el error
> - [Solución de problemas comunes al implementar recursos en Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md) para obtener información sobre cómo resolver errores comunes de implementación

La plantilla puede ser un archivo local o en un archivo externo que está disponible a través de un identificador URI. Cuando la plantilla se encuentra en una cuenta de almacenamiento, puede restringir el acceso a la plantilla y proporcionar un token de firma de acceso compartido (SAS) durante la implementación.

## Pasos rápidos para la implementación

En este artículo se describen todas las diferentes opciones disponibles durante la implementación. Sin embargo, a menudo solo necesita dos sencillos comandos. Para empezar a trabajar rápidamente con la implementación, use los siguientes comandos:

    azure group create -n ExampleResourceGroup -l "West US"
    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

Para obtener más información acerca de opciones de implementación que podrían ser más adecuadas para su escenario, siga leyendo este artículo.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## Implementación con la CLI de Azure

Si todavía no ha usado la CLI de Azure con Administrador de recursos, consulte [Uso de la interfaz de la línea de comandos de Azure con Administrador de recursos](xplat-cli-azure-resource-manager.md).

1. Inicio de sesión en la cuenta de Azure Después de proporcionar las credenciales, el comando devuelve el resultado del inicio de sesión.

        azure login
  
        ...
        info:    login command OK

2. Si tiene varias suscripciones, proporcione el identificador de suscripción que desea usar para la implementación.

        azure account set <YourSubscriptionNameOrId>

3. Cambie al módulo de Administrador de recursos de Azure. Recibirá una confirmación del modo nuevo.

        azure config mode arm
   
        info:     New mode is arm

4. Si no tiene un grupo de recursos existente, puede crear uno. Proporcione el nombre del grupo de recursos y la ubicación que necesita para la solución. Se devuelve un resumen del grupo de recursos nuevo.

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

5. Para implementar recursos en el grupo de recursos, ejecute el siguiente comando y proporcione los parámetros necesarios. Los parámetros incluyen un nombre para la implementación, el nombre del grupo de recursos, la ruta de acceso o dirección URL a la plantilla que creó y cualquier otro parámetro necesario para el escenario.
   
     Tiene las siguientes tres opciones para proporcionar valores de parámetro:

     1. Use parámetros en línea y una plantilla local. Cada parámetro tiene el formato: `"ParameterName": { "value": "ParameterValue" }`. En el ejemplo siguiente se muestran los parámetros con caracteres de escape.

            azure group deployment create -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     2. Use parámetros en línea y un vínculo a una plantilla.

            azure group deployment create --template-uri <LinkToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     3. Use un archivo de parámetro. Para obtener información sobre el archivo de plantilla, consulte [Archivo de parámetros](./#parameter-file).
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Una vez implementados los recursos mediante uno de los tres métodos anteriores, verá un resumen de la implementación.
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     Para ejecutar una implementación completa, establezca el **modo** en **Completo**. Tenga cuidado al usar este modo, ya que puede eliminar accidentalmente los recursos que no estén en la plantilla.

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. Si desea registrar más información sobre la implementación que pueda ayudarle a solucionar los errores de implementación, use el parámetro **debug-setting**. Puede especificar que se registren el contenido de la solicitud y el de la respuesta, o ambos, con la operación de implementación.

        azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

## Implementación de plantilla desde el almacenamiento con token de SAS

Puede agregar las plantillas a una cuenta de almacenamiento y establecer vínculos a ellas durante la implementación con un token de SAS.

> [AZURE.IMPORTANT] Siguiendo estos pasos, el blob que contiene la plantilla solo es accesible para el propietario de la cuenta. Sin embargo, cuando se crea un token de SAS para el blob, el blob es accesible para cualquier persona con ese URI. Si otro usuario intercepta el URI, ese usuario podrá tener acceso a la plantilla. El uso de un token de SAS supone un buen método para limitar el acceso a las plantillas, pero no debe incluir información confidencial como contraseñas directamente en estas.

### Adición de plantilla privada a la cuenta de almacenamiento

Con los pasos siguientes se configura una cuenta de almacenamiento para plantillas:

1. Cree un grupo de recursos.

        azure group create -n "ManageGroup" -l "westus"

2. Cree una cuenta de almacenamiento. El nombre de la cuenta de almacenamiento debe ser único en Azure, así que indique su propio nombre para la cuenta.

        azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates

3. Establezca las variables para la cuenta de almacenamiento y la clave.

        export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
        export AZURE_STORAGE_ACCESS_KEY={storage_account_key}

4. Cree un contenedor. El permiso está establecido en **Desactivado**, lo que significa que el contenedor solo es accesible para el propietario.

        azure storage container create --container templates -p Off 
        
4. Agregue la plantilla al contenedor.

        azure storage blob upload --container templates -f c:\Azure\Templates\azuredeploy.json
        
### Provisión del token de SAS durante la implementación

Para implementar una plantilla privada en una cuenta de almacenamiento, recupere un token de SAS e inclúyalo en el URI de la plantilla.

1. Cree un token de SAS con permisos de lectura y un tiempo de expiración para limitar el acceso. Establezca el tiempo de expiración con un margen suficiente para completar la implementación. Recupere el URI completo de la plantilla que incluye el token de SAS.

        expiretime=$(date -I'minutes' --date "+30 minutes")
        fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")

2. Implemente la plantilla proporcionando el URI que incluye el token de SAS.

        azure group deployment create --template-uri $fullurl -g ExampleResourceGroup

Para obtener un ejemplo del uso de un token de SAS con plantillas vinculadas, consulte [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## Pasos siguientes
- Para ver un ejemplo de cómo implementar los recursos mediante la biblioteca cliente de .NET, consulte [Deploy Azure resources using .NET libraries and a template](virtual-machines/virtual-machines-windows-csharp-template.md) (Implementación de recursos de Azure mediante bibliotecas de .NET y una plantilla).
- Para definir parámetros de plantilla, consulte [Creación de plantillas](resource-group-authoring-templates.md#parameters).
- Para obtener instrucciones sobre cómo implementar la solución en diferentes entornos, vea [Entornos de desarrollo y pruebas en Microsoft Azure](solution-dev-test-environments.md).
- Para más información sobre el uso de una referencia KeyVault para pasar valores seguros, consulte [Paso de valores seguros durante la implementación](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0817_2016-->