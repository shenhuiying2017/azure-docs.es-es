<properties
   pageTitle="Plantilla del Administrador de recursos para las asignaciones de roles | Microsoft Azure"
   description="Muestra el esquema del Administrador de recursos para implementar una asignación de rol mediante una plantilla."
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

# Asignaciones de roles: esquema de plantilla

Asigna un usuario, grupo o entidad de servicio a un rol en un ámbito especificado.

## Formato de esquema

Para crear una asignación de roles, agregue el siguiente esquema a la sección de recursos de la plantilla.
    
    {
        "type": "Microsoft.Authorization/roleAssignments",
        "apiVersion": "2014-10-01-preview",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "roleDefinitionId": string,
            "principalId": string,
            "scope": string
        }
    }

## Valores

Las tablas siguientes describen los valores que debe establecer en el esquema.

| Nombre | Valor |
| ---- | ---- |
| type | Enum<br />Obligatorio<br />**Microsoft.Authorization/roleAssignments**<br /><br />Tipo de recurso que se creará. |
| apiVersion | Enum<br />Obligatorio<br />**2014-10-01-preview**<br /><br />Versión de la API que se usará para crear el recurso. |  
| name | Cadena<br />Obligatorio<br />**Globally-unique identifier**<br /><br />Identificador de la nueva asignación de roles. |
| dependsOn | Matriz<br />Opcional<br />Lista separada por comas de nombres o identificadores únicos de recursos.<br /><br />Colección de recursos de los que depende esta asignación de roles. Si asigna un rol que se centra en un recurso y ese recurso se implementa en la misma plantilla, incluya el nombre de ese recurso en este elemento para asegurarse de que el recurso se implementa en primer lugar. | 
| propiedades | Object<br />Obligatorio<br />[Objeto de propiedades](#properties)<br /><br />Objeto que identifica la definición de rol, la entidad y el ámbito. |  

<a id="properties" />
### properties object

| Nombre | Valor |
| ------- | ---- |
| roleDefinitionId | Cadena<br />Obligatorio<br /> **/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}**<br /><br />Identificador de una definición de rol existente que se utilizará en la asignación de roles. |
| principalId | Cadena<br />Obligatorio<br />**Globally-unique identifier**<br /><br />Identificador de una entidad existente. Se asigna al identificador dentro del directorio y puede señalar a un usuario, una entidad de servicio o un grupo de seguridad. |
| ámbito | Cadena<br />Obligatorio<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}** (para grupos de recursos) o<br />**/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}** (para recursos)<br /><br />Ámbito al que se aplica esta asignación de roles. |


## Cómo usar el recurso de asignación de rol

Una asignación de roles se agrega a la plantilla cuando se necesita agregar un usuario, un grupo o una entidad de servicio a un rol durante la implementación. Las asignaciones de roles se heredan de los niveles de ámbito más elevados; por tanto, si ya ha agregado una entidad a un rol en el nivel de suscripción, no es necesario volver a asignarla para el grupo de recursos o el recurso.

Puede generar un nuevo identificador para **name** con:

    PS C:\> $name = [System.Guid]::NewGuid().toString()

Puede recuperar el identificador único global para la definición de roles con:

    PS C:\> $roledef = (Get-AzureRmRoleDefinition -Name Reader).id

Puede recuperar el identificador de la entidad con uno de los siguientes comandos.

Para un grupo denominado **Auditors**:

    PS C:\> $principal = (Get-AzureRmADGroup -SearchString Auditors).id

Para un usuario denominado **exampleperson**:

    PS C:\> $principal = (Get-AzureRmADUser -SearchString exampleperson).id

Para una entidad de servicio denominada **exampleapp**:

    PS C:\> $principal = (Get-AzureRmADServicePrincipal -SearchString exampleapp).id 
 

## Ejemplos

En el ejemplo siguiente se asigna un grupo a un rol para el grupo de recursos.

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

## Plantillas de inicio rápido

Las plantillas siguientes muestran cómo usar el recurso de asignación de rol:

- [Assign built-in role to resource group (Asignar rol integrado al grupo de recursos)](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-resourcegroup)
- [Assign built-in role to existing VM](https://azure.microsoft.com/documentation/templates/101-rbac-builtinrole-virtualmachine)
- [Assign built-in role to multiple existing VMs](https://azure.microsoft.com/documentation/templates/201-rbac-builtinrole-multipleVMs)

## Pasos siguientes

- Para obtener más información sobre la estructura de la plantilla, consulte [Crear plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md).
- Para obtener más información acerca del control de acceso basado en roles, vea [Control de acceso basado en roles de Azure Active Directory](active-directory/role-based-access-control-configure.md).

<!---HONumber=AcomDC_0406_2016-->