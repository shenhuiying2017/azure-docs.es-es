<properties
	pageTitle="Control de acceso basado en roles en el portal de Microsoft Azure"
	description="Describe la manera en que funciona el control de acceso basado en roles y cómo configurarlo"
	services=""
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="Ibiza"
	ms.workload="infrastructure-services"
	ms.date="08/14/2015"
	ms.author="inhenk"/>

# Control de acceso basado en roles en el portal de Microsoft Azure

Con el fin de que las organizaciones satisfagan sus requisitos de administración de acceso de forma simple y precisa, el Portal de Microsoft Azure es ahora compatible con el control de acceso basado en roles (RBAC). En la [entrada de blog](http://go.microsoft.com/fwlink/?LinkId=511576) se proporciona una introducción rápida a la característica, mientras que en este tema se describen detalladamente los conceptos y se incluyen casos de uso adicionales.


## RBAC en Azure

Cada una de las suscripciones de Azure está asociada a un Azure Active Directory. Los usuarios y los servicios que acceden a los recursos de la suscripción mediante el Portal de administración de Microsoft Azure o la API de Administrador de recursos de Azure deben autenticarse primero con ese Azure Active Directory.

![][1]

El control de acceso basado en roles de Azure permite conceder el acceso apropiado a usuarios, grupos y servicios de Azure AD mediante la asignación de roles en un ámbito de suscripción, grupo de recursos o recurso individual. El rol asignado define el nivel de acceso que los usuarios, grupos o servicios tienen en el recurso de Azure.

### Rol

Un rol es una colección de acciones que se pueden realizar en recursos de Azure. Para que un usuario o un servicio puedan llevar a cabo una acción en un recurso de Azure, deben tener asignado un rol que contenga dicha acción. Para obtener una lista de funciones integradas y **sus** propiedades acciones y **no acciones**, consulte [Roles integrados](#builtinroles).

### Asignación de roles

El acceso se concede a usuarios y servicios de Azure AD mediante la asignación de un rol apropiado en un recurso de Azure.

#### Entidades de seguridad de Azure AD

Los roles se pueden asignar a los siguientes tipos de entidades de seguridad de Azure AD:

+ **Usuarios**: los roles se pueden asignar a usuarios de la organización que están en Azure AD al que está asociada la suscripción de Azure. Los roles también se pueden asignar a usuarios externos que dispongan de una cuenta Microsoft (por ejemplo, joe@outlook.com). Para ello, se debe usar la acción Invitar a fin de asignar al usuario un rol en el Portal de Azure. Al asignar un rol a un usuario externo de una cuenta Microsoft, se crea una cuenta de invitado en Azure AD. Si esta cuenta de invitado se deshabilita en el directorio, el usuario externo no podrá acceder a los recursos de Azure para los que se le haya concedido acceso.
+ **Grupos**: los roles se pueden asignar a grupos de seguridad de Azure AD. Si un usuario pasa a pertenecer a un grupo que tiene acceso, ese usuario dispondrá automáticamente de acceso a un recurso. Del mismo modo, si el usuario se quita del grupo, pierde automáticamente el acceso al recurso. En lugar de asignar roles directamente a los usuarios, lo más recomendable es administrar el acceso por grupos mediante la asignación de roles a grupos y la adición de usuarios a esos grupos. RBAC de Azure no permite la asignación de roles a listas de distribución. La capacidad de asignar roles a grupos permite a una organización extender a la nube su modelo de control de acceso local existente; de esta forma, los grupos de seguridad que ya tienen establecido un control de acceso local se pueden reutilizar para controlar el acceso a los recursos en el Portal de Azure. Para obtener más información sobre las distintas opciones de sincronización de usuarios y grupos de un directorio local, consulte [Integración de directorios](http://technet.microsoft.com/library/jj573653.aspx). Azure AD Premium también ofrece una [característica de administración de grupos delegada](http://msdn.microsoft.com/library/azure/dn641267.aspx) mediante la cual se puede delegar la capacidad de crear y administrar grupos en usuarios que no sean administradores de Azure AD.
+ **Entidades de servicio**: las identidades de servicio se representan como entidades de servicio en el directorio. Se autentican con Azure AD y se comunican entre ellas de forma segura. Para que los servicios tengan acceso a los recursos de Azure, se pueden asignar roles vía el módulo Azure para Windows PowerShell a la entidad de seguridad de servicio de Azure AD que representa a ese servicio.

#### Ámbito de recursos

El acceso no tiene por qué concederse a toda la suscripción. Los roles también se pueden asignar para grupos de recursos y recursos individuales. En el RBAC de Azure, los recursos heredan las asignaciones de roles de sus recursos primarios. Por tanto, si a un usuario, grupo o servicio solo se le concede acceso a uno de los grupos de recursos de una suscripción, únicamente podrá acceder a ese grupo de recursos —y a los recursos que este contenga—, pero no a otros grupos de recursos de la suscripción. Otro ejemplo: un grupo de seguridad se puede agregar al rol de lector para un grupo de recursos y al rol de colaborador para una base de datos que esté dentro de ese grupo de recursos.

![][2]

## Coexistencia de RBAC con coadministradores de suscripciones

El administrador y los coadministradores de suscripciones seguirán teniendo acceso total a los portales de Azure y a las API de administración. En el modelo RBAC, se les asigna el rol de propietario en el nivel de suscripción. Sin embargo, el nuevo modelo RBAC solo es compatible con el Portal de Azure y las API del Administrador de recursos de Azure. Los usuarios y los servicios a los que se les asignan roles RBAC no pueden acceder al Portal de administración de Azure ni a las API de administración de servicios. Los usuarios agregados al rol de propietario de una suscripción en el Portal de Azure no se convierten en coadministradores de la suscripción en el Portal de Azure completo.

Si desea conceder a un usuario acceso a un recurso de Azure que todavía no está disponible para su administración a través del Portal de Azure, agréguelo a los coadministradores de suscripción mediante el Portal de administración de Azure. El Bus de servicio y StorSimple son ejemplos de recursos que actualmente no se pueden administrar mediante RBAC.

## Autorización para administración frente a operaciones de datos

El control de acceso basado en roles solo es compatible con operaciones de administración de los recursos de Azure del Portal de Azure y las API de Administrador de recursos de Azure. No todas las operaciones de nivel de datos para recursos de Azure se pueden autorizar mediante RBAC. Por ejemplo, la creación, la lectura, la actualización o la eliminación de cuentas de almacenamiento se puede controlar mediante RBAC; pero la creación, la lectura, la actualización o la eliminación de blobs o tablas de la cuenta de almacenamiento aún no se puede controlar mediante RBAC. Del mismo modo, la creación, la lectura, la actualización o la eliminación de una base de datos de SQL se puede controlar mediante RBAC; pero la creación, la lectura, la actualización o la eliminación tablas de base de datos de SQL aún no se puede controlar mediante RBAC.

## Adición y eliminación de acceso

Veamos un ejemplo de cómo un propietario de recursos de una organización puede administrar el acceso. En este escenario, hay varias personas trabajando en distintos proyectos de prueba y producción que se compilan mediante recursos de Azure, y se quieren seguir los procedimientos recomendados para la concesión de acceso. Los usuarios deben tener acceso a todos los recursos que necesitan, pero solo a estos. Se desea reaprovechar todas las inversiones realizadas en procesos y herramientas a fin de usar los grupos de seguridad que se controlan en un Active Directory local. Las secciones siguientes describen cómo configurar el acceso a estos recursos:

* [Agregar acceso](#add-access)
* [Quitar acceso](#remove-access)
* [Agregar o quitar acceso para usuario externo](#add-or-remove-access-for-external-user)

### Agregar acceso

Aquí se resumen de los requisitos de acceso y su configuración en Azure.

Usuario/Grupo | Requisito de acceso | rol y ámbito de acceso
------------- | -------------  | ------------
Todo el equipo de Jill Santos | Leer todos los recursos de Azure | Agregar el grupo de AD que representa al equipo de Jill Santos al rol de lector para la suscripción de Azure
Todo el equipo de Jill Santos | Crear y administrar todos los recursos del grupo de recursos Prueba | Agregar el grupo de AD que representa al equipo de Jill Santos al rol de colaborador para el grupo de recursos Prueba
Brock | Crear y administrar todos los recursos del grupo de recursos Prod | Agregar Brock al rol de colaborador para el grupo de recursos Prod


En primer lugar, vamos a agregar el acceso de lectura para todos los recursos de la suscripción. Haga clic en **Examinar > Todo > Suscripciones**.

![][3]

Haga clic en el *nombre de su suscripción* \*\* > Lector > Agregar\*\*. En la lista de usuarios y grupos, seleccione o escriba el nombre del grupo de Active Directory.

![][4]

Después agregue el mismo equipo al rol de colaborador del grupo de recursos Prueba. Haga clic en el grupo de recursos para abrir su hoja de propiedades. En **Roles**, haga clic en **Colaborador > Agregar** y escriba el nombre del equipo.

![][5]

Para agregar Brock al rol de colaborador del grupo de recursos Prod, haga clic en el grupo de recursos, después en **Colaborador > Agregar** y escriba el nombre de Brock.

![][6]

Las asignaciones de roles también se pueden administrar mediante el módulo de Microsoft Azure para Windows PowerShell. En el ejemplo siguiente se muestra cómo agregar la cuenta de Brock mediante el cmdlet New-AzureRoleAssignment en lugar del portal:

	PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Para obtener más información sobre el uso de Windows PowerShell para agregar y quitar acceso, consulte el artículo [Administrar el control de acceso basado en roles con Windows PowerShell](role-based-access-control-powershell.md).

### Quitar acceso

Las asignaciones también se pueden quitar fácilmente. Supongamos que se desea quitar un usuario llamado Brad Adams del rol de lector en un grupo de recursos denominado TestDB. Abra la hoja del grupo de recursos y haga clic en **Lector > Brad Adams > Quitar**.

![][7]

En el ejemplo siguiente se muestra cómo quitar Brad Adams mediante el cmdlet Remove-AzureRoleAssignment:

	PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

### Agregar o quitar acceso para usuario externo

La pestaña **Configurar** de un directorio incluye opciones para controlar el acceso de los usuarios externos. Estas opciones solo se pueden cambiar en la interfaz de usuario (no hay ningún método de Windows PowerShell o API) en el portal de Azure completo y solo puede hacerlo un administrador global de directorio. Para abrir la pestaña **Configurar** del portal de Azure, haga clic en **Active Directory** y luego haga clic en el nombre del directorio.

![][10]

A continuación, puede editar las opciones para controlar el acceso de usuarios externos.

![][8]

De forma predeterminada, los invitados no pueden mostrar el contenido del directorio y, por tanto, no ven a ningún usuario ni grupo en la **Lista de miembros**. Pueden buscar un usuario si escriben la dirección de correo electrónico completa del usuario y luego conceden acceso. El conjunto de restricciones predeterminadas para los usuarios son:

- No pueden enumerar usuarios ni grupos en el directorio.
- Pueden ver información limitada de un usuario si conocen la dirección de correo electrónico del usuario.
- Pueden ver información limitada de un grupo si conocen el nombre del grupo.

La capacidad de los invitados para ver información limitada de un usuario o grupo les permite invitar a otras personas y ver algunos detalles de la gente con la que colaboran.

Pasemos al proceso de agregar acceso para un usuario externo. Agregaremos un usuario externo al mismo rol de lector para el grupo de recursos TestDB con el fin de que el usuario pueda ayudar a depurar un error. Abra la hoja del grupo de recursos, haga clic en **Lector > Agregar > Invitar** y escriba la dirección de correo electrónico del usuario que desee agregar.

![][9]

Cuando se agrega un usuario externo, se crea un invitado en el directorio. En adelante, y del mismo modo que cualquier otro usuario del directorio, ese invitado se puede agregar a un grupo o quitar de un grupo, o se puede agregar o quitar de un rol de forma individual.

También se puede quitar un invitado de un rol siguiendo el mismo procedimiento que con cualquier usuario. Aunque se quite un invitado de un rol en un recurso, el invitado no se quita del directorio.

## Cómo realizar el seguimiento de cambios en las asignaciones de roles

Los cambios realizados en las asignaciones de roles se registran en los [registros de auditoría](http://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/), de forma parecida a como ocurre con otros eventos. Se puede recuperar el registro de cambios de la asignación de rol mediante [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx) o la [API de REST del Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn931927.aspx).

Por ejemplo, para recuperar la lista de cambios de asignación de roles para una suscripción completa, ejecute los siguientes dos cmdlets con Azure PowerShell. El primero de ellos cambia al modo de Administrador de recursos de Azure.

`Switch-AzureMode -name AzureResourceManager`

`Get-AzureSubscriptionIdLog –DetailedOutput -StartTime '06-15-15' -EndTime '06-29-15'`

Los cambios de asignación de rol se capturan en eventos donde ResourceProviderName es `Microsoft.Authorization`. Los detalles reales de la asignación se capturan en el detalle de evento: qué entidad de seguridad se asignó a qué rol y para qué ámbito. Los cambios de asignación de rol aparecen cuando se buscan los registros de auditoría en el portal, pero el portal no muestra los detalles del evento. Para ver detalles de eventos, debe usar Azure PowerShell.

###Detalle de evento

Este es un ejemplo de un detalle de evento para un cambio de asignación de rol:

```
Authorization        :
                       Scope     : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-W
                       estUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authoriza
                       tion/roleAssignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78
                       Action    : Microsoft.Authorization/roleAssignments/write
                       Role      : Subscription Admin
                       Condition :
Caller               : William.Hennum@contoso.com
Claims               :
                       aud            : https://management.core.windows.net/
                       iss            : https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/
                       iat            : 1435333533
                       nbf            : 1435333533
                       exp            : 1435337433
                       ver            : 1.0
                       http://schemas.microsoft.com/identity/claims/tenantid: 72f988bf-86f1-41af-91ab-2d7cd011db47
                       http://schemas.microsoft.com/identity/claims/objectidentifier:
                       dda50086-5e3d-4a4b-b8bc-f54771104d89
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn: William.Hennum@contoso.com
                       puid           : 10030000803CDC0B
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier:
                       MJwntjqWaULfl30NJMiDRVSVCWMX5GzmMNU4oqitDXs
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname: William
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname: Hennum
                       name           : William Hennum
                       http://schemas.microsoft.com/claims/authnmethodsreferences: rsa,wia,mfa
                       _claim_names   : {"groups":"src1"}
                       _claim_sources : {"src1":{"endpoint":"https://graph.windows.net/72f988bf-86f1-41af-91ab-2d7cd011
                       db47/users/dda50086-5e3d-4a4b-b8bc-f54771104d89/getMemberObjects"}}
                       http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name: William.Hennum@contoso.com
                       onprem_sid     : S-1-5-21-1721254763-462695806-1538882281-3175325
                       appid          : c44b4083-3bb0-49c1-b47d-974e53cbdf3c
                       appidacr       : 2
                       http://schemas.microsoft.com/identity/claims/scope: user_impersonation
                       http://schemas.microsoft.com/claims/authnclassreference: 1
CorrelationId        : d724ffd0-31a4-4564-941b-f3a5d32ad8a4
Description          :
EventChannels        : Operation
EventDataId          : ed8e79b6-c7d1-4332-adcf-70d37546c5a6
EventName            : BeginRequest
EventSource          : Administrative
EventTimestamp       : 6/26/2015 3:53:34 PM
HttpRequest          :
                       ClientId        : F7272386-295A-4545-96BD-21F0856A43FE
                       Method          : PUT
                       Url             :
                       ClientIpAddress : 23.99.81.159
Id                   : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/provid
                       ers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authorization/roleAss
                       ignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78/events/ed8e79b6-c7d1-4332-adcf-70d37546c5a6/ticks/
                       635709308140011864
Level                : Informational
OperationId          : d724ffd0-31a4-4564-941b-f3a5d32ad8a4
OperationName        : Microsoft.Authorization/roleAssignments/write
Properties           :
                       requestbody    : {"Id":"531f036a-37ff-40c1-9bb9-aa580ebe7e78","Properties":{"PrincipalId":"dda50
                       086-5e3d-4a4b-b8bc-f54771104d89","RoleDefinitionId":"/subscriptions/ff945b8d-441a-41ef-a9db-7bd5
                       fcc99978/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7"
                       ,"Scope":"/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-Wes
                       tUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes"}}
ResourceGroupName    : Default-Storage-WestUS
ResourceProviderName : Microsoft.Authorization
ResourceId           : /subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/provid
                       ers/Microsoft.ClassicStorage/storageAccounts/authzwaes/providers/Microsoft.Authorization/roleAss
                       ignments/531f036a-37ff-40c1-9bb9-aa580ebe7e78
Status               : Started
SubmissionTimestamp  : 6/26/2015 3:53:50 PM
SubscriptionId       : ff945b8d-441a-41ef-a9db-7bd5fcc99978
SubStatus            :`
```

La información del evento se interpreta de la siguiente forma:

| Campo | Valor | Detalles |
| --- | --- | --- |
| Autor de llamada |	`William.Hennum@contoso.com` | La entidad de seguridad que realizó la asignación de roles. La entidad de seguridad puede ser un usuario, grupo o entidad de servicio.
| HttpRequest: Método | `PUT` | La acción realizada. PUT concede una asignación y DELETE quita una asignación. |
| Propiedades: PrincipalId | `dda50086-5e3d-4a4b-b8bc-f54771104d89` | 	El identificador de objeto de la entidad de seguridad que se asignó al rol. La entidad de seguridad puede ser un usuario, grupo o entidad de servicio. Puede determinar el nombre y el tipo de la entidad de seguridad buscándolo en Azure Active Directory con Azure PowerShell. |
| Propiedades: RoleDefinitionId |	`/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7	` | El rol que se asignó. Puede determinar el nombre para mostrar del rol con Azure PowerShell. |
| Propiedades: Ámbito | `/subscriptions/ff945b8d-441a-41ef-a9db-7bd5fcc99978/resourceGroups/Default-Storage-WestUS/providers/Microsoft.ClassicStorage/storageAccounts/authzwaes` |	El recurso en el que se realizó la asignación de roles. Puede tratarse de un recurso, un grupo de recursos o una suscripción. |

###Fragmentos de código de PowerShell de ejemplo

En primer lugar, este es un código de Azure PowerShell de ejemplo para asignar PrincipalId a un nombre y tipo:

```
# Sample - how to resolve a principal
function Get-PrincipalDetails($principalId)
{
    $principalDetails = "" | select Name, Type
    $user = Get-AzureADUser -ObjectId $principalId
    if ($user) {
        $principalDetails.Name = $user.DisplayName
        $principalDetails.Type = "User"
    } else {
        $group = Get-AzureADGroup -ObjectId $principalId
        if ($group) {
            $principalDetails.Name = $group.DisplayName
            $principalDetails.Type = "Group"
        } else {
            $servicePrincipal = Get-AZureADServicePrincipal -objectId $principalId
            if ($servicePrincipal) {
                $principalDetails.Name = $servicePrincipal.DisplayName
                $principalDetails.Type = "Service Principal"
            }
        }
    }

    $principalDetails
}
```

A continuación, mostramos un código de Azure PowerShell de ejemplo para asignar un ámbito a un nombre de recurso y tipo:

```
# Sample - how to resolve a resource
function Get-ResourceDetails($resourceId)
{
    $resourceDetails = "" | select Name, Type
    $resource = Get-AzureResource -Id $resourceId -OutputObjectFormat New
    if ($resource) {
        if ($resource.ResourceName) {
            $resourceDetails.Name = $resource.ResourceName
            $resourceDetails.Type = "Resource"
        } elseif ($resource.ResourceGroupName) {
            $resourceDetails.Name = $resource.ResourceGroupName
            $resourceDetails.Type = "Resource Group"
        } elseif ($resource.SubscriptionId) {
            $resourceDetails.Name = $resource.SubscriptionId
            $resourceDetails.Type = "Subscription"
        }
    }
    $resourceDetails
}
```
A continuación, aparece un código de Azure PowerShell d de ejemplo para asignar RoleDefinitionId al nombre para mostrar del rol:

```

# Get the name of a role
function Get-AzureRoleDefinitionName($roleDefinitionId)
{
    if (!$Global:_azureRoleDefinitionCache) {
        $Global:_azureRoleDefinitionCache = @{}
        Get-AzureRoleDefinition | % { $Global:_azureRoleDefinitionCache[$_.Id] = $_; }
    }

    if ($Global:_azureRoleDefinitionCache[$roleDefinitionId]) {
        return $Global:_azureRoleDefinitionCache[$roleDefinitionId].Name
    } else {
        return ""
    }
}

```

###Script de Azure PowerShell de ejemplo

En resumen, le presentamos un script de ejemplo que recupera los eventos de asignación de rol para un intervalo de fechas determinado y los muestra en forma de tabla:

```
# Sample - how to resolve a principal
function Get-PrincipalDetails($principalId)
{
    $principalDetails = "" | select Name, Type
    $user = Get-AzureADUser -ObjectId $principalId
    if ($user) {
        $principalDetails.Name = $user.DisplayName
        $principalDetails.Type = "User"
    } else {
        $group = Get-AzureADGroup -ObjectId $principalId
        if ($group) {
            $principalDetails.Name = $group.DisplayName
            $principalDetails.Type = "Group"
        } else {
            $servicePrincipal = Get-AZureADServicePrincipal -objectId $principalId
            if ($servicePrincipal) {
                $principalDetails.Name = $servicePrincipal.DisplayName
                $principalDetails.Type = "Service Principal"
            }
        }
    }

    $principalDetails
}
# Sample - how to resolve a resource
function Get-ResourceDetails($resourceId)
{
    $resourceDetails = "" | select Name, Type
    $resource = Get-AzureResource -Id $resourceId -OutputObjectFormat New
    if ($resource) {
        if ($resource.ResourceName) {
            $resourceDetails.Name = $resource.ResourceName
            $resourceDetails.Type = "Resource"
        } elseif ($resource.ResourceGroupName) {
            $resourceDetails.Name = $resource.ResourceGroupName
            $resourceDetails.Type = "Resource Group"
        } elseif ($resource.SubscriptionId) {
            $resourceDetails.Name = $resource.SubscriptionId
            $resourceDetails.Type = "Subscription"
        }
    }
    $resourceDetails
}
# Get the name of a role
function Get-AzureRoleDefinitionName($roleDefinitionId)
{
    if (!$Global:_azureRoleDefinitionCache) {
        $Global:_azureRoleDefinitionCache = @{}
        Get-AzureRoleDefinition | % { $Global:_azureRoleDefinitionCache[$_.Id] = $_; }
    }

    if ($Global:_azureRoleDefinitionCache[$roleDefinitionId]) {
        return $Global:_azureRoleDefinitionCache[$roleDefinitionId].Name
    } else {
        return ""
    }
}
# Sample - output the list of role assignment events
function Get-AzureRBACAuditLog($startDateTime, $endDateTime)
{
    $log = Get-AzureSubscriptionIdLog -DetailedOutput -StartTime $startDateTime -EndTime $endDateTime
    $log = $log | ? { $_.ResourceProviderName -ieq "Microsoft.Authorization" }
    $startEvents = $log | ? { $_.httpRequest -and $_.Status -ieq "Started" }
    $endEvents = @{}
    $log | ? { $_.httpRequest -and $_.Status -ne "Started" } | % { $endEvents[$_.OperationId] = $_ }

    $startEvents | ? { $endEvents.ContainsKey($_.OperationId) } | % {
        $endEvent = $endEvents[$_.OperationId];
        $out = "" | select Timestamp, Caller, Action, PrincipalId, PrincipalName, PrincipalType, RoleName, Scope, ScopeName, ScopeType, RoleDefinitionId
        $out.Timestamp = $endEvent.EventTimestamp
        $out.Caller = $_.Caller
        if ($_.HttpRequest.Method -ieq "PUT") {
            $out.Action = "Granted"
            if ($_.Properties.Content.ContainsKey("requestbody")) {
                $messageBody = ConvertFrom-Json $_.Properties.Content["requestbody"]
            }
        }
        elseif ($_.HttpRequest.Method -ieq "DELETE") {
            $out.Action = "Revoked"
            if ($endEvent.Properties.Content.ContainsKey("responseBody")) {
                $messageBody = ConvertFrom-Json $endEvent.Properties.Content["responseBody"]
            }
        }

        if ($messageBody) {
            $out.PrincipalId = $messageBody.properties.principalId
            $pd = Get-PrincipalDetails $out.PrincipalId
            $out.PrincipalName = $pd.Name
            $out.PrincipalType = $pd.Type
            $out.RoleName = (Get-AzureRoleDefinitionName $messageBody.properties.roleDefinitionId)
            $out.Scope = $messageBody.properties.Scope
            $rd = Get-ResourceDetails $out.Scope
            $out.ScopeName = $rd.Name
            $out.ScopeType = $rd.Type
            $out.RoleDefinitionId = $messageBody.properties.roleDefinitionId
        }

        $out
    }
}

```

Aquí están los comandos para ejecutar el script

```
$log = Get-AzureRBACAuditLog '2015-06-26' '2015-06-27'

$log | Format-Table
```

## Problemas conocidos del control de acceso basado en roles

Si se encuentra con un problema al usar la característica de control de acceso basado en roles durante la vista previa, consulte el artículo [Solución de problemas de control de acceso basado en roles](role-based-access-control-troubleshooting.md) para obtener información acerca de los problemas conocidos.


## Roles integrados

El control de acceso basado en roles de Azure dispone de tres roles integrados siguientes que se pueden asignar a usuarios, grupos y servicios. La definición de los roles integrados no se puede modificar. En una próxima versión del RBAC de Azure se podrán definir roles personalizados formando un conjunto de acciones elegidas a partir de una lista de acciones disponibles que se pueden realizar en recursos de Azure.

Haga clic en el vínculo correspondiente para ver las propiedades **acciones** y **no acciones** de una definición de rol. La propiedad **acciones** especifica las acciones permitidas en los recursos de Azure. Las cadenas de acciones pueden utilizar caracteres comodín. La propiedad **no acciones** de una definición de rol especifica las acciones que se deben excluir de las acciones permitidas.


Nombre de rol | Descripción
------------- | -------------  
[Colaborador de servicio de administración de API](#api-management-service-contributor) | Permite administrar el servicio Administración de API, pero no el acceso.
[Colaborador de componentes de Application Insights](#application-insights-component-contributor) | Permite administrar los componentes de Application Insights, pero no el acceso a ellos.
[Colaborador de BizTalk](#biztalk-contributor) | Permite administrar los servicios de BizTalk, pero no el acceso a ellos.
[Colaborador de ClearDB MySQL DB](#cleardb-mysql-db-contributor) | Permite administrar las bases de datos ClearDB MySQL, pero no el acceso a ellas.
[Colaborador](#contributor) | Los colaboradores pueden administrar todo excepto el acceso.
[Colaborador de Factoría de datos](#data-factory-contributor) | Permite administrar las factorías de datos, pero no el acceso a ellas.
[Colaborador de cuenta de base de datos de documento](#document-db-account-contributor) | Permite administrar las cuentas de DocumentDB, pero no el acceso a ellas.
[Colaborador de la cuenta de Sistemas inteligentes](#intelligent-systems-account-contributor) | Permite administrar las cuentas de Sistemas inteligentes, pero no el acceso a ellas.
[Colaborador de la cuenta de NewRelic APM](#newrelic-apm-account-contributor) | Permite administrar las aplicaciones y cuentas de administración del rendimiento de la aplicación New Relic, pero no el acceso a ellas.
[Propietario](#owner) | El propietario puede administrar todo, incluido el acceso.
[Lector](#reader) | Los lectores pueden ver todo, pero no realizar cambios.
[Colaborador de la memoria caché de Redis](#redis-cache-contributor) | Permite administrar las memorias caché de Redis, pero no el acceso a ellas.
[Colaborador de Base de datos de SQL](#sql-db-contributor) | Permite administrar las bases de datos SQL, pero no el acceso a ellas. Además, no puede administrar los servidores SQL primarios ni sus directivas relacionadas con la seguridad.
[Administrador de seguridad SQL](#sql-security-manager) | Permite administrar las directivas relacionadas con la seguridad de las bases de datos y los servidores SQL Server, pero no el acceso a ellos.
[Colaborador de SQL Server](#sql-server-contributor) | Permite administrar servidores SQL Server y bases de datos, pero no el acceso a ellos ni sus directivas relacionadas con la seguridad.
[Colaborador de colecciones de trabajos de Scheduler](#scheduler-job-collections-contributor) | Permite administrar las colecciones de trabajos de Scheduler, pero no el acceso a ellas.
[Colaborador del servicio de búsqueda](#search-service-contributor) | Permite administrar los servicios de búsqueda, pero no el acceso a ellos.
[Colaborador de la cuenta de almacenamiento](#storage-account-contributor) | Permite administrar las cuentas de almacenamiento, pero no el acceso a ellas.
[Administrador de acceso de usuario](#user-access-administrator) | Permite administrar el acceso de usuario a los recursos de Azure.
[Colaborador de la máquina virtual](#virtual-machine-contributor) | Permite administrar las máquinas virtuales, pero no el acceso a ellas ni la red virtual o cuenta de almacenamiento a la que están conectadas.
[Colaborador de la red virtual](#virtual-network-contributor) | Permite administrar las redes virtuales, pero no el acceso a ellas.
[Colaborador de plan web](#web-plan-contributor) | Permite administrar los planes web para sitios web, pero no el acceso a ellos.
[Colaborador de sitio web](#website-contributor) | Permite administrar sitios web (no planes de web), pero no el acceso a ellos.


### Colaborador de servicio de administración de API

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>Microsoft.ApiManagement/Services/*</td>
<td>Crear y administrar los servicios de administración de API</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Colaborador de componente de Application Insights

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>Microsoft.Insights/components/*</td>
<td>Crear y administrar componentes de Insights</td>
</tr>
<tr>
<td>Microsoft.Insights/webtests/*</td>
<td>Crear y administrar pruebas web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Colaborador de BizTalk

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>Microsoft.BizTalkServices/BizTalk/*</td>
<td>Crear y administrar los servicios de BizTalk</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Colaborador de ClearDB MySQL DB

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>successbricks.cleardb/databases/*</td>
<td>Crear y administrar bases de datos ClearDB MySQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Colaborador

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>*</td>
<td>Crear y administrar recursos de todos los tipos</td>
</tr>
<tr>
<th colspan="2">No acciones</th>
</tr>
<tr>
<td>Microsoft.Authorization/*/Write</td>
<td>No puede crear roles ni asignaciones de roles </td>
</tr>
<tr>
<td>Microsoft.Authorization/*/Delete</td>
<td>No puede eliminar roles ni asignaciones de roles</td>
</tr>
</table>

### Colaborador de factoría de datos

<table style=width:100%">
<tr>
<td>Microsoft.DataFactory/dataFactories/*</td>
<td>Crear y administrar factorías de datos</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Colaborador de cuenta de base de datos de documento

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>Microsoft.DocumentDb/databaseAccounts/*</td>
<td>Crear y administrar cuentas de DocumentDB</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Colaborador de la cuenta de sistemas inteligentes

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>Microsoft.IntelligentSystems/accounts/*</td>
<td>Crear y administrar cuentas de sistemas inteligentes</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Colaborador de la cuenta de NewRelic APM

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>NewRelic.APM/accounts/*</td>
<td>Crear y administrar cuentas de administración del rendimiento de la aplicación New Relic</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Propietario

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>*</td>
<td>Crear y administrar recursos de todos los tipos</td>
</tr>
</table>

### Lector

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>*/read</td>
<td>Leer recursos de todos los tipos. Aunque no se puede leer la información confidencial.</td>
</tr>
</table>

### Colaborador de la memoria caché de Redis

<table style=width:100%">
<tr>
<td>Microsoft.Cache/redis/*</td>
<td>Crear y administrar memorias caché de Redis</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Colaborador de la base de datos de SQL

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>Leer los servidores SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/*</td>
<td>Crear y administrar bases de datos SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>Leer recursos de suscripción</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>Leer recursos del grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
<tr>
<th colspan="2">No acciones</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>No puede administrar directivas de auditoría de bases de datos SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>No puede administrar directivas de conexión de bases de datos SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>No puede administrar directivas de enmascaramiento de datos de bases de datos SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>No puede administrar las métricas de seguridad de bases de datos SQL</td>
</tr>
</table>

### Administrador de seguridad SQL

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/read</td>
<td>Leer los servidores SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>Crear y administrar directivas de auditoría de SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/read</td>
<td>Leer bases de datos SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>Crear y administrar directivas de auditoría de bases de datos SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>Crear y administrar directivas de conexión de bases de datos SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>Crear y administrar directivas de enmascaramiento de datos de bases de datos SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>Crear y administrar medidas de seguridad de bases de datos SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Colaborador de SQL Server

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/*</td>
<td>Crear y administrar servidores SQL</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resources/read</td>
<td>Leer recursos de suscripción</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/resources/read</td>
<td>Leer recursos del grupo de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
<tr>
<th colspan="2">No acciones</th>
</tr>
<tr>
<td>Microsoft.Sql/servers/auditingPolicies/*</td>
<td>No puede administrar directivas de auditoría de SQL Server</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/auditingPolicies/*</td>
<td>No puede administrar directivas de auditoría de bases de datos SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/connectionPolicies/*</td>
<td>No puede administrar directivas de conexión de bases de datos SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/dataMaskingPolicies/*</td>
<td>No puede administrar directivas de enmascaramiento de datos de bases de datos SQL</td>
</tr>
<tr>
<td>Microsoft.Sql/servers/databases/securityMetrics/*</td>
<td>No puede administrar las métricas de seguridad de bases de datos SQL</td>
</tr>
</table>

### Colaborador de colecciones de trabajos de Scheduler

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>Microsoft.Scheduler/jobcollections/*</td>
<td>Crear y administrar colecciones de trabajos de Scheduler</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Colaborador del servicio de búsqueda

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>Microsoft.Search/searchServices/*</td>
<td>Crear y administrar servicios de búsqueda</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Colaborador de la cuenta de almacenamiento

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/*</td>
<td>Crear y administrar cuentas de almacenamiento</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Administrador de acceso de usuario

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>*/read</td>
<td>Leer recursos de todos los tipos</td>
</tr>
<tr>
<td>Microsoft.Authorization/*</td>
<td>Crear y administrar roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Colaborador de la máquina virtual

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/read</td>
<td>Leer cuentas de almacenamiento</td>
</tr>
<tr>
<td>Microsoft.ClassicStorage/storageAccounts/listKeys/action</td>
<td>Recuperar claves de cuentas de almacenamiento</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/read</td>
<td>Leer redes virtuales</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/join/action</td>
<td>Unirse a redes virtuales</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/read</td>
<td>Leer direcciones IP reservadas</td>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/reservedIps/link/action</td>
<td>Vincularse a direcciones IP reservadas</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/domainNames/*</td>
<td>Crear y administrar servicios en la nube</td>
</tr>
<tr>
<td>Microsoft.ClassicCompute/virtualMachines/*</td>
<td>Crear y administrar máquinas virtuales</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Colaborador de la red virtual

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>Microsoft.ClassicNetwork/virtualNetworks/*</td>
<td>Crear y administrar redes virtuales</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Colaborador de plan web

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/*</td>
<td>Crear y administrar planes web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>

### Colaborador de sitio web

<table style=width:100%">
<tr>
<th colspan="2">Acciones</th>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/read</td>
<td>Leer planes web</td>
</tr>
<tr>
<td>Microsoft.Web/serverFarms/join/action</td>
<td>Unirse a planes web</td>
</tr>
<tr>
<td>Microsoft.Web/sites/*</td>
<td>Crear y administrar sitios web</td>
</tr>
<tr>
<td>Microsoft.Web/certificates/*</td>
<td>Crear y administrar certificados de sitios web</td>
</tr>
<tr>
<td>Microsoft.Authorization/*/read</td>
<td>Leer roles y asignaciones de roles</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/read</td>
<td>Leer grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Resources/subscriptions/resourceGroups/deployments/*</td>
<td>Crear y administrar implementaciones de grupos de recursos</td>
</tr>
<tr>
<td>Microsoft.Insights/alertRules/*</td>
<td>Crear y administrar reglas de alertas</td>
</tr>
<tr>
<td>Microsoft.Support/*</td>
<td>Crear y administrar incidencias de soporte técnico</td>
</tr>
</table>


## Comentarios

Pruebe el RBAC de Azure y envíenos sus [comentarios](http://aka.ms/azurerbacfeedback).


## Pasos siguientes

Los recursos siguientes le servirán de ayuda para el control de acceso basado en rol:

+ [Administrar el control de acceso basado en roles con Windows PowerShell](role-based-access-control-powershell.md)
+ [Administrar el control de acceso basado en roles con CLI de Azure](role-based-access-control-xplat-cli.md)
+ [Solución de problemas de control de acceso basado en roles](role-based-access-control-troubleshooting.md)
+ [Azure Active Directory Premium y Basic](active-directory-editions.md)
+ [Cómo se asocian las suscripciones a Azure con Azure AD](active-directory-how-subscriptions-associated-directory.md)
+ Para una introducción a la administración de grupos de autoservicio para grupos de seguridad, consulte el [blog del equipo de Active Directory](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx)

<!--Image references-->
[1]: ./media/role-based-access-control-configure/RBACSubAuthDir.png
[2]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
[3]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
[4]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
[5]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
[6]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
[7]: ./media/role-based-access-control-configure/RBACRemoveRole.png
[8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
[9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
[10]: ./media/role-based-access-control-configure/RBACDirConfigTab.png

<!---HONumber=August15_HO8-->