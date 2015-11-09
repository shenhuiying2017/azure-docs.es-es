<properties
   pageTitle="Plantilla del Administrador de recursos para los bloqueos de recursos | Microsoft Azure"
   description="Muestra el esquema del Administrador de recursos para los bloqueos de recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2015"
   ms.author="tomfitz"/>

# Bloqueos de recursos: esquema de plantilla

Crea un nuevo bloqueo en un recurso y sus recursos secundarios.

## Formato de esquema

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



## Valores

Las tablas siguientes describen los valores que debe establecer en el esquema.

| Nombre | Tipo | Obligatorio | Valores permitidos | Descripción |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Sí | Para recursos: <br />**{namespace}/{type}/providers/locks**<br /><br />Para grupos de recursos:<br />**Microsoft.Authorization/locks** | El tipo de recurso que se creará. |
| apiVersion | enum | Sí | **2015-01-01** | La versión de la API que se usará para crear el recurso. |  
| name | cadena | Sí | Para recursos:<br />**{resouce}/Microsoft.Authorization/{lockname}**<br /><br />Para grupos de recursos:<br />**{lockname}****<br /><br />hasta 64 caracteres<br />No puede incluir <, > %, &, ? ni ningún carácter de control. | Un valor que especifica el recurso que se bloqueará y un nombre para el bloqueo. | | dependsOn | array | No | Una lista separada por comas de los nombres de un recurso o de los identificadores únicos de un recurso. | La colección de recursos de la que depende este bloqueo. Si el recurso que bloquea se implementa en la misma plantilla, incluya el nombre de ese recurso en este elemento para garantizar que el recurso se implemente primero. | | properties | object | Yes | (se muestra a continuación) | Un objeto que identifica el tipo de bloqueo y notas sobre el bloqueo. | 

### properties object

| Nombre | Tipo | Obligatorio | Valores permitidos | Descripción |
| ------- | ---- | ---------------- | -------- | ----------- |
| level | enum | Sí | **CannotDelete** <br /> **ReadOnly** | El tipo de bloqueo que se aplicará al ámbito. CanNotDelete permite la modificación, pero evita la eliminación. ReadOnly evita la modificación y la eliminación. |
| HDInsight | cadena | No | 512 caracteres | Descripción del bloqueo. |


## Cómo usar el recurso de bloqueo

Agrega este recurso a la plantilla para evitar que se realicen acciones especificadas en un recurso. El bloqueo se aplica a todos los usuarios y grupos. Generalmente, aplica un bloqueo solo durante un tiempo limitado; por ejemplo, cuando se ejecuta un proceso y desea garantizar que ninguna persona de su organización modifique o elimine por accidente un recurso.

Para crear o eliminar bloqueos de administración, debe tener acceso a las acciones **Microsoft.Authorization/*** o **Microsoft.Authorization/locks/***. De los roles integrados, solo se conceden esas acciones a **Propietario** y **Administrador de acceso de usuario**. Para obtener más información sobre el control de acceso basado en rol, consulte [Administración del acceso a los recursos](./azure-portal/resource-group-rbac.md).

El bloqueo se aplica al recurso especificado y a cualquier recurso secundario. Si se aplica más de un bloqueo a un recurso, el bloqueo más restrictivo tiene prioridad. Por ejemplo, si aplica ReadOnly en el nivel primario (por ejemplo, el grupo de recursos) y CanNotDelete a un recurso dentro de ese grupo, prevalece el bloqueo más restrictivo (ReadOnly) del elemento primario.

Puede quitar un bloqueo con el comando PowerShell **Remove-AzureRmResourceLock** o con la [operación de eliminación](https://msdn.microsoft.com/library/azure/mt204562.aspx) de la API de REST.

## Ejemplos

El ejemplo siguiente aplica un bloqueo de solo lectura a una aplicación web.

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
                    "level": "ReadOnly",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

El próximo ejemplo aplica un bloqueo de solo lectura al grupo de recursos.

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
                    "level": "ReadOnly",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## Pasos siguientes

- Para obtener más información sobre la estructura de la plantilla, consulte [Crear plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).
- Para obtener más información sobre los bloqueos, consulte [Bloqueo de recursos con el Administrador de recursos de Azure](resource-group-lock-resources.md).

<!---HONumber=Nov15_HO1-->