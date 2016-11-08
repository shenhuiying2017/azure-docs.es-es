---
title: Plantilla del Administrador de recursos para las asignaciones de roles | Microsoft Docs
description: Muestra el esquema del Administrador de recursos para implementar una asignación de rol mediante una plantilla.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: tomfitz

---
# <a name="role-assignments-template-schema"></a>Asignaciones de roles: esquema de plantilla
Asigna un usuario, grupo o entidad de servicio a un rol en un ámbito especificado.

## <a name="resource-format"></a>Formato de los recursos
Para crear una asignación de roles, agregue el siguiente esquema a la sección de recursos de la plantilla.

    {
        "type": string,
        "apiVersion": "2015-07-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## <a name="values"></a>Valores
Las tablas siguientes describen los valores que debe establecer en el esquema.

| Nombre | Obligatorio | Descripción |
| --- | --- | --- |
| type |Sí |Tipo de recurso que se creará.<br /><br /> Para el grupo de recursos:<br />**Microsoft.Authorization/roleAssignments**<br /><br />Para el recurso:<br />**{provider-namespace}/{resource-type}/providers/roleAssignments** |
| apiVersion |yes |Versión de la API que se usará para crear el recurso.<br /><br /> Use **2015-07-01**. |
| name |yes |Un identificador único global para la nueva asignación de roles. |
| dependsOn |No |Una matriz separada por comas de nombres de recursos o identificadores de recursos únicos.<br /><br />La colección de recursos de la que depende esta asignación de roles. Si asigna un rol que se centra en un recurso y ese recurso se implementa en la misma plantilla, incluya el nombre de ese recurso en este elemento para asegurarse de que el recurso se implementa en primer lugar. |
| propiedades |Sí |Un objeto de propiedades que identifica la definición de roles, la entidad de seguridad y el ámbito. |

### <a name="properties-object"></a>properties object
| Nombre | Obligatorio | Descripción |
| --- | --- | --- |
| roleDefinitionId |yes |El identificador de una definición de rol existente que se usará en la asignación de roles.<br /><br /> Utilice el siguiente formato:<br /> **/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}** |
| principalId |Sí |El identificador único global de una entidad de seguridad existente. Este valor se asigna al identificador dentro del directorio y puede señalar a un usuario, una entidad de servicio o un grupo de seguridad. |
| ámbito |No |El ámbito en el que se aplica esta asignación de roles.<br /><br />Para los grupos de recursos, use:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}**  <br /><br />Para los recursos, use:<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** |

## <a name="how-to-use-the-role-assignment-resource"></a>Cómo usar el recurso de asignación de rol
Una asignación de roles se agrega a la plantilla cuando se necesita agregar un usuario, un grupo o una entidad de servicio a un rol durante la implementación. Las asignaciones de roles se heredan de los niveles de ámbito más elevados; por tanto, si ya ha agregado una entidad a un rol en el nivel de suscripción, no hace falta reasignarla para el grupo de recursos o el recurso.

Hay muchos valores de identificador que debe dar al trabajar con asignaciones de roles. Puede recuperar los valores a través de PowerShell o CLI de Azure.

### <a name="powershell"></a>PowerShell
El nombre de la asignación de roles requiere un identificador único global. Puede generar un nuevo identificador para **name** con:

    $name = [System.Guid]::NewGuid().toString()

Puede recuperar el identificador para la definición de roles con:

    $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Puede recuperar el identificador de la entidad con uno de los siguientes comandos.

Para un grupo denominado **Auditors**:

    $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Para un usuario denominado **exampleperson**:

    $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Para una entidad de servicio denominada **exampleapp**:

    $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 

### <a name="azure-cli"></a>Azure CLI
Puede recuperar el identificador para la definición de roles con:

    azure role show Reader --json | jq .[].Id -r

Puede recuperar el identificador de la entidad con uno de los siguientes comandos.

Para un grupo denominado **Auditors**:

    azure ad group show --search Auditors --json | jq .[].objectId -r

Para un usuario denominado **exampleperson**:

    azure ad user show --search exampleperson --json | jq .[].objectId -r

Para una entidad de servicio denominada **exampleapp**:

    azure ad sp show --search exampleapp --json | jq .[].objectId -r

## <a name="examples"></a>Ejemplos
La siguiente plantilla recibe un identificador para un rol y un identificador para un usuario, un grupo o una entidad de servicio. Asigna el rol en el nivel del grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "roleDefinitionId": {
                "type": "string"
            },
            "roleAssignmentId": {
                "type": "string"
            },
            "principalId": {
                "type": "string"
            }
        },
        "resources": [
            {
              "type": "Microsoft.Authorization/roleAssignments",
              "apiVersion": "2015-07-01",
              "name": "[parameters('roleAssignmentId')]",
              "properties":
              {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]",
                "scope": "[concat(subscription().id, '/resourceGroups/', resourceGroup().name)]"
              }
            }
        ],
        "outputs": {}
    }



La plantilla siguiente crea una cuenta de almacenamiento y le asigna el rol de lector. Los identificadores de dos grupos y el rol de lector se han incluido en la plantilla para simplificar la implementación. Esos valores podrían recuperarse en el momento de la implementación en un script y entregarse como parámetros.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "roleName": {
          "type": "string"
        },
        "groupToAssign": {
          "type": "string",
          "allowedValues": [
            "Auditors",
            "Limited"
          ]
        }
      },
      "variables": {
        "readerRole": "[concat('/subscriptions/',subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]",
        "scope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Storage/storageAccounts/', variables('storageName'))]",
        "Auditors": "1c272299-9729-462a-8d52-7efe5ece0c5c",
        "Limited": "7c7250f0-7952-441c-99ce-40de5e3e30b5",
        "fullRoleName": "[concat(variables('storageName'), '/Microsoft.Authorization/', parameters('roleName'))]"
      },
      "resources": [
        {
          "apiVersion": "2016-01-01",
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[variables('storageName')]",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "tags": {
            "displayName": "MyStorageAccount"
          },
          "properties": {}
        },
        {
          "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
          "apiVersion": "2015-07-01",
          "name": "[variables('fullRoleName')]",
          "dependsOn": [
            "[variables('storageName')]"
          ],
          "properties": {
            "roleDefinitionId": "[variables('readerRole')]",
            "principalId": "[variables(parameters('groupToAssign'))]"
          }
        }
      ]
    }

## <a name="quickstart-templates"></a>Plantillas de inicio rápido
Las plantillas siguientes muestran cómo usar el recurso de asignación de rol:

* [Assign built-in role to resource group (Asignar rol integrado al grupo de recursos)](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
* [Assign built-in role to existing VM](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
* [Assign built-in role to multiple existing VMs](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre la estructura de la plantilla, consulte [Crear plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).
* Para obtener más información sobre el control de acceso basado en roles, consulte [Control de acceso basado en roles de Azure Active Directory](active-directory/role-based-access-control-configure.md).

<!--HONumber=Oct16_HO2-->


