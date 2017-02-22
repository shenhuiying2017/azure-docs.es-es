---
title: "Implementación de recursos con la plantilla y la CLI de Azure | Microsoft Docs"
description: Use Azure Resource Manager y la CLI de Azure para implementar recursos en Azure. Los recursos se definen en una plantilla de Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e4851e872349fa6483e1f1a340d0968e845a3518
ms.openlocfilehash: ccbb918a3377094395a27a9b7a63f213c5085027


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Implementación de recursos con plantillas de Resource Manager y la CLI de Azure
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [CLI de Azure](resource-group-template-deploy-cli.md)
> * [Portal](resource-group-template-deploy-portal.md)
> * [API DE REST](resource-group-template-deploy-rest.md)
> 
> 

En este tema se explica cómo usar la CLI de Azure con plantillas de Resource Manager para implementar sus recursos en Azure.  La plantilla puede ser un archivo local o en un archivo externo que está disponible a través de un identificador URI. Cuando la plantilla se encuentra en una cuenta de almacenamiento, puede restringir el acceso a la plantilla y proporcionar un token de firma de acceso compartido (SAS) durante la implementación.

> [!TIP]
> Para obtener ayuda con la depuración de un error durante la implementación, consulte:
> 
> * [Visualización de operaciones de implementación](resource-manager-deployment-operations.md) para obtener información que lo ayude a solucionar el error
> * [Solución de problemas comunes al implementar recursos en Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md) para obtener información sobre cómo resolver errores comunes de implementación
> 
> 

## <a name="quick-steps-to-deployment"></a>Pasos rápidos para la implementación
Para empezar a trabajar rápidamente con la implementación, use los siguientes comandos:

```
azure group create -n examplegroup -l "West US"
azure group deployment create -f "c:\MyTemplates\example.json" -e "c:\MyTemplates\example.params.json" -g examplegroup -n exampledeployment
```

Estos comandos crean un grupo de recursos e implementan una plantilla en dicho grupo de recursos. El archivo de plantilla y parámetros son locales. Si funciona, tiene todo lo que necesita para implementar recursos. Sin embargo, hay más opciones disponibles para especificar los recursos que se van a implementar. En el resto de este artículo se describen todas las diferentes opciones disponibles durante la implementación. 

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy"></a>Implementación
Si todavía no ha usado la CLI de Azure con Administrador de recursos, consulte [Uso de la interfaz de la línea de comandos de Azure con Administrador de recursos](xplat-cli-azure-resource-manager.md).

1. Inicio de sesión en la cuenta de Azure Después de proporcionar las credenciales, el comando devuelve el resultado del inicio de sesión.
   
   ```
   azure login
   ```
2. Si tiene varias suscripciones, proporcione el identificador de suscripción que desea usar para la implementación.
   
   ```
   azure account set <YourSubscriptionNameOrId>
   ```
4. Al implementar una plantilla, debe especificar un grupo de recursos que contendrá los recursos implementados. Si ya tiene un grupo de recursos en el que desea realizar la implementación, puede omitir este paso y usar ese grupo. 
   
     Para crear un grupo de recursos, proporcione un nombre y una ubicación para el grupo de recursos. Debe proporcionar una ubicación para el grupo de recursos porque este almacena metadatos sobre los recursos. Por motivos de cumplimiento, debería especificar dónde se almacenan esos metadatos. Por lo general, se recomienda especificar una ubicación en la que vayan a residir la mayoría de los recursos. Si usa la misma ubicación, puede simplificar la plantilla.
   
   ```
   azure group create -n ExampleResourceGroup -l "West US"
   ```

     Se devuelve un resumen del grupo de recursos nuevo.
   
5. Valide la implementación antes de ejecutarla. Para ello, ejecute el comando **azure group template validate**. Al probar la implementación, proporcione los parámetros exactamente como lo haría al ejecutar la implementación (como se muestra en el paso siguiente).
   
   ```
   azure group template validate -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup
   ```
