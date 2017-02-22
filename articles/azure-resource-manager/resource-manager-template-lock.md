---
title: Plantilla de Resource Manager para los bloqueos de recursos | Microsoft Docs
description: Muestra el esquema del Administrador de recursos para implementar los bloqueos de recursos mediante una plantilla.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: de6c0c57-e33a-4960-98e0-900901592003
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 16f87b8859a5d98c2328388ef12b13ef0e261861


---
# <a name="resource-locks-template-schema"></a>Bloqueos de recursos: esquema de plantilla
Crea un bloqueo en un recurso y sus recursos secundarios.

## <a name="schema-format"></a>Formato de esquema
Para crear un bloqueo, agregue el siguiente esquema a la sección de recursos de la plantilla.

    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## <a name="values"></a>Valores
Las tablas siguientes describen los valores que debe establecer en el esquema.

| Nombre | Obligatorio | Descripción |
| --- | --- | --- |
| type |Sí |Tipo de recurso que se creará.<br /><br />Para los recursos, use lo siguiente:<br />**{namespace}/{type}/providers/locks**<br /><br/>Para los grupos de recursos, use lo siguiente:<br />**Microsoft.Authorization/locks** |
| apiVersion |yes |Versión de la API que se usará para crear el recurso.<br /><br />Uso:<br />**2015-01-01**<br /><br /> |
| name |Sí |Un valor que especifica el recurso que se va a bloquear y un nombre para el bloqueo. Puede tener hasta 64 caracteres y no puede incluir <, >, % &,? o cualquier carácter de control.<br /><br />Para los recursos, use lo siguiente:<br />**{resource}/Microsoft.Authorization/{lockname}**<br /><br />Para los grupos de recursos, use lo siguiente:<br />**{lockname}** |
| dependsOn |No |Una lista separada por comas de nombres de recursos o identificadores de recursos únicos.<br /><br />Colección de recursos de los que depende este bloqueo. Si el recurso que está bloqueando se implementa en la misma plantilla, incluya el nombre de ese recurso en este elemento para asegurarse de que el recurso se implementa primero. |
| propiedades |Sí |Objeto que identifica el tipo de bloqueo y notas sobre el bloqueo.<br /><br />Consulte [properties object](#properties-object). |

### <a name="properties-object"></a>properties object
| Nombre | Obligatorio | Descripción |
| --- | --- | --- |
| level |Sí |Tipo de bloqueo que se aplican al ámbito.<br /><br />**CannotDelete**: los usuarios pueden modificar recursos, pero no eliminarlos.<br />**ReadOnly**: los usuarios autorizados solo pueden leer recursos, pero no realizar acciones en ellos ni eliminarlos. |
| HDInsight |No |Descripción del bloqueo. Puede tener hasta 512 caracteres. |

## <a name="how-to-use-the-lock-resource"></a>Cómo usar el recurso de bloqueo
Agrega este recurso a la plantilla para evitar que se realicen acciones especificadas en un recurso. El bloqueo se aplica a todos los usuarios y grupos.

Para crear o eliminar bloqueos de administración, debe tener acceso a las acciones **Microsoft.Authorization/*** o **Microsoft.Authorization/locks/***. Entre los roles integrados, solamente se conceden esas acciones al **propietario** y al **administrador de acceso de usuarios**. Para información sobre el control de acceso basado en roles, vea [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md).

El bloqueo se aplica al recurso especificado y a cualquier recurso secundario.

Puede quitar un bloqueo con el comando PowerShell **Remove-AzureRmResourceLock** o con la [operación de eliminación](https://msdn.microsoft.com/library/azure/mt204562.aspx) de la API de REST.

## <a name="examples"></a>Ejemplos
El ejemplo siguiente aplica un bloqueo de no se puede eliminar a una aplicación web.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                  "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

El próximo ejemplo aplica un bloqueo de no se puede eliminar al grupo de recursos.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre la estructura de la plantilla, consulte [Crear plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).
* Para obtener más información sobre los bloqueos, consulte [Bloqueo de recursos con el Administrador de recursos de Azure](resource-group-lock-resources.md).




<!--HONumber=Nov16_HO3-->


