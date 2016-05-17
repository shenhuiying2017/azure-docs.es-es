<properties 
	pageTitle="Personalización de la plantilla de Resource Manager exportada | Microsoft Azure" 
	description="Agregue parámetros a una plantilla de Azure Resource Manager exportada y vuelva a implementarla mediante Azure PowerShell o la CLI de Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/10/2016" 
	ms.author="tomfitz"/>

# Personalización de la plantilla de Resource Manager exportada

En este tema se muestra cómo modificar una plantilla exportada para permitir que pase valores adicionales como parámetros. Se basa en los pasos realizados en el tema [Exportación de plantillas de Azure Resource Manager desde recursos existentes](resource-manager-export-template.md), aunque no es esencial que complete primero ese tema. Puede encontrar la plantilla y los scripts que necesita en este tema.

## Visualización de la plantilla exportada

Si ha completado [Exportación de plantillas de Azure Resource Manager desde recursos existentes](resource-manager-export-template.md), abra la plantilla que ha descargado. La plantilla se llama **template.json**. Si tiene Visual Studio o Visual Code, puede usar cualquiera de ellos para editar la plantilla. De lo contrario, puede usar cualquier editor de texto o de json.

Si no ha completado el tutorial anterior, la plantilla exportada se muestra a continuación. Cree un archivo llamado **template.json** y agregue el contenido siguiente al archivo.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
        },
        "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworks_VNET_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "default",
                        "properties": {
                            "addressPrefix": "10.0.0.0/24"
                        }
                    }
                ]
            },
            "dependsOn": []
        },
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccounts_storagetf05092016_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "tags": {},
            "properties": {
                "accountType": "Standard_RAGRS"
            },
            "dependsOn": []
        }
    ]
}
```

La plantilla anterior funciona bien si desea crear el mismo tipo de cuenta de almacenamiento en la misma región con una red virtual que usa el mismo prefijo de dirección y el mismo prefijo de subred para cada implementación. No obstante, Resource Manager permite implementar plantillas con mucha más flexibilidad. Por ejemplo, durante la implementación, puede especificar el tipo de cuenta de almacenamiento que desea crear o los valores que prefiere para el prefijo de la dirección de red virtual y de la subred.

## Personalización de la plantilla

En esta sección, agregará parámetros a la plantilla exportada para poder reutilizarla en la implementación de estos recursos en otros entornos. También agregará algunas características a la plantilla para reducir la probabilidad de que se produzca un error al implementarla. Ya no tendrá que adivinar un nombre único para la cuenta de almacenamiento. En su lugar, la plantilla lo crea. Limitará los valores que se pueden especificar para el tipo de cuenta de almacenamiento solamente a opciones válidas.

1. Para permitir pasar los valores que desee especificar durante la implementación, reemplace la sección **parameters** por las siguientes definiciones de parámetro. Observe los valores permitidos para **storageAccount\_accountType**. Si proporciona accidentalmente un valor no válido, se reconoce ese error antes de que se inicie la implementación. Además, observe que solo va a proporcionar un prefijo para el nombre de la cuenta de almacenamiento y que está limitado a 11 caracteres. Al limitarlo a 11 caracteres, se asegura de que el nombre completo no supere el número máximo de caracteres para una cuenta de almacenamiento. El prefijo permite aplicar una convención de nomenclatura a las cuentas de almacenamiento. Verá cómo crear un nombre único en el paso siguiente.

        "parameters": {
          "storageAccount_prefix": {
            "type": "string",
            "maxLength": 11
          },
          "storageAccount_accountType": {
            "defaultValue": "Standard_RAGRS",
            "type": "string",
            "allowedValues": [
              "Standard_LRS",
              "Standard_ZRS",
              "Standard_GRS",
              "Standard_RAGRS",
              "Premium_LRS"
            ]
          },
          "virtualNetwork_name": {
            "type": "string"
          },
          "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "string"
          },
          "subnetName": {
            "defaultValue": "subnet-1",
            "type": "string"
          },
          "subnetAddressPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "string"
          }
        },
       
2. La sección **variables** de la plantilla está vacía actualmente. Reemplácela por el siguiente código: La sección **variables** le permite, como autor de la plantilla, crear valores que simplifican la sintaxis para el resto de la plantilla. La variable **storageAccount\_name** concatena el prefijo del parámetro con una cadena única que se genera basándose en el identificador del grupo de recursos.

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. Para usar los parámetros y la variable en las definiciones de recursos, reemplace la sección **resources** por las siguientes definiciones. Observe que realmente se ha cambiado muy poco en las definiciones de recursos, aparte del valor que se asigna a la propiedad de recurso. Las propiedades son exactamente iguales que las de la plantilla exportada. Simplemente va a asignar propiedades a los valores de parámetro, en lugar de valores codificados de forma rígida. La ubicación de los recursos se establece en la misma que la del grupo de recursos por medio de la expresión **resourceGroup().location**. Se hace referencia a la variable que creó para el nombre de la cuenta de almacenamiento mediante la expresión **variables**.

        "resources": [
          {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetwork_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "properties": {
              "addressSpace": {
                "addressPrefixes": [
                  "[parameters('addressPrefix')]"
                ]
              },
              "subnets": [
                {
                  "name": "[parameters('subnetName')]",
                  "properties": {
                    "addressPrefix": "[parameters('subnetAddressPrefix')]"
                  }
                }
              ]
            },
            "dependsOn": []
          },
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccount_name')]",
            "apiVersion": "2015-06-15",
            "location": "[resourceGroup().location]",
            "tags": {},
            "properties": {
                "accountType": "[parameters('storageAccount_accountType')]"
            },
            "dependsOn": []
          }
        ]

## Corrección del archivo de parámetros

El archivo de parámetros descargado ya no coincide con los parámetros de su plantilla. No es necesario utilizar un archivo de parámetros, aunque puede simplificar la nueva implementación de un entorno. Usará los valores predeterminados definidos en la plantilla para muchos de los parámetros, por lo que el archivo de parámetros solo necesita dos valores.

Reemplace el contenido del archivo parameters.json por:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

El archivo de parámetros que se mostró antes solamente proporciona valores para los parámetros que carecen de valor predeterminado. Puede proporcionar valores para los demás parámetros cuando desee un valor diferente al predeterminado.

## Implementación de la plantilla

Puede usar Azure PowerShell o la CLI de Azure para implementar la plantilla personalizada y los archivos de parámetros. Si es necesario, instale [Azure PowerShell](powershell-install-configure.md) o la [CLI de Azure](xplat-cli-install.md). Puede usar los scripts que se descargaron con la plantilla cuando exportó la plantilla original o escribir el suyo propio para implementar la plantilla. En este tema se muestran ambas opciones.

2. Para implementarla con su propio script, use uno de los siguientes.

     Para PowerShell, ejecute:
   
        # login
        Add-AzureRmAccount
        
        # create a new resource group
        New-AzureRmResourceGroup -Name ExportGroup -Location "West Europe"

        # deploy the template to the resource group
        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExportGroup -TemplateFile {path-to-file}\template.json -TemplateParameterFile {path-to-file}\parameters.json
        
     Para la CLI de Azure, ejecute:
   
        azure login
        
        azure group create -n ExportGroup -l "West Europe"

        azure group deployment create -f {path-to-file}\azuredeploy.json -e {path-to-file}\parameters.json -g ExportGroup -n ExampleDeployment

3. Si ha descargado la plantilla y los scripts exportados, busque el archivo **deploy.ps1** (para PowerShell) o **deploy.sh** (para la CLI de Azure).

     Para PowerShell, ejecute:

        Get-Item deploy.ps1 | Unblock-File
        
        .\deploy.ps1

     Para la CLI de Azure, ejecute:
     
        .\deploy.sh

## Pasos siguientes

- El [Tutorial de la plantilla de Resource Manager](resource-manager-template-walkthrough.md) se basa en lo que ha aprendido en este tema y en él se crea una plantilla para una solución más complicada. Lo ayuda a comprender mejor los recursos que están disponibles y cómo determinar qué valores se deben proporcionar.
- Para ver cómo exportar una plantilla mediante PowerShell, consulte [Uso de Azure PowerShell con Azure Resource Manager](powershell-azure-resource-manager.md).
- Para ver cómo exportar una plantilla mediante la CLI de Azure, consulte [Uso de la CLI de Azure para Mac, Linux y Windows con Azure Resource Manager](xplat-cli-azure-resource-manager.md). 
- Para aprender sobre la estructura de las plantillas, consulte [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_0511_2016-->