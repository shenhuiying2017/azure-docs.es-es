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
ms.date: 12/06/2017
ms.author: tomfitz
ms.openlocfilehash: 1210b2da9126c24b59e8ef59b50742a17b2e740d
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell

En este artículo se explica cómo usar Azure PowerShell con plantillas de Resource Manager para implementar sus recursos en Azure. Si no está familiarizado con los conceptos asociados a la implementación y administración de sus soluciones de Azure, consulte [Introducción a Azure Resource Manager](resource-group-overview.md).

La plantilla de Resource Manager que ha implementado puede ser un archivo local en su equipo, o un archivo externo ubicado en un repositorio como GitHub. La plantilla que se implementa en este artículo está disponible en la sección [Plantilla de ejemplo](#sample-template), o como [plantilla de la cuenta de almacenamiento en GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

Si es necesario, instale el módulo Azure PowerShell con las instrucciones de la [guía de Azure PowerShell](/powershell/azure/overview) y ejecute `Login-AzureRmAccount` para crear una conexión con Azure.

<a id="deploy-local-template" />

## <a name="deploy-a-template-from-your-local-machine"></a>Implementación de una plantilla desde la máquina local

Al implementar recursos en Azure, siga estos pasos:

1. Inicie sesión en la cuenta de Azure.
2. Cree un grupo de recursos que actúe como contenedor para los recursos implementados. El nombre del grupo de recursos solo puede incluir caracteres alfanuméricos, puntos, guiones bajos, guiones y paréntesis. Puede tener hasta 90 caracteres. No puede terminar en punto.
3. Implemente en el grupo de recursos la plantilla que define los recursos que se van a crear.

Una plantilla puede incluir parámetros que le permiten personalizar la implementación. Por ejemplo, puede proporcionar valores que están diseñados para un entorno concreto (como desarrollo, prueba y producción). La plantilla de ejemplo define un parámetro para la SKU de la cuenta de almacenamiento.

En el ejemplo siguiente se crea un grupo de recursos y se implementa una plantilla desde la máquina local:

```powershell
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

La implementación puede demorar unos minutos en completarse. Cuando termine, verá un mensaje que incluye el resultado:

```powershell
ProvisioningState       : Succeeded
```

## <a name="deploy-a-template-from-an-external-source"></a>Implementación de una plantilla desde un origen externo

En lugar de almacenar las plantillas de Resource Manager en el equipo local, quizás prefiera almacenarlas en una ubicación externa. Puede almacenar plantillas en un repositorio de control de código fuente (por ejemplo, GitHub). O bien, puede almacenarlas en una cuenta de Azure Storage para el acceso compartido en su organización.

Para implementar una plantilla externa, use el parámetro **TemplateUri**. Use el identificador URI en el ejemplo para implementar la plantilla de ejemplo de GitHub.

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS
```

En el ejemplo anterior, se requiere un identificador URI accesible públicamente para la plantilla, que funciona con la mayoría de los escenarios porque la plantilla no debe incluir datos confidenciales. Si tiene que especificar datos confidenciales (por ejemplo, una contraseña de administrador), pase ese valor como un parámetro seguro. Sin embargo, si no desea que la plantilla sea accesible públicamente, puede protegerla mediante el almacenamiento en un contenedor de almacenamiento privado. Para más información sobre la implementación de una plantilla que requiere un token de Firma de acceso compartido (SAS), consulte [Implementación de una plantilla privada con el token de SAS](resource-manager-powershell-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

En Cloud Shell, use los comandos siguientes:

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile "C:\users\ContainerAdministrator\CloudDrive\templates\azuredeploy.json" -storageAccountType Standard_GRS
```

## <a name="parameter-files"></a>Archivos de parámetros

En lugar de pasar parámetros como valores en línea en el script, quizá le resulte más fácil usar un archivo JSON que contiene los valores de parámetro. El archivo de parámetros debe estar en el siguiente formato:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Tenga en cuenta que la sección de parámetros incluye un nombre de parámetro que coincide con el parámetro definido en la plantilla (storageAccountType). El archivo de parámetros contiene un valor para el parámetro. Este valor se pasa automáticamente a la plantilla durante la implementación. Puede crear varios archivos de parámetros para diferentes escenarios de implementación y, después, pasar el archivo de parámetros adecuado. 

Copie el ejemplo anterior y guárdelo como un archivo denominado `storage.parameters.json`.

Para pasar un archivo de parámetros local, use el parámetro **TemplateParameterFile**:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Para pasar un archivo de parámetros externo, use el parámetro **TemplateParameterUri**:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

Puede usar parámetros en línea y un archivo de parámetros local en la misma operación de implementación. Por ejemplo, puede especificar algunos valores en el archivo de parámetros local y agregar otros valores en línea durante la implementación. Si proporciona valores para un parámetro en el archivo de parámetros local y en línea, el valor en línea tiene prioridad.

Sin embargo, cuando se utiliza un archivo de parámetros externo, no se pueden pasar otros valores ya sea en línea o desde un archivo local. Cuando se especifica un archivo de parámetros en el parámetro **TemplateParameterUri**, se omiten todos los parámetros en línea. Proporcione todos los valores de parámetro en el archivo externo. Si la plantilla incluye un valor confidencial que no puede incluir en el archivo de parámetros, agregue ese valor a un almacén de claves o proporcione dinámicamente todos los valores de parámetro en línea.

Si la plantilla incluye un parámetro con el mismo nombre que uno de los parámetros del comando de PowerShell, PowerShell presenta el parámetro de la plantilla con el postfijo **FromTemplate**. Por ejemplo, un parámetro denominado **ResourceGroupName** de su plantilla entra en conflicto con el parámetro **ResourceGroupName** del cmdlet [AzureRmResourceGroupDeployment nuevo](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment). Se le pedirá que proporcione un valor para **ResourceGroupNameFromTemplate**. Por lo general, debe evitar esta confusión no nombrando los parámetros con el mismo nombre que los parámetros utilizados para operaciones de implementación.

## <a name="test-a-template-deployment"></a>Prueba de una implementación de plantilla

Para probar los valores de parámetro y de plantilla sin implementar realmente ningún recurso, use [Test-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment). 

```powershell
Test-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Si no se detectan errores, el comando finaliza sin una respuesta. Si se detecta un error, el comando devuelve un mensaje de error. Por ejemplo, si se intenta pasar un valor incorrecto a la SKU de la cuenta de almacenamiento, se devuelve el error siguiente:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Si la plantilla tiene un error de sintaxis, el comando devuelve un error que indica que no se pudo analizar la plantilla. El mensaje indica el número de línea y la posición del error de análisis.

```powershell
Test-AzureRmResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Para usar el modo completo, emplee el parámetro `Mode`:

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```

## <a name="sample-template"></a>Plantilla de ejemplo

La plantilla siguiente se usa para los ejemplos de este artículo. Cópiela y guárdela como un archivo denominado storage.json. Para comprender cómo crear esta plantilla, consulte [Creación de la primera plantilla de Azure Resource Manager](resource-manager-create-first-template.md).  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>pasos siguientes
* Los ejemplos de este artículo implementan recursos en un grupo de recursos de su suscripción predeterminada. Para usar una suscripción diferente, consulte [Administración de varias suscripciones de Azure](/powershell/azure/manage-subscriptions-azureps).
* Para obtener un script de ejemplo completo que implementa una plantilla, vea [Resource Manager template deployment script](resource-manager-samples-powershell-deploy.md) (Script de implementación de plantilla de Resource Manager).
* Para entender cómo definir parámetros en la plantilla, consulte [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para obtener sugerencias para resolver los errores de implementación más comunes, consulte [Solución de errores comunes de implementación de Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Para más información sobre la implementación de una plantilla que requiere un token de SAS, vea [Implementación de una plantilla privada con el token de SAS](resource-manager-powershell-sas-token.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).

