<properties
   pageTitle="Administración y auditoría del acceso a los recursos"
   description="Utilice el control de acceso basado en roles (RBAC) para administrar permisos de usuario para los recursos implementados en Azure."
   services="azure-portal"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-portal"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="06/18/2015"
   ms.author="tomfitz"/>

# Administración y auditoría del acceso a los recursos

Con Administrador de recursos de Azure, puede asegurarse de que los usuarios de su organización tendrán los permisos adecuados para administrar o acceder a los recursos. Administrador de recursos aprovecha las ventajas del control de acceso basado en roles (RBAC) para que pueda aplicar fácilmente las directivas de seguridad a recursos individuales o a grupos de recursos. Por ejemplo, puede conceder a un usuario acceso a una máquina virtual específica en una suscripción o conceder a un usuario la capacidad de administrar todos los sitios web de una suscripción, pero ningún otro recurso.

## Conceptos

Hay unos cuantos conceptos claves acerca del control de acceso basado en roles que conviene entender:

1. Entidad de seguridad: la entidad que concede el permiso, por ejemplo, un usuario, un grupo de seguridad o aplicación.
2. Rol: conjunto de acciones de permitidas
3. Ámbito: el nivel al que se aplica un rol, como la suscripción, grupo de recursos o recurso.
3. Asignación de rol: el proceso de agregar una entidad de seguridad a un rol y establecer el ámbito.

## Ejemplos de rol
Para comprender los conceptos RBAC, echemos un vistazo a algunos ejemplos de definiciones de roles comunes:

| Rol | Acciones permitidas |
| ------- | ----------------- |
| Lector | */lectura (leer todo) | | Propietario | * (Leer/escribir todo) |

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
Si no tiene instalada la última versión de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md). Abra la consola de PowerShell de Azure.

1. Inicie sesión en su cuenta de Azure con sus credenciales. El comando devuelve información acerca de su cuenta.

        PS C:\> Add-AzureAccount
          
        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

2. Si tiene varias suscripciones, proporcione el identificador de suscripción que desea usar para la implementación.

        PS C:\> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Cambie al módulo Administrador de recursos de Azure.

        PS C:\> Switch-AzureMode AzureResourceManager

### Vista de roles disponibles
Para ver las funciones disponibles para la suscripción, ejecute el comando **AzureRoleDefinition Get**.

    PS C:\> Get-AzureRoleDefinition

    Name                          Id                            Actions                  NotActions
    ----                          --                            -------                  ----------
    API Management Service Con... /subscriptions/####... {Microsoft.ApiManagement/S...   {}
    Application Insights Compo... /subscriptions/####... {Microsoft.Insights/compon...   {}
    ...

### Conceda permiso de lectura a un grupo para la suscripción.
1. Revise la definición del rol **Lector** al proporcionar el nombre del rol cuando se ejecuta el comando **Get-AzureRoleDefinition**. Compruebe que las acciones permitidas son las que se pretende asignar.

        PS C:\> Get-AzureRoleDefinition Reader
   
        Name            Id                            Actions           NotActions
        ----            --                            -------           ----------
        Reader          /subscriptions/####...        {*/read}          {}

2. Obtiene el grupo de seguridad necesario mediante la ejecución del comando **Get-AzureADGroup**. Proporcione el nombre real del grupo en su suscripción. ExampleAuditorGroup se muestra a continuación.

        PS C:\> $group = Get-AzureAdGroup -SearchString ExampleAuditorGroup

3. Cree la asignación de roles para el grupo de seguridad de auditor. Cuando se complete el comando, se devuelve la nueva asignación de roles.

        PS C:\> New-AzureRoleAssignment -ObjectId $group.Id -Scope /subscriptions/{subscriptionId}/ -RoleDefinitionName Reader

        Mail               :
        RoleAssignmentId   : /subscriptions/####/providers/Microsoft.Authorization/roleAssignments/####
        DisplayName        : Auditors
        RoleDefinitionName : Reader
        Actions            : {*/read}
        NotActions         : {}
        Scope              : /subscriptions/####
        ObjectId           : ####

###Conceda permisos de colaborador para una aplicación a un grupo de recursos.
1. Revise la definición del rol **Colaborador** al proporcionar el nombre del rol cuando se ejecuta el comando **Get-AzureRoleDefinition**. Compruebe que las acciones permitidas son las que se pretende asignar.

        PS C:\> Get-AzureRoleDefinition Contributor

2. Obtenga el identificador del objeto de la entidad de servicio mediante la ejecución del comando **Get-AzureADServicePrincipal** y el suministro del nombre de la aplicación en su suscripción. ExampleApplication se muestra a continuación.

        PS C:\> $service = Get-AzureADServicePrincipal -SearchString ExampleApplicationName

3. Cree las asignaciones de roles para la entidad de servicio mediante la ejecución del comando **New-AzureRoleAssignment**.

        PS C:\> New-AzureRoleAssignment -ObjectId $service.Id -ResourceGroupName ExampleGroupName -RoleDefinitionName Contributor

Para obtener una explicación más completa de la configuración de una aplicación de Azure Active Directory y de una entidad de servicio, consulte [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](../resource-group-authenticate-service-principal.md).

###Conceda permisos de propietario a un usuario para un recurso.
1. Revise la definición del rol **Propietario** al proporcionar el nombre del rol cuando se ejecuta el comando **Get-AzureRoleDefinition**. Compruebe que las acciones permitidas son las que se pretende asignar.

        PS C:\> Get-AzureRoleDefinition Owner

2. Cree las asignaciones de roles para el usuario.

        PS C:\> New-AzureRoleAssignment -UserPrincipalName "someone@example.com" -ResourceGroupName {groupName} -ResourceType "Microsoft.Web/sites" -ResourceName "mysite" -RoleDefinitionName Owner


###Lista de los registros de auditoría del grupo de recursos.
Para obtener el registro de auditoría para un grupo de recursos, ejecute el comando **Get-AzureResourceGroupLog**.

      PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleGroupName

## Uso de la CLI de Azure para Mac, Linux y Windows

Si no tiene la CLI de Azure para Mac, Linux y Windows instalada o no ha configurado la cuenta de su organización para su uso con la CLI de Azure, consulte [Instalación y configuración de la interfaz de la línea de comandos de Azure](../xplat-cli-install.md).

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

- [Control de acceso basado en roles en el portal de Microsoft Azure](../role-based-access-control-configure.md)
- [Creación de una nueva entidad de servicio de Azure mediante el portal clásico de Azure](../resource-group-create-service-principal-portal.md)
- [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](../resource-group-authenticate-service-principal.md)

 

<!---HONumber=62-->