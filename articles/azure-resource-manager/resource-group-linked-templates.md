---
title: "Plantillas vinculadas para la implementación de Azure | Microsoft Docs"
description: "Describe cómo usar plantillas vinculadas en una plantilla del Administrador de recursos de Azure para crear una solución de plantilla modular. Muestra cómo pasar valores de parámetros y especificar un archivo de parámetros y las direcciones URL creadas dinámicamente."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 27d8c4b2-1e24-45fe-88fd-8cf98a6bb2d2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: 8b58a83ffd473500dd3f76c09e251f9208527d4f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="using-linked-templates-when-deploying-azure-resources"></a>Uso de plantillas vinculadas al implementar recursos de Azure
Desde dentro de una plantilla de Azure Resource Manager, puede realizar la vinculación con otra plantilla que le permita descomponer la implementación en un conjunto de plantillas con fines específicos y dirigidas. Como ocurre con la descomposición de una aplicación en varias clases de código, la descomposición proporciona ventajas en cuanto a las pruebas, la reutilización y la legibilidad.  

Puede pasar parámetros de una plantilla principal a una plantilla vinculada de nuevo, y esos parámetros pueden asignarse directamente a parámetros o variables expuestas por la plantilla de llamada. La plantilla vinculada también puede pasar una variable de salida de nuevo a la plantilla de origen, lo que permite un intercambio de datos bidireccional entre las plantillas.

## <a name="linking-to-a-template"></a>Vinculación con una plantilla
Cree un vínculo entre dos plantillas mediante la adición de un recurso de implementación dentro de la plantilla principal que apunta a la plantilla vinculada. Para ello, debe establecer la propiedad **templateLink** en el URI de la plantilla vinculada. Los valores de los parámetros de la plantilla se pueden proporcionar directamente en la plantilla o en archivo de parámetros. El siguiente ejemplo usa la propiedad **parameters** para especificar un valor de parámetro directamente.

```json
"resources": [ 
  { 
      "apiVersion": "2017-05-10", 
      "name": "linkedTemplate", 
      "type": "Microsoft.Resources/deployments", 
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": { 
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
        } 
      } 
  } 
] 
```

Al igual que otros tipos de recursos, puede establecer dependencias entre la plantilla vinculada y otros recursos. Por lo tanto, cuando los otros recursos requieren un valor de salida de la plantilla vinculada, puede asegurarse de que la plantilla vinculada se implementa antes que ellos. O bien, cuando la plantilla vinculada se basa en otros recursos, puede asegurarse de que otros recursos se implementan antes que la plantilla vinculada. Puede recuperar un valor de una plantilla vinculada con la sintaxis siguiente:

```json
"[reference('linkedTemplate').outputs.exampleProperty.value]"
```

El servicio Resource Manager debe tener acceso a la plantilla vinculada. No se puede especificar un archivo local o un archivo que solo está disponible en la red local para la plantilla vinculada. Solo se puede proporcionar un valor de URI que incluya **http** o **https**. Una opción es colocar la plantilla vinculada en una cuenta de almacenamiento y usar el URI para dicho elemento, como se muestra en el ejemplo siguiente:

```json
"templateLink": {
    "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
    "contentVersion": "1.0.0.0",
}
```

Aunque la plantilla vinculada debe estar disponible externamente, no es necesario que esté generalmente disponible para el público. Puede agregar la plantilla a una cuenta de almacenamiento privada que sea accesible solo al propietario de la cuenta de almacenamiento. Ahora cree un token de Firma de acceso compartido (SAS) para permitir el acceso durante la implementación. Ese token SAS se agrega al identificador URI para la plantilla vinculada. Para obtener información sobre cómo configurar una plantilla en una cuenta de almacenamiento y generar un token de SAS, consulte [Deploy resources with Resource Manager templates and Azure PowerShell](resource-group-template-deploy.md) (Implementación de recursos con plantillas de Resource Manager y Azure PowerShell) o [Deploy resources with Resource Manager templates and Azure CLI](resource-group-template-deploy-cli.md) (Implementación de recursos con plantillas de Azure Manager y la CLI de Azure). 

En el ejemplo siguiente se muestra una plantilla principal que se vincula a otra plantilla. El acceso a la plantilla anidada se obtiene con un token de SAS que se pasa como un parámetro.