6. Para implementar recursos en el grupo de recursos, ejecute el comando **azure group deployment create** y especifique los parámetros necesarios. Los parámetros incluyen un nombre para la implementación, el nombre del grupo de recursos, la ruta de acceso o dirección URL de la plantilla y cualquier otro parámetro necesario para el escenario. Si no se especifica el parámetro **mode**, se usa el valor predeterminado **Incremental**. Para ejecutar una implementación completa, establezca el **modo** en **Completo**. Tenga cuidado al usar este modo, ya que puede eliminar accidentalmente los recursos que no estén en la plantilla.
   
     Para implementar una plantilla local, use el parámetro **template-file**:
   
   ```
   azure group deployment create --resource-group examplegroup --template-file "c:\MyTemplates\example.json"
   ```
   
     Para implementar una plantilla externa, use el parámetro **template-uri**:
   
   ```
   azure group deployment create --resource-group examplegroup --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.json"
   ```
   
     Los dos ejemplos anteriores no incluyen valores de parámetro. Obtendrá información sobre las opciones para pasar valores de parámetro en la sección [Parámetros](#parameters). Por ahora, se le pide que proporcione los valores de parámetro con la sintaxis siguiente:

   ```
   info:    Executing command group deployment create
   info:    Supply values for the following parameters
   firstParameters:  <type here>
   ```

      Una vez implementados los recursos, verá un resumen de la implementación. El resumen incluye un valor **ProvisioningState**, que indica si la implementación se realizó correctamente.

   ```
   + Initializing template configurations and parameters
   + Creating a deployment
   info:    Created template deployment "example"
   + Waiting for deployment to complete
   +
   +
   data:    DeploymentName     : example
   data:    ResourceGroupName  : examplegroup
   data:    ProvisioningState  : Succeeded
   ```
7. Si desea registrar más información sobre la implementación que pueda ayudarle a solucionar los errores de implementación, use el parámetro **debug-setting** . Puede especificar que se registren el contenido de la solicitud y el de la respuesta, o ambos, con la operación de implementación.
   
   ```
   azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g examplegroup -n exampleDeployment
   ```

## <a name="deploy-template-from-storage-with-sas-token"></a>Implementación de plantilla desde el almacenamiento con token de SAS
Puede agregar las plantillas a una cuenta de almacenamiento y establecer vínculos a ellas durante la implementación con un token de SAS.

> [!IMPORTANT]
> Siguiendo estos pasos, el blob que contiene la plantilla solo es accesible para el propietario de la cuenta. Sin embargo, cuando se crea un token de SAS para el blob, el blob es accesible para cualquier persona con ese URI. Si otro usuario intercepta el URI, ese usuario podrá tener acceso a la plantilla. El uso de un token de SAS supone un buen método para limitar el acceso a las plantillas, pero no debe incluir información confidencial como contraseñas directamente en estas.
> 
> 

### <a name="add-private-template-to-storage-account"></a>Adición de plantilla privada a la cuenta de almacenamiento
Con los pasos siguientes se configura una cuenta de almacenamiento para plantillas:

1. Cree un grupo de recursos.
   
   ```
   azure group create -n "ManageGroup" -l "westus"
   ```
2. Cree una cuenta de almacenamiento. El nombre de la cuenta de almacenamiento debe ser único en Azure, así que indique su propio nombre para la cuenta.
   
   ```
   azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates
   ```
3. Establezca las variables para la cuenta de almacenamiento y la clave.
   
   ```
   export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
   export AZURE_STORAGE_ACCESS_KEY={storage_account_key}
   ```
4. Cree un contenedor. El permiso está establecido en **Desactivado** , lo que significa que el contenedor solo es accesible para el propietario.
   
   ```
   azure storage container create --container templates -p Off 
   ```
5. Agregue la plantilla al contenedor.
   
   ```
   azure storage blob upload --container templates -f c:\MyTemplates\azuredeploy.json
   ```

### <a name="provide-sas-token-during-deployment"></a>Provisión del token de SAS durante la implementación
Para implementar una plantilla privada en una cuenta de almacenamiento, recupere un token de SAS e inclúyalo en el URI de la plantilla.

1. Cree un token de SAS con permisos de lectura y un tiempo de expiración para limitar el acceso. Establezca el tiempo de expiración con un margen suficiente para completar la implementación. Recupere el URI completo de la plantilla que incluye el token de SAS.
   
   ```
   expiretime=$(date -I'minutes' --date "+30 minutes")
   fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")
   ```
2. Implemente la plantilla proporcionando el URI que incluye el token de SAS.
   
   ```
   azure group deployment create --template-uri $fullurl -g ExampleResourceGroup
   ```

Para obtener un ejemplo del uso de un token de SAS con plantillas vinculadas, consulte [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).

## <a name="parameters"></a>Parámetros

Tiene las opciones siguientes para proporcionar valores de parámetro: 
   
- Use parámetros en línea. Cada parámetro tiene el formato: `"ParameterName": { "value": "ParameterValue" }`. En el ejemplo siguiente se muestran los parámetros con caracteres de escape.

   ```   
   azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment
   ```
- Use un archivo de parámetro.

  ```    
  azure group deployment create -f "c:\MyTemplates\example.json" -e "c:\MyTemplates\example.params.json" -g ExampleResourceGroup -n ExampleDeployment
  ```
      

[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Pasos siguientes
* Para ver un ejemplo de cómo implementar los recursos mediante la biblioteca cliente de .NET, consulte [Deploy Azure resources using .NET libraries and a template](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Implementación de recursos de Azure mediante bibliotecas de .NET y una plantilla).
* Para definir parámetros de plantilla, consulte [Creación de plantillas](resource-group-authoring-templates.md#parameters).
* Para obtener instrucciones sobre cómo implementar la solución en diferentes entornos, vea [Entornos de desarrollo y pruebas en Microsoft Azure](solution-dev-test-environments.md).
* Para más información sobre el uso de una referencia KeyVault para pasar valores seguros, consulte [Paso de valores seguros durante la implementación](resource-manager-keyvault-parameter.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).
* Para obtener una serie de cuatro partes acerca de cómo automatizar la implementación, vea [Automatización de implementaciones de aplicaciones en Azure Virtual Machines](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Esta serie cubre la arquitectura de la aplicación, el acceso y la seguridad, la disponibilidad y la escala, y la implementación de aplicaciones.




<!--HONumber=Jan17_HO2-->


