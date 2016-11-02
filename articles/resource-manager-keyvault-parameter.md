<properties
   pageTitle="Secreto del Almacén de claves con la plantilla de Resource Manager | Microsoft Azure"
   description="Muestra cómo pasar un secreto de un almacén de claves como un parámetro durante la implementación."
   services="azure-resource-manager,key-vault"
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
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# Paso de valores seguros durante la implementación

Si necesita pasar un valor seguro (como una contraseña) como un parámetro durante la implementación, puede almacenar ese valor como un secreto en un [Almacén de claves de Azure](./key-vault/key-vault-whatis.md) y hacer referencia al valor en otras plantillas del Administrador de recursos. Incluya solo una referencia al secreto en la plantilla para que el secreto nunca se exponga. No es necesario especificar manualmente el valor para el secreto cada vez que implemente los recursos. Especifique qué usuarios o entidades de servicio pueden tener acceso al secreto.

## Implementación de un almacén de claves y un secreto

Para crear un almacén de claves al que se pueda hacer referencia desde otras plantillas del Administrador de recursos, debe establecer la propiedad **enabledForTemplateDeployment** en **true**, y debe conceder acceso al usuario o entidad de servicio que ejecutará la implementación que hace referencia al secreto.

Para obtener información acerca de cómo implementar un almacén de claves y un secreto, consulte [Esquema del almacén de claves](resource-manager-template-keyvault.md) y [Esquema del secreto del almacén de claves](resource-manager-template-keyvault-secret.md).

## Referencia a un secreto con identificador estático

Se hace referencia al secreto desde dentro de un archivo de parámetros que pasa valores a la plantilla. Se hace referencia al secreto pasando el identificador de recurso de almacén de claves y el nombre del secreto. En este ejemplo, ya debe existir el secreto del Almacén de claves y se utiliza un valor estático para el mismo id. de recurso.

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

Un archivo de parámetros completo podría tener el siguiente aspecto:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

El parámetro que acepta el secreto debe ser **securestring**. En el ejemplo siguiente se muestran las secciones pertinentes de una plantilla que implementa un SQL Server que requiere una contraseña de administrador.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }

## Referencia a un secreto con identificador dinámico

En la sección anterior se mostró cómo pasar un identificador de recurso estático para el secreto del Almacén de claves. Sin embargo, en algunos escenarios, debe hacer referencia a un secreto del Almacén de claves que varía en función de la implementación actual. En ese caso, no se puede codificar el identificador de recurso en el archivo de parámetros. Desafortunadamente, no se puede generar dinámicamente el identificador de recurso en el archivo de parámetros, ya que no se permiten expresiones de plantilla en este tipo de archivos.

Para generar dinámicamente el identificador de recurso de un secreto del Almacén de claves, debe mover los recursos que necesite el secreto a una plantilla anidada. En la plantilla principal, agregue la plantilla anidada y pase un parámetro que contenga el identificador de recurso generado dinámicamente.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "vaultName": {
          "type": "string"
        },
        "secretName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "nestedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "adminPassword": {
                "reference": {
                  "keyVault": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
                  },
                  "secretName": "[parameters('secretName')]"
                }
              }
            }
          }
        }
      ],
      "outputs": {}
    }


## Pasos siguientes

- Para obtener información general sobre almacenes de claves, consulte [Introducción al Almacén de claves de Azure](./key-vault/key-vault-get-started.md).
- Para obtener información sobre cómo utilizar un Almacén de claves con una máquina virtual, consulte [Consideraciones de seguridad para Azure Resource Manager](best-practices-resource-manager-security.md).
- Para obtener ejemplos completos de secretos de clave de referencia, consulte [Ejemplos del Almacén de claves](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

<!---HONumber=AcomDC_0629_2016-->