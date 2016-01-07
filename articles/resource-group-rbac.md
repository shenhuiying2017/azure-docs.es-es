<properties
   pageTitle="Administración del acceso a los recursos"
   description="Utilice el control de acceso basado en roles (RBAC) para administrar permisos de usuario para los recursos implementados en Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="11/28/2015"
   ms.author="tomfitz"/>

# Administración del acceso a los recursos

Con Administrador de recursos de Azure, puede asegurarse de que los usuarios de su organización tendrán los permisos adecuados para administrar o acceder a los recursos. Administrador de recursos aprovecha las ventajas del control de acceso basado en roles (RBAC) para que pueda aplicar fácilmente las directivas de seguridad a recursos individuales o a grupos de recursos. Por ejemplo, puede conceder a un usuario acceso a una máquina virtual específica en una suscripción o conceder a un usuario la capacidad de administrar todos los sitios web de una suscripción, pero ningún otro recurso.

Este tema se centra en los comandos que se utilizan para asignar roles y permisos. Para obtener información general sobre el control de acceso basado en roles, consulte [Control de acceso basado en roles en el portal de Microsoft Azure](active-directory/role-based-access-control-configure.md).

## Conceptos

Hay unos cuantos conceptos claves acerca del control de acceso basado en roles que conviene entender:

1. Entidad de seguridad: la entidad que concede el permiso, por ejemplo, un usuario de Azure Active Directory, un grupo de seguridad o aplicación.
2. Rol: un conjunto de acciones permitidas y excluidas.
3. Acción: una acción que se realiza en un recurso (p. ej., leer, crear). 
4. Ámbito: el nivel al que se aplica un rol, como la suscripción, grupo de recursos o recurso.
5. Asignación de rol: el proceso de asociar una entidad de seguridad a un rol y establecer el ámbito.

Puede obtener una lista de **acciones** admitidas mediante la herramienta de la CLI multiplataforma de Azure (xPlat) o el módulo de Azure PowerShell.

Para la herramienta de la CLI multiplataforma de Azure, use el siguiente comando para enumerar **todas** las acciones para todos los proveedores de recursos.

    azure provider operations show --operationSearchString '*';

Si usa el módulo de PowerShell de Administrador de recursos de Azure (AzureRm), use el siguiente comando para enumerar **todas** las acciones para **todos** los proveedores de recursos.

    Get-AzureRmProviderOperation -OperationSearchString *;

## Ejemplos de rol
Para comprender los conceptos RBAC, echemos un vistazo a algunos ejemplos de definiciones de roles comunes:

| Rol | Acciones permitidas |
| ------- | ----------------- |
| Lector | **/lector (leer todo) |
| Propietario | * (Leer/escribir todo) |

Para asignar el rol **Lector** al **Usuario A** para el grupo de recursos denominado **ExampleGroup** y el rol **Propietario** al **Usuario B** para toda la suscripción, se podría asignar lo siguiente:

| Rol | Principal | Scope |
| --------|-------------|---------- |
| Lector | Usuario A | /subscriptions/{subscriptionId}/resourceGroups/examplegroup |
| Propietario | Usuario B | /subscriptions/{subscriptionId} |

## Escenarios de ejemplo

En este tema, verá cómo realizar los siguientes escenarios comunes a través de Azure PowerShell, CLI de Azure para Mac, Linux y Windows y API de REST.

1. Consulte las funciones disponibles en una suscripción y las acciones permitidas para dichos roles.
2. Conceda permisos de lector a los miembros de un grupo a través de la suscripción.
3. Conceda permisos de colaborador a una aplicación para que la aplicación administre los recursos dentro del grupo de recursos.
4. Conceda permisos de propietario para un determinado sitio web a un único usuario.
5. Lista de los registros de auditoría del grupo de recursos.


## Uso de PowerShell para administrar el acceso

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]


### Vista de roles disponibles
Para ver todos los roles disponibles para la suscripción, ejecute el comando **Get-AzureRmRoleDefinition**.

    PS C:\> Get-AzureRmRoleDefinition
    
    Name             : API Management Service Contributor
    Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
    IsCustom         : False
    Description      : Lets you manage API Management services, but not access to them.
    Actions          : {Microsoft.ApiManagement/Services/*, Microsoft.Authorization/*/read,
                       Microsoft.Resources/subscriptions/resourceGroups/read,
                       Microsoft.Resources/subscriptions/resourceGroups/resources/read...}
    NotActions       : {}
    AssignableScopes : {/}

    Name             : Application Insights Component Contributor
    Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
    IsCustom         : False
    Description      : Lets you manage Application Insights components, but not access to them.
    Actions          : {Microsoft.Insights/components/*, Microsoft.Insights/webtests/*, Microsoft.Authorization/*/read,
                       Microsoft.Resources/subscriptions/resourceGroups/read...}
    NotActions       : {}
    AssignableScopes : {/}
    ...

