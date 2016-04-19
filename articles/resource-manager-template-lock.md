<properties
   pageTitle="Plantilla del Administrador de recursos para los bloqueos de recursos | Microsoft Azure"
   description="Muestra el esquema del Administrador de recursos para implementar los bloqueos de recursos mediante una plantilla."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
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

| Nombre | Valor |
| ---- | ---- | 
| type | Enum<br />Obligatorio<br />**{namespace}/{type}/providers/locks** (para recursos) o<br />**Microsoft.Authorization/locks**(para grupos de recursos)<br /><br />Tipo de recurso que se creará. |
| apiVersion | Enum<br />Obligatorio<br />**2015-01-01**<br /><br />Versión de la API que se usará para crear el recurso. |  
| name | Cadena<br />Obligatorio<br />**{resource}/Microsoft.Authorization/{lockname}** (para recursos) o<br />**{lockname}** (para grupos de recursos)<br />Hasta 64 caracteres y no puede incluir <>, % &,? o cualquier carácter de control.<br /><br />Valor que especifica el recurso de bloqueo y un nombre para el bloqueo. |
| dependsOn | Matriz<br />Opcional<br />Lista separada por comas de nombres o identificadores únicos de recursos.<br /><br />Colección de recursos de los que depende este bloqueo. Si el recurso que está bloqueando se implementa en la misma plantilla, incluya el nombre de ese recurso en este elemento para asegurarse de que el recurso se implementa primero. | 
| propiedades | Objeto<br />Obligatorio<br />[Objeto de propiedades](#properties)<br /><br />Objeto que identifica el tipo de bloqueo y notas sobre el bloqueo. |  

<a id="properties" />
### properties object

| Nombre | Valor |
| ------- | ---- |
| level | Enum<br />Obligatorio<br />**CannotDelete**<br /><br />Tipo de bloqueo que se aplican al ámbito. CanNotDelete permite la modificación pero impide la eliminación. |
| HDInsight | Cadena<br />Opcional<br />Hasta 512 caracteres<br /><br />Descripción del bloqueo. |


## Cómo usar el recurso de bloqueo

Agrega este recurso a la plantilla para evitar que se realicen acciones especificadas en un recurso. El bloqueo se aplica a todos los usuarios y grupos. Generalmente, aplica un bloqueo solo durante un tiempo limitado; por ejemplo, cuando se ejecuta un proceso y desea garantizar que ninguna persona de su organización modifique o elimine por accidente un recurso.

Para crear o eliminar bloqueos de administración, debe tener acceso a las acciones **Microsoft.Authorization/*** o **Microsoft.Authorization/locks/***. De los roles integrados, solo se conceden esas acciones a **Propietario** y **Administrador de acceso de usuario**. Para información sobre el control de acceso basado en roles, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

El bloqueo se aplica al recurso especificado y a cualquier recurso secundario.

Puede quitar un bloqueo con el comando PowerShell **Remove-AzureRmResourceLock** o con la [operación de eliminación](https://msdn.microsoft.com/library/azure/mt204562.aspx) de la API de REST.

## Ejemplos

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

## Pasos siguientes

- Para obtener más información sobre la estructura de la plantilla, consulte [Crear plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).
- Para obtener más información sobre los bloqueos, consulte [Bloqueo de recursos con el Administrador de recursos de Azure](resource-group-lock-resources.md).

<!---HONumber=AcomDC_0406_2016-->