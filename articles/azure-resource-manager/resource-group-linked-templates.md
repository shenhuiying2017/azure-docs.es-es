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
ms.date: 01/17/2018
ms.author: tomfitz
ms.openlocfilehash: 38d4281dfadaefdf331e493745363e8b4152209d
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Uso de plantillas vinculadas y anidadas al implementar recursos de Azure

Para implementar la solución, puede utilizar una sola plantilla o una plantilla principal con varias plantillas relacionadas. La plantilla relacionada puede ser un archivo independiente que está vinculado a partir de la plantilla principal, o bien una plantilla que está anidada dentro de la plantilla principal.

En el caso de soluciones pequeñas o medianas, es más fácil entender y mantener una única plantilla. Es posible ver todos los recursos y valores en un único archivo. Para los escenarios avanzados, las plantillas vinculadas le permiten desglosar la solución en componentes dirigidos y volver a usar plantillas.

Al usar una plantilla vinculada, se crea una plantilla principal que recibe los valores de parámetro durante la implementación. La plantilla principal contiene todas las plantillas vinculadas y pasa valores a esas plantillas según sea necesario.

![plantillas vinculadas](./media/resource-group-linked-templates/nestedTemplateDesign.png)

## <a name="link-or-nest-a-template"></a>Vinculación o anidamiento de una plantilla

Para crear un vínculo a otra plantilla, agregue un recurso **implementaciones** a la plantilla principal.

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

Las propiedades que se proporcionan para el recurso de implementación varían en función de si va a vincular a una plantilla externa o va a anidar una plantilla alineada en la plantilla principal.

### <a name="nested-template"></a>Plantilla anidada

Para anidar la plantilla dentro de la plantilla principal, use la propiedad **template** y especifique la sintaxis de la plantilla.

```json
"resources": [
  {
    "apiVersion": "2017-05-10",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
    "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
              "accountType": "Standard_LRS"
            }
          }
        ]
      }
    }
  }
]
```

> [!NOTE]
> En las plantillas anidadas, no puede utilizar parámetros o variables definidos en la plantilla anidada. Puede usar parámetros y variables de la plantilla principal. En el ejemplo anterior, `[variables('storageName')]` recupera un valor de la plantilla principal, no de la plantilla anidada. Esta restricción no se aplica a las plantillas externas.
>
> No se puede utilizar la función `reference` en la sección de salidas de una plantilla anidada. Para devolver los valores de un recurso implementado en una plantilla anidada, convierta la plantilla anidada en una plantilla vinculada.

### <a name="external-template-and-external-parameters"></a>Plantilla externa y parámetros externos

Para vincular a una plantilla externa y a un archivo de parámetros, utilice **templateLink** y **parametersLink**. Al vincular a una plantilla, el servicio Resource Manager debe tener acceso a ella. No se puede especificar un archivo local o un archivo que solo está disponible en la red local. Solo se puede proporcionar un valor de URI que incluya **http** o **https**. Una opción es colocar la plantilla vinculada en una cuenta de almacenamiento y usar el URI para dicho elemento.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parametersLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
          "contentVersion":"1.0.0.0"
       }
     }
  }
]
```

### <a name="external-template-and-inline-parameters"></a>Plantilla externa y parámetros insertados

O bien, puede proporcionar el parámetro de manera insertada. Para pasar un valor de la plantilla principal a la plantilla vinculada, use **parameters**.

```json
"resources": [
  {
     "apiVersion": "2017-05-10",
     "name": "linkedTemplate",
     "type": "Microsoft.Resources/deployments",
     "properties": {
       "mode": "incremental",
       "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
       },
       "parameters": {
          "StorageAccountName":{"value": "[parameters('StorageAccountName')]"}
        }
     }
  }
]
```

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

## <a name="get-values-from-linked-template"></a>Obtención de valores a partir de la plantilla vinculada

Para obtener un valor de salida de una plantilla vinculada, recupere el valor de propiedad con sintaxis de esta manera: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Los ejemplos siguientes muestran cómo hacer referencia a una plantilla vinculada y recuperar un valor de salida. La plantilla vinculada devuelve un mensaje simple.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "greetingMessage": {
            "value": "Hello World",
            "type" : "string"
        }
    }
}
```

La plantilla principal implementa la plantilla vinculada y obtiene el valor devuelto. Observe que hace referencia al recurso de implementación por su nombre, y utiliza el nombre de la propiedad devuelta por la plantilla vinculada.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'helloworld.json')]",
                    "contentVersion": "1.0.0.0"
                }
            }
        }
    ],
    "outputs": {
        "messageFromLinkedTemplate": {
            "type": "string",
            "value": "[reference('linkedTemplate').outputs.greetingMessage.value]"
        }
    }
}
```

Al igual que otros tipos de recursos, puede establecer dependencias entre la plantilla vinculada y otros recursos. Por lo tanto, cuando los otros recursos requieren un valor de salida de la plantilla vinculada, puede asegurarse de que la plantilla vinculada se implementa antes que ellos. O bien, cuando la plantilla vinculada se basa en otros recursos, puede asegurarse de que otros recursos se implementan antes que la plantilla vinculada.

En el ejemplo siguiente se muestra una plantilla que implementa una dirección IP pública y devuelve el identificador de recurso:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
            "location": "eastus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "resourceID": {
            "type": "string",
            "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
        }
    }
}
```