### Conceda permiso de lectura a un grupo para la suscripción.
1. Revise la definición del rol **Lector** al proporcionar el nombre del rol cuando se ejecuta el comando **Get-AzureRmRoleDefinition**. Compruebe que las acciones permitidas son las que se pretende asignar.

        PS C:\> Get-AzureRmRoleDefinition Reader
   
        Name             : Reader
        Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
        IsCustom         : False
        Description      : Lets you view everything, but not make any changes.
        Actions          : {*/read}
        NotActions       : {}
        AssignableScopes : {/}

2. Obtenga el grupo de seguridad necesario mediante la ejecución del comando **Get-AzureRmADGroup**. Proporcione el nombre real del grupo en su suscripción. ExampleAuditorGroup se muestra a continuación.

        PS C:\> $group = Get-AzureRmAdGroup -SearchString ExampleAuditorGroup

3. Cree la asignación de roles para el grupo de seguridad de auditor. Cuando se complete el comando, se devuelve la nueva asignación de roles.

        PS C:\> New-AzureRmRoleAssignment -ObjectId $group.Id -Scope /subscriptions/{subscriptionId}/ -RoleDefinitionName Reader


###Conceda permisos de colaborador para una aplicación a un grupo de recursos.
1. Revise la definición del rol **Colaborador** al proporcionar el nombre del rol cuando se ejecuta el comando **Get-AzureRmRoleDefinition**. Compruebe que las acciones permitidas son las que se pretende asignar.

        PS C:\> Get-AzureRmRoleDefinition Contributor

2. Obtenga el identificador del objeto de la entidad de servicio mediante la ejecución del comando **Get-AzureRmADServicePrincipal** y el suministro del nombre de la aplicación en su suscripción. ExampleApplication se muestra a continuación.

        PS C:\> $service = Get-AzureRmADServicePrincipal -SearchString ExampleApplicationName

3. Cree las asignaciones de roles para la entidad de servicio mediante la ejecución del comando **New-AzureRmRoleAssignment**.

        PS C:\> New-AzureRmRoleAssignment -ObjectId $service.Id -ResourceGroupName ExampleGroupName -RoleDefinitionName Contributor

Para obtener una explicación más completa de la configuración de una aplicación de Azure Active Directory y de una entidad de servicio, consulte [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](resource-group-authenticate-service-principal.md).

###Conceda permisos de propietario a un usuario para un recurso.
1. Revise la definición del rol **Propietario** al proporcionar el nombre del rol cuando se ejecuta el comando **Get-AzureRmRoleDefinition**. Compruebe que las acciones permitidas son las que se pretende asignar.

        PS C:\> Get-AzureRmRoleDefinition Owner

2. Cree las asignaciones de roles para el usuario.

        PS C:\> New-AzureRmRoleAssignment -UserPrincipalName "someone@example.com" -ResourceGroupName {groupName} -ResourceType "Microsoft.Web/sites" -ResourceName "mysite" -RoleDefinitionName Owner


###Lista de los registros de auditoría del grupo de recursos.
Para obtener el registro de auditoría de un grupo de recursos, ejecute el comando **Get-AzureRmLog** (o **Get-AzureResourceGroupLog** para las versiones de Azure PowerShell anteriores a la versión preliminar 1.0).

      PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroupName

## Uso de la CLI de Azure para Mac, Linux y Windows

Si no tiene la CLI de Azure para Mac, Linux y Windows instalada o no ha configurado la cuenta de su organización para su uso con la CLI de Azure, consulte [Instalación y configuración de la interfaz de la línea de comandos de Azure](xplat-cli-install.md).

1. Inicie sesión en su cuenta de Azure con sus credenciales. El comando devuelve el resultado del inicio de sesión.

        azure login

        ...
        info:    login command OK

2. Si tiene varias suscripciones, proporcione el identificador de suscripción que desea usar para la implementación.

        azure account set <YourSubscriptionNameOrId>

3. Cambie al módulo de Administrador de recursos de Azure. Recibirá una confirmación del modo nuevo.

        azure config mode arm
        
        info:     New mode is arm