```json
"parameters": {
    "sasToken": { "type": "securestring" }
},
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "linkedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
          "mode": "incremental",
          "templateLink": {
            "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
            "contentVersion": "1.0.0.0"
          }
        }
    }
],
```

Aunque el token se pasa como una cadena segura, el identificador URI de la plantilla vinculada, incluido el token de SAS, se registra en las operaciones de implementación. Para limitar la exposición, establezca una caducidad para el token.

Resource Manager controla cada plantilla vinculada como una implementación independiente. En el historial de implementación para el grupo de recursos, vea implementaciones independientes del elemento primario y las plantillas anidadas.

![Historial de implementación](./media/resource-group-linked-templates/linked-deployment-history.png)

## <a name="linking-to-a-parameter-file"></a>Vinculación con un archivo de parámetros
El siguiente ejemplo utiliza la propiedad **parametersLink** para vincular a un archivo de parámetros.

```json
"resources": [ 
  { 
     "apiVersion": "2017-05-10", 
     "name": "linkedTemplate", 
     "type": "Microsoft.Resources/deployments", 
     "properties": { 
       "mode": "incremental", 
       "templateLink": {
          "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       }, 
       "parametersLink": { 
          "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
          "contentVersion":"1.0.0.0"
       } 
     } 
  } 
] 
```

El valor del URI para el archivo del parámetro vinculado no puede ser un archivo local y debe incluir **http** o **https**. También se puede limitar el acceso al archivo de parámetros a través de un token de SAS.

## <a name="using-variables-to-link-templates"></a>Uso de variables para vincular plantillas
Los ejemplos anteriores mostraron valores de dirección URL codificadas de forma rígida para los vínculos de la plantilla. Este enfoque puede funcionar en una plantilla sencilla pero no funciona bien cuando se trabaja con un gran conjunto de plantillas modulares. En su lugar, puede crear una variable estática que almacene una dirección URL base para la plantilla principal y, luego, crear dinámicamente direcciones URL para las plantillas vinculadas desde esa dirección URL base. La ventaja de este enfoque es que puede mover fácilmente o bifurcar la plantilla porque solo tendrá que cambiar la variable estática en la plantilla principal. La plantilla principal pasa los URI correctos por toda la plantilla descompuesta.

En el ejemplo siguiente se muestra cómo usar una dirección URL base para crear dos direcciones URL para las plantillas vinculadas (**sharedTemplateUrl** y **vmTemplate**). 

```json
"variables": {
    "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
    "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
    "vmTemplateUrl": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

También puede usar la función [deployment()](resource-group-template-functions-deployment.md#deployment) para obtener la dirección URL base de la plantilla actual y usar esta información para obtener la dirección URL de otras plantillas en la misma ubicación. Este enfoque resulta útil si cambia la ubicación de la plantilla (probablemente debido al control de versiones) o desea evitar la codificación de forma rígida de las direcciones URL en el archivo de plantilla. 

```json
"variables": {
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

## <a name="complete-example"></a>Ejemplo completo
Las plantillas de ejemplo siguientes muestran una organización simplificada de plantillas vinculadas para ilustrar algunos de los conceptos de este artículo. Se supone que las plantillas se agregaron en el mismo contenedor en una cuenta de almacenamiento con el acceso público desactivado. La plantilla vinculada pasa un valor de vuelta a la plantilla principal en la sección **salidas** .

El archivo **parent.json** consta de lo siguiente:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "string" }
  },
  "resources": [
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[reference('linkedTemplate').outputs.result.value]"
    }
  }
}
```

El archivo **helloworld.json** consta de lo siguiente:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "result": {
        "value": "Hello World",
        "type" : "string"
    }
  }
}
```

En PowerShell, se obtiene un token para el contenedor y se implementan las plantillas con lo siguiente:

```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzureStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

En la CLI de Azure 2.0, se obtiene un token para el contenedor y se implementan las plantillas con el código siguiente:

```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name storagecontosotemplates \
    --query connectionString)
token=$(az storage container generate-sas \
    --name templates \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name parent.json \
    --output tsv \
    --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az group deployment create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="next-steps"></a>Pasos siguientes
* Para obtener información sobre cómo definir el orden de implementación de los recursos, consulte [Definición de dependencias en plantillas de Azure Resource Manager](resource-group-define-dependencies.md)
* Para obtener información sobre cómo definir un recurso y crear numerosas instancias de este, consulte [Creación de varias instancias de recursos en Azure Resource Manager](resource-group-create-multiple.md)