Para usar la dirección IP pública de la plantilla anterior al implementar un equilibrador de carga, vincule a la plantilla y agregue una dependencia en el recurso de implementación. La dirección IP pública del equilibrador de carga se establece en el valor de salida de la plantilla vinculada.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "loadBalancers_name": {
            "defaultValue": "mylb",
            "type": "string"
        },
        "publicIPAddresses_name": {
            "defaultValue": "myip",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "name": "[parameters('loadBalancers_name')]",
            "apiVersion": "2017-06-01",
            "location": "eastus",
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LoadBalancerFrontEnd",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[reference('linkedTemplate').outputs.resourceID.value]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [],
                "loadBalancingRules": [],
                "probes": [],
                "inboundNatRules": [],
                "outboundNatRules": [],
                "inboundNatPools": []
            },
            "dependsOn": [
                "linkedTemplate"
            ]
        },
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[uri(deployment().properties.templateLink.uri, 'publicip.json')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters":{
                    "publicIPAddresses_name":{"value": "[parameters('publicIPAddresses_name')]"}
                }
            }
        }
    ]
}
```

## <a name="linked-and-nested-templates-in-deployment-history"></a>Plantillas vinculadas y anidadas en el historial de implementación

Resource Manager procesa cada plantilla como una implementación independiente en el historial de implementación. Por lo tanto, una plantilla principal con tres plantillas vinculadas o anidadas aparece en el historial de implementación del modo siguiente:

![Historial de implementación](./media/resource-group-linked-templates/deployment-history.png)

Puede utilizar estas entradas independientes en el historial para recuperar valores de salida después de la implementación. La siguiente plantilla crea una dirección IP pública y genera la dirección IP como salida:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_name": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddresses_name')]",
            "apiVersion": "2017-06-01",
            "location": "southcentralus",
            "properties": {
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Static",
                "idleTimeoutInMinutes": 4,
                "dnsSettings": {
                    "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "returnedIPAddress": {
            "type": "string",
            "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
        }
    }
}
```

La siguiente plantilla se vincula a la plantilla anterior. Crea tres direcciones IP públicas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "[concat('linkedTemplate', copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "Incremental",
              "templateLink": {
                "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
                "contentVersion": "1.0.0.0"
              },
              "parameters":{
                  "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
              }
            },
            "copy": {
                "count": 3,
                "name": "ip-loop"
            }
        }
    ]
}
```

Después de la implementación, puede recuperar los valores de salida con el siguiente script de PowerShell:

```powershell
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
    $name = 'linkedTemplate' + $i;
    $deployment = Get-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
    Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

O bien, el script de la CLI de Azure:

```azurecli
for i in 0 1 2;
do
    name="linkedTemplate$i";
    deployment=$(az group deployment show -g examplegroup -n $name);
    ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
    echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Protección de una plantilla externa

Aunque la plantilla vinculada debe estar disponible externamente, no es necesario que esté generalmente disponible para el público. Puede agregar la plantilla a una cuenta de almacenamiento privada que sea accesible solo al propietario de la cuenta de almacenamiento. Ahora cree un token de Firma de acceso compartido (SAS) para permitir el acceso durante la implementación. Ese token SAS se agrega al identificador URI para la plantilla vinculada. Aunque el token se pasa como una cadena segura, el identificador URI de la plantilla vinculada, incluido el token de SAS, se registra en las operaciones de implementación. Para limitar la exposición, establezca una caducidad para el token.

También se puede limitar el acceso al archivo de parámetros a través de un token de SAS.

En el ejemplo siguiente se muestra cómo pasar un token de SAS al vincular a una plantilla:

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

En la CLI de Azure, se obtiene un token para el contenedor y se implementan las plantillas con el código siguiente:

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

## <a name="example-templates"></a>Plantillas de ejemplo

En los ejemplos siguientes se muestran los usos más comunes de las plantillas vinculadas.

|Plantilla principal  |Plantilla vinculada |DESCRIPCIÓN  |
|---------|---------| ---------|
|[Hello World](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[plantilla vinculada](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Devuelve una cadena de plantilla vinculada. |
|[Load Balancer con dirección IP pública](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[plantilla vinculada](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Devuelve una dirección IP pública de la plantilla vinculada y establece ese valor en el equilibrador de carga. |
|[Varias direcciones IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [plantilla vinculada](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Crea varias direcciones IP públicas en la plantilla vinculada.  |

## <a name="next-steps"></a>pasos siguientes

* Para obtener información sobre cómo definir el orden de implementación de los recursos, consulte [Definición de dependencias en plantillas de Azure Resource Manager](resource-group-define-dependencies.md).
* Para obtener información sobre cómo definir un recurso y crear numerosas instancias de este, consulte [Creación de varias instancias de recursos en Azure Resource Manager](resource-group-create-multiple.md).
* Para obtener información sobre cómo configurar una plantilla en una cuenta de almacenamiento y generar un token de SAS, consulte [Deploy resources with Resource Manager templates and Azure PowerShell](resource-group-template-deploy.md) (Implementación de recursos con plantillas de Resource Manager y Azure PowerShell) o [Deploy resources with Resource Manager templates and Azure CLI](resource-group-template-deploy-cli.md) (Implementación de recursos con plantillas de Azure Manager y la CLI de Azure).