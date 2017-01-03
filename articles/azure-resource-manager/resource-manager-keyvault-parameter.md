---
title: Secreto de Key Vault con la plantilla de Resource Manager | Microsoft Docs
description: "Muestra cómo pasar un secreto de un almacén de claves como un parámetro durante la implementación."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c582c144-4760-49d3-b793-a3e1e89128e2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: fa688748be96aa614b46a218e19a0e771b908baf


---
# <a name="pass-secure-values-during-deployment"></a>Paso de valores seguros durante la implementación

Si necesita pasar un valor seguro (como una contraseña) como un parámetro durante la implementación, puede almacenar ese valor como un secreto en una instancia de [Azure Key Vaulte](../key-vault/key-vault-whatis.md)y hacer referencia al secreto en el archivo de parámetros. El valor nunca se expone debido a que solo hace referencia a su identificador de almacén de claves. No es necesario especificar manualmente el valor del secreto cada vez que implementan los recursos.

## <a name="deploy-a-key-vault-and-secret"></a>Implementación de un almacén de claves y un secreto

Para obtener información sobre cómo implementar un almacén de claves y un secreto, consulte [Esquema del almacén de claves](resource-manager-template-keyvault.md) y [Esquema del secreto del almacén de claves](resource-manager-template-keyvault-secret.md). Al crear el almacén de claves, establezca el valor de la propiedad **enabledForTemplateDeployment** en **True** para que pueda hacerse referencia a él desde otras plantillas de Resource Manager. 

## <a name="reference-a-secret-with-static-id"></a>Referencia a un secreto con identificador estático

Se hace referencia al secreto desde dentro de un archivo de parámetros que pasa valores a la plantilla. Se hace referencia al secreto pasando el identificador de recurso de almacén de claves y el nombre del secreto. En este ejemplo, ya debe existir el secreto del almacén de claves y se utiliza un valor estático para el mismo identificador de recurso.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "sqlsvrAdminLoginPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          },
          "secretName": "adminPassword"
        }
      },
      "sqlsvrAdminLogin": {
        "value": "exampleadmin"
      }
    }
}
```

El parámetro que acepta el secreto debe ser **securestring**. En el ejemplo siguiente se muestran las secciones pertinentes de una plantilla que implementa un SQL Server que requiere una contraseña de administrador.

```json
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
        }
    ],
    "variables": {
        "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
    },
    "outputs": { }
}
```

El usuario que implementa una plantilla que hace referencia a un secreto debe tener el permiso **Microsoft.KeyVault/vaults/deploy/action** para el almacén de claves. Los roles [Propietario](../active-directory/role-based-access-built-in-roles.md#owner) y [Colaborador](../active-directory/role-based-access-built-in-roles.md#contributor) conceden este acceso. También puede crear un [rol personalizado](../active-directory/role-based-access-control-custom-roles.md) que concede este permiso y agrega el usuario a ese rol.

## <a name="reference-a-secret-with-dynamic-id"></a>Referencia a un secreto con identificador dinámico

En la sección anterior se mostró cómo pasar un identificador de recurso estático para el secreto del almacén de claves. Sin embargo, en algunos escenarios, debe hacer referencia a un secreto del Almacén de claves que varía en función de la implementación actual. En ese caso, no se puede codificar el identificador de recurso en el archivo de parámetros. Desafortunadamente, no se puede generar dinámicamente el identificador de recurso en el archivo de parámetros, ya que no se permiten expresiones de plantilla en este tipo de archivos.

Para generar dinámicamente el identificador de recurso de un secreto del almacén de claves, debe mover los recursos que necesite el secreto a una plantilla anidada. En la plantilla principal, agregue la plantilla anidada y pase un parámetro que contenga el identificador de recurso generado dinámicamente.

```json
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
    }],
    "outputs": {}
}
```

## <a name="next-steps"></a>Pasos siguientes
* Para obtener información general sobre almacenes de claves, consulte el artículo de [introducción a Azure Key Vault](../key-vault/key-vault-get-started.md).
* Para obtener información sobre cómo utilizar un Almacén de claves con una máquina virtual, consulte [Consideraciones de seguridad para Azure Resource Manager](best-practices-resource-manager-security.md).
* Para obtener ejemplos completos de secretos de clave de referencia, consulte [Ejemplos del Almacén de claves](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).




<!--HONumber=Nov16_HO3-->