### Vista de roles disponibles
Vea todos los roles disponibles para su suscripción. Devuelve una lista de definiciones de roles.

    azure role list

### Conceda permiso de lectura a un grupo para la suscripción.
1. Obtenga la definición de rol para el rol **Lector**. Compruebe que las acciones permitidas son las que se pretende asignar.

        azure role show Reader
        
        info:    Executing command role show
        + Getting role definitions
        data:    Name    Actions  NotActions
        data:    ------  -------  ----------
        data:    Reader  */read
        info:    role show command OK

2. Obtenga el grupo de seguridad necesario y su identificador de objeto buscando el grupo basado en el nombre. ExampleAuditorGroup se muestra en el ejemplo siguiente.

        azure ad group show --search ExampleAuditorGroup
        
        info:    Executing command ad group show
        + Getting group list
        data:    Display Name:      ExampleAuditorGroup
        data:    ObjectId:          1c272299-9729-462a-8d52-7efe5ece0c5c
        data:    Security Enabled:  true
        data:    Mail Enabled:
        data:
        info:    ad group show command OK

3. Cree la asignación de rol para el grupo de seguridad.

        azure role assignment create --objectId {group-object-id} -o Reader -c /subscriptions/{subscriptionId}/
        
        info:    Executing command role assignment create
        + Getting role definition id
        + Creating role assignment
        info:    role assignment create command OK


### Conceda permisos de colaborador para una aplicación a un grupo de recursos.
1. Obtenga la definición de rol para el rol **Colaborador**. Compruebe que las acciones permitidas son las que se pretende asignar.

        azure role show Contributor

2. Obtenga el identificador de objeto para la aplicación. Cree el nombre de la aplicación que se va a recuperar.

        azure ad sp show --search ExampleApplicationName

2. Cree la asignación de rol para la aplicación.

        azure role assignment create --ObjectId {service-principal-object-id} -o Contributor -c /subscriptions/{subscriptionId}/


###Conceda permisos de propietario a un usuario para un sitio web particular.
1. Obtenga la definición de rol para el rol **Propietario**. Compruebe que las acciones permitidas son las que se pretende asignar.

        azure role show Owner

2. Cree las asignaciones de rol para el usuario.

        azure role assignment create --mail "someone@example.com" -o Owner -c /subscriptions/{subscriptionId}/resourceGroups/{groupName}/providers/Microsoft.Web/sites/{mySiteName}


###Lista de los registros de auditoría del grupo de recursos.
Para obtener el registro de auditoría para un grupo de recursos, ejecute el comando **azure group log show** y proporcione el nombre del grupo de recursos deseados.

         azure group log show ExampleGroupName


## Uso de la API de REST
Para administrar el control de acceso basado en roles a través de la API de REST del Administrador de recursos de Azure, debe establecer encabezados y parámetros comunes (incluidos los tokens de autenticación) al enviar las solicitudes. Para obtener información, consulte [Encabezados y parámetros comunes](https://msdn.microsoft.com/library/azure/dn906885.aspx).
   
Para detectar las versiones de API admitidas, ejecute:

      GET https://management.azure.com/providers/Microsoft.Authorization?api-version=2015-01-01

Puede utilizar la versión `2014-10-01-preview` para este tema.

###Crear una asignación de rol
Obtenga todas las definiciones de rol disponibles.

    GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-10-01-preview

Cree la asignación de rol.

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview
    {
      "properties": {
          "roleDefinitionId": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
          "principalId": "{principal-object-id}",
          "scope": "/subscriptions/{subscription-id}"
       }
    }


###Eliminación de una asignación de rol

      Delete  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview


## Pasos siguientes

- Para obtener más información sobre el control de acceso basado en roles, consulte [Control de acceso basado en roles en el Portal de Microsoft Azure](role-based-access-control-configure.md).
- Para obtener más información sobre cómo trabajar con entidades de servicio para administrar el acceso a las aplicaciones de su suscripción, vea [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](resource-group-authenticate-service-principal.md) y [Creación de una nueva entidad de servicio de Azure mediante el Portal de Azure clásico](../resource-group-create-service-principal-portal.md).
- Para obtener más información sobre las operaciones de auditoría de su organización, consulte [Operaciones de auditoría con el Administrador de recursos](resource-group-audit.md).
- Puede aplicar restricciones y convenciones a través de su suscripción con directivas personalizadas. Para obtener más información, consulte [Uso de directivas para administrar los recursos y controlar el acceso](resource-manager-policy.md).

 

<!----HONumber=AcomDC_1203_2015-->