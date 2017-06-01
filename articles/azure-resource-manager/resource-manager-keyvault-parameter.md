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
ms.date: 12/09/2016
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 0bf872a44b8ed7cae53d2659aa7be878902130e9
ms.contentlocale: es-es
ms.lasthandoff: 05/26/2017


---
# <a name="use-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Uso de Key Vault para pasar el valor de parámetro seguro durante la implementación

Cuando tiene que pasar un valor seguro (por ejemplo, una contraseña) como un parámetro durante la implementación, puede recuperar el valor de [Azure Key Vault](../key-vault/key-vault-whatis.md). El valor se recupera haciendo referencia a Key Vault y al secreto del archivo de parámetros. El valor nunca se expone debido a que solo hace referencia a su identificador de almacén de claves. No es necesario especificar manualmente el valor del secreto cada vez que implementan los recursos. Key Vault puede existir en una suscripción diferente en la que está implementando el grupo de recursos. Cuando hace referencia a Key Vault, incluye el identificador de suscripción.

En este tema se muestra cómo crear un Key Vault y el secreto, configurar el acceso al secreto para una plantilla de Resource Manager y pasar el secreto como un parámetro. Si ya tiene un Key Vault y un secreto, pero necesita comprobar el acceso de usuario y la plantilla, vaya a la sección [Habilitación del acceso al secreto](#enable-access-to-the-secret). Si ya tiene Key Vault y el secreto y está seguro de está configurado para el acceso de usuario y la plantilla, vaya a la sección [Referencia a un secreto con identificador estático](#reference-a-secret-with-static-id). 

## <a name="deploy-a-key-vault-and-secret"></a>Implementación de un almacén de claves y un secreto

Puede implementar Key Vault y un secreto a través de una plantilla de Resource Manager. Para obtener un ejemplo, vea [Esquema de la plantilla de Key Vault](resource-manager-template-keyvault.md) y [Key vault secret template](resource-manager-template-keyvault-secret.md) (Plantilla de secreto de Key Vault). Al crear el almacén de claves, establezca el valor de la propiedad **enabledForTemplateDeployment** en **True** para que pueda hacerse referencia a él desde otras plantillas de Resource Manager. 

O bien, puede crear Key Vault y el secreto a través de Azure Portal. 

1. Seleccione **Nuevo** -> **Seguridad e identidad** -> **Key Vault**.

   ![crear un Key Vault nuevo](./media/resource-manager-keyvault-parameter/new-key-vault.png)

2. Proporcione valores para Key Vault. Por ahora, puede omitir la configuración de **Directivas de acceso** y **Directiva de acceso avanzado**. Dichas configuraciones se tratarán en la sección. Seleccione **Crear**.

   ![establecer Key Vault](./media/resource-manager-keyvault-parameter/create-key-vault.png)

3. Ahora dispone de un Key Vault. Seleccione ese Key Vault.

4. En la hoja Key Vault, seleccione **Secretos**.

   ![seleccionar secretos](./media/resource-manager-keyvault-parameter/select-secret.png)

5. Seleccione **Agregar**.

   ![seleccionar agregar](./media/resource-manager-keyvault-parameter/add-secret.png)

6. Seleccione **Manual** para opciones de carga. Proporcione un nombre y valor para el secreto. Seleccione **Crear**.

   ![proporcionar secreto](./media/resource-manager-keyvault-parameter/provide-secret.png)

Ha creado su Key Vault y el secreto.

## <a name="enable-access-to-the-secret"></a>Habilitación del acceso al secreto

Si usa un nuevo Key Vault o uno ya existente, asegúrese de que el usuario que implementa la plantilla puede acceder al secreto. El usuario que implementa una plantilla que hace referencia a un secreto debe tener el permiso `Microsoft.KeyVault/vaults/deploy/action` para Key Vault. Los roles [Propietario](../active-directory/role-based-access-built-in-roles.md#owner) y [Colaborador](../active-directory/role-based-access-built-in-roles.md#contributor) conceden este acceso. También puede crear un [rol personalizado](../active-directory/role-based-access-control-custom-roles.md) que concede este permiso y agrega el usuario a ese rol. Además, debe conceder a Resource Manager la capacidad de acceder a Key Vault durante la implementación.

Puede comprobar o realizar estos pasos a través del portal.

1. Seleccione **Access Control (IAM)**.

   ![seleccionar control de acceso](./media/resource-manager-keyvault-parameter/select-access-control.png)

2. Si la cuenta que piensa utilizar para la implementación de plantillas todavía no tiene el rol Propietario o Colaborador (o se ha agregado a un rol personalizado que conceda permiso `Microsoft.KeyVault/vaults/deploy/action`), seleccione **Agregar**

   ![agregar usuario](./media/resource-manager-keyvault-parameter/add-user.png)

3. Seleccione el rol Colaborador o Propietario y busque la identidad para asignar a ese rol. Seleccione **Aceptar** para terminar de agregar la identidad al rol.

   ![agregar usuario](./media/resource-manager-keyvault-parameter/search-user.png)

4. Para habilitar el acceso desde una plantilla durante la implementación, seleccione **Directivas de acceso avanzado**. Seleccione la opción **Habilitar el acceso a Azure Resource Manager para la implementación de plantillas**.

   ![habilitar el acceso de plantilla](./media/resource-manager-keyvault-parameter/select-template-access.png)

## <a name="reference-a-secret-with-static-id"></a>Referencia a un secreto con identificador estático

Se hace referencia al secreto desde dentro de un **archivo de parámetros (no la plantilla)** que pasa valores a la plantilla. Se hace referencia al secreto pasando el identificador de recurso de almacén de claves y el nombre del secreto. En este ejemplo, ya debe existir el secreto del almacén de claves y se utiliza un valor estático para el mismo identificador de recurso.

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

En la plantilla, el parámetro que acepta el secreto debe ser **securestring**. En el ejemplo siguiente se muestran las secciones pertinentes de una plantilla que implementa un SQL Server que requiere una contraseña de administrador.

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
* Para obtener ejemplos completos de secretos de clave de referencia, consulte [Ejemplos del Almacén de claves](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).


