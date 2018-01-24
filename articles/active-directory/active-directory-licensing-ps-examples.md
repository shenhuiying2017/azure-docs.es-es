---
title: Ejemplos de PowerShell para licencias basadas en grupos de Azure AD | Microsoft Docs
description: Escenarios de PowerShell para licencias basadas en grupos de Azure Active Directory
services: active-directory
keywords: Licencias de Azure AD
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.openlocfilehash: 82d4bdbe60fe403ea07ed958e9aec9dbf4e9fbb8
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="powershell-examples-for-group-based-licensing-in-azure-ad"></a>Ejemplos de PowerShell para licencias basadas en grupos de Azure AD

Toda la funcionalidad para licencias basadas en grupos está disponible en [Azure Portal](https://portal.azure.com) y, actualmente, la compatibilidad con PowerShell es limitada. Sin embargo, hay algunas tareas útiles que se pueden realizar con los [cmdlets existentes de MSOnline PowerShell](https://docs.microsoft.com/powershell/msonline/v1/azureactivedirectory). Este documento proporciona ejemplos de lo que es posible.

> [!NOTE]
> Antes de comenzar la ejecución de cmdlets, ejecute el cmdlet `Connect-MsolService` para conectarse a su inquilino.

## <a name="view-product-licenses-assigned-to-a-group"></a>Visualización de las licencias de producto asignadas a un grupo
El cmdlet [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0) se puede usar para recuperar el objeto de grupo y comprobar la propiedad *Licenses*: enumera todas las licencias de producto actualmente asignadas al grupo.
```
(Get-MsolGroup -ObjectId 99c4216a-56de-42c4-a4ac-e411cd8c7c41).Licenses
| Select SkuPartNumber
```
Salida:
```
SkuPartNumber
-------------
ENTERPRISEPREMIUM
EMSPREMIUM
```

> [!NOTE]
> Los datos se limitan a la información de producto (SKU). No es posible mostrar los planes de servicio deshabilitados en la licencia.

## <a name="get-all-groups-with-licenses"></a>Obtención de todos los grupos con licencias

Puede encontrar todos los grupos con cualquier licencia asignada ejecutando el comando siguiente:
```
Get-MsolGroup | Where {$_.Licenses}
```
Se pueden mostrar más detalles acerca de qué productos se han asignado:
```
Get-MsolGroup | Where {$_.Licenses} | Select `
    ObjectId, `
    DisplayName, `
    @{Name="Licenses";Expression={$_.Licenses | Select -ExpandProperty SkuPartNumber}}
```

Salida:
```
ObjectId                             DisplayName              Licenses
--------                             -----------              --------
7023a314-6148-4d7b-b33f-6c775572879a EMS E5 – Licensed users  EMSPREMIUM
cf41f428-3b45-490b-b69f-a349c8a4c38e PowerBi - Licensed users POWER\_BI\_STANDARD
962f7189-59d9-4a29-983f-556ae56f19a5 O365 E3 - Licensed users ENTERPRISEPACK
c2652d63-9161-439b-b74e-fcd8228a7074 EMSandOffice             {ENTERPRISEPREMIUM,EMSPREMIUM}
```

## <a name="get-statistics-for-groups-with-licenses"></a>Obtención de las estadísticas de grupos con licencias
Puede notificar estadísticas básicas para grupos con licencias. En el ejemplo siguiente se indican el número total de usuarios, el número de usuarios con licencias ya asignadas por el grupo y el número de usuarios para los que no se pudieron asignar licencias en el grupo.

```
#get all groups with licenses
Get-MsolGroup -All | Where {$_.Licenses}  | Foreach {
    $groupId = $_.ObjectId;
    $groupName = $_.DisplayName;
    $groupLicenses = $_.Licenses | Select -ExpandProperty SkuPartNumber
    $totalCount = 0;
    $licenseAssignedCount = 0;
    $licenseErrorCount = 0;

    Get-MsolGroupMember -All -GroupObjectId $groupId |
    #get full info about each user in the group
    Get-MsolUser -ObjectId {$_.ObjectId} |
    Foreach {
        $user = $_;
        $totalCount++

        #check if any licenses are assigned via this group
        if($user.Licenses | ? {$_.GroupsAssigningLicense -ieq $groupId })
        {
            $licenseAssignedCount++
        }
        #check if user has any licenses that failed to be assigned from this group
        if ($user.IndirectLicenseErrors | ? {$_.ReferencedObjectId -ieq $groupId })
        {
            $licenseErrorCount++
        }     
    }

    #aggregate results for this group
    New-Object Object |
                    Add-Member -NotePropertyName GroupName -NotePropertyValue $groupName -PassThru |
                    Add-Member -NotePropertyName GroupId -NotePropertyValue $groupId -PassThru |
                    Add-Member -NotePropertyName GroupLicenses -NotePropertyValue $groupLicenses -PassThru |
                    Add-Member -NotePropertyName TotalUserCount -NotePropertyValue $totalCount -PassThru |
                    Add-Member -NotePropertyName LicensedUserCount -NotePropertyValue $licenseAssignedCount -PassThru |
                    Add-Member -NotePropertyName LicenseErrorCount -NotePropertyValue $licenseErrorCount -PassThru

    } | Format-Table
```


Salida:
```
GroupName         GroupId                              GroupLicenses       TotalUserCount LicensedUserCount LicenseErrorCount
---------         -------                              -------------       -------------- ----------------- -----------------
Dynamics Licen... 9160c903-9f91-4597-8f79-22b6c47eafbf AAD_PREMIUM_P2                   0                 0                 0
O365 E5 - base... 055dcca3-fb75-4398-a1b8-f8c6f4c24e65 ENTERPRISEPREMIUM                2                 2                 0
O365 E5 - extr... 6b14a1fe-c3a9-4786-9ee4-3a2bb54dcb8e ENTERPRISEPREMIUM                3                 3                 0
EMS E5 - all s... 7023a314-6148-4d7b-b33f-6c775572879a EMSPREMIUM                       2                 2                 0
PowerBi - Lice... cf41f428-3b45-490b-b69f-a349c8a4c38e POWER_BI_STANDARD                2                 2                 0
O365 E3 - all ... 962f7189-59d9-4a29-983f-556ae56f19a5 ENTERPRISEPACK                   2                 2                 0
O365 E5 - EXO     102fb8f4-bbe7-462b-83ff-2145e7cdd6ed ENTERPRISEPREMIUM                1                 1                 0
Access to Offi... 11151866-5419-4d93-9141-0603bbf78b42 STANDARDPACK                     4                 3                 1
```

## <a name="get-all-groups-with-license-errors"></a>Obtención de todos los grupos con errores de licencia
Para buscar los grupos que contengan algunos usuarios para los que no se pudieron asignar licencias:
```
Get-MsolGroup -HasLicenseErrorsOnly $true
```
Salida:
```
ObjectId                             DisplayName             GroupType Description
--------                             -----------             --------- -----------
11151866-5419-4d93-9141-0603bbf78b42 Access to Office 365 E1 Security  Users who should have E1 licenses
```
## <a name="get-all-users-with-license-errors-in-a-group"></a>Obtención de todos los usuarios con errores de licencia en un grupo

Si un grupo contiene algunos errores relacionados con licencias, puede enumerar todos los usuarios afectados por dichos errores. Un usuario puede tener también errores de otros grupos. Sin embargo, en este ejemplo, se limitan los resultados solo a los errores relacionados con el grupo en cuestión mediante la comprobación de la propiedad **ReferencedObjectId** de cada entrada **IndirectLicenseError** del usuario.

```
#a sample group with errors
$groupId = '11151866-5419-4d93-9141-0603bbf78b42'

#get all user members of the group
Get-MsolGroupMember -All -GroupObjectId $groupId |
    #get full information about user objects
    Get-MsolUser -ObjectId {$_.ObjectId} |
    #filter out users without license errors and users with licenense errors from other groups
    Where {$_.IndirectLicenseErrors -and $_.IndirectLicenseErrors.ReferencedObjectId -eq $groupId} |
    #display id, name and error detail. Note: we are filtering out license errors from other groups
    Select ObjectId, `
           DisplayName, `
           @{Name="LicenseError";Expression={$_.IndirectLicenseErrors | Where {$_.ReferencedObjectId -eq $groupId} | Select -ExpandProperty Error}}
```

Salida:
```
ObjectId                             DisplayName      License Error
--------                             -----------      ------------
6d325baf-22b7-46fa-a2fc-a2500613ca15 Catherine Gibson MutuallyExclusiveViolation
```
## <a name="get-all-users-with-license-errors-in-the-entire-tenant"></a>Obtención de todos los usuarios con errores de licencia en todo el inquilino

Para enumerar todos los usuarios que tienen errores de licencia de uno o varios grupos, puede utilizarse el siguiente script. Este script mostrará una fila por cada usuario y cada error de licencia, lo que le permite identificar claramente el origen de cada error.

> [!NOTE]
> Este script enumerará todos los usuarios en el inquilino que pudieran no ser óptimos para inquilinos grandes.

```
Get-MsolUser -All | Where {$_.IndirectLicenseErrors } | % {   
    $user = $_;
    $user.IndirectLicenseErrors | % {
            New-Object Object |
                Add-Member -NotePropertyName UserName -NotePropertyValue $user.DisplayName -PassThru |
                Add-Member -NotePropertyName UserId -NotePropertyValue $user.ObjectId -PassThru |
                Add-Member -NotePropertyName GroupId -NotePropertyValue $_.ReferencedObjectId -PassThru |
                Add-Member -NotePropertyName LicenseError -NotePropertyValue $_.Error -PassThru
        }
    }  
```

Salida:

```
UserName         UserId                               GroupId                              LicenseError
--------         ------                               -------                              ------------
Anna Bergman     0d0fd16d-872d-4e87-b0fb-83c610db12bc 7946137d-b00d-4336-975e-b1b81b0666d0 MutuallyExclusiveViolation
Catherine Gibson 6d325baf-22b7-46fa-a2fc-a2500613ca15 f2503e79-0edc-4253-8bed-3e158366466b CountViolation
Catherine Gibson 6d325baf-22b7-46fa-a2fc-a2500613ca15 11151866-5419-4d93-9141-0603bbf78b42 MutuallyExclusiveViolation
Drew Fogarty     f2af28fc-db0b-4909-873d-ddd2ab1fd58c 1ebd5028-6092-41d0-9668-129a3c471332 MutuallyExclusiveViolation
```

Aquí se muestra otra versión del script que busca únicamente en grupos que contienen errores de licencia. Puede resultar más óptimo en escenarios en los que se espera encontrar pocos grupos con problemas.

```
Get-MsolUser -All | Where {$_.IndirectLicenseErrors } | % {   
    $user = $_;
    $user.IndirectLicenseErrors | % {
            New-Object Object |
                Add-Member -NotePropertyName UserName -NotePropertyValue $user.DisplayName -PassThru |
                Add-Member -NotePropertyName UserId -NotePropertyValue $user.ObjectId -PassThru |
                Add-Member -NotePropertyName GroupId -NotePropertyValue $_.ReferencedObjectId -PassThru |
                Add-Member -NotePropertyName LicenseError -NotePropertyValue $_.Error -PassThru
        }
    }
```

## <a name="check-if-user-license-is-assigned-directly-or-inherited-from-a-group"></a>Comprobación de si una licencia de usuario está asignada directamente o heredada de un grupo

Para un objeto de usuario, se puede determinar si un grupo le ha asignado una licencia de producto en particular o si se le ha asignado directamente.

Las dos funciones del ejemplo siguiente pueden utilizarse para analizar el tipo de asignación de un usuario individual:
```
#Returns TRUE if the user has the license assigned directly
function UserHasLicenseAssignedDirectly
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    foreach($license in $user.Licenses)
    {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
            #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
            #This could be a group object or a user object (contrary to what the name suggests)
            #If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past
            if ($license.GroupsAssigningLicense.Count -eq 0)
            {
                return $true
            }

            #If the collection contains the ID of the user object, this means the license is assigned directly
            #Note: the license may also be assigned through one or more groups in addition to being assigned directly
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
            {
                if ($assignmentSource -ieq $user.ObjectId)
                {
                    return $true
                }
            }
            return $false
        }
    }
    return $false
}
#Returns TRUE if the user is inheriting the license from a group
function UserHasLicenseAssignedFromGroup
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    foreach($license in $user.Licenses)
    {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
            #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
            #This could be a group object or a user object (contrary to what the name suggests)
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
            {
                #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                #Note: the license may also be assigned directly in addition to being inherited
                if ($assignmentSource -ine $user.ObjectId)
                {
                    return $true
                }
            }
            return $false
        }
    }
    return $false
}
```

Este script ejecuta esas funciones en cada usuario del inquilino, con el identificador de SKU como entrada; en este ejemplo estamos interesados en la licencia para *Enterprise Mobility + Security*, que en nuestro inquilino se representa con el identificador *contoso:EMS*:
```
#the license SKU we are interested in. use Msol-GetAccountSku to see a list of all identifiers in your tenant
$skuId = "contoso:EMS"

#find all users that have the SKU license assigned
Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
    ObjectId, `
    @{Name="SkuId";Expression={$skuId}}, `
    @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
    @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
```

Salida:
```
ObjectId                             SkuId       AssignedDirectly AssignedFromGroup
--------                             -----       ---------------- -----------------
157870f6-e050-4b3c-ad5e-0f0a377c8f4d contoso:EMS             True             False
1f3174e2-ee9d-49e9-b917-e8d84650f895 contoso:EMS            False              True
240622ac-b9b8-4d50-94e2-dad19a3bf4b5 contoso:EMS             True              True
```

## <a name="remove-direct-licenses-for-users-with-group-licenses"></a>Retirada de licencias directas a usuarios con licencias de grupo
El propósito de este script es quitar las licencias directas no necesarias a los usuarios que heredan la misma licencia de un grupo; por ejemplo, como parte de una [migración de usuarios individuales con licencia a licencias basadas en grupos](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-migration-azure-portal).
> [!NOTE]
> Es importante validar en primer lugar que las licencias directas que va a quitar no habilitan más funcionalidades de servicio que las licencias heredadas. De lo contrario, quitar la licencia directa podría deshabilitar el acceso a servicios y datos a esos usuarios. Actualmente no es posible comprobar a través de PowerShell si los servicios se han habilitado por licencias heredadas o por licencias directas. En el script se especificará el nivel mínimo de servicios que sabemos que se heredan de grupos y se comprobará con él.

```
#BEGIN: Helper functions used by the script

#Returns TRUE if the user has the license assigned directly
function UserHasLicenseAssignedDirectly
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
        #This could be a group object or a user object (contrary to what the name suggests)
        #If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past
        if ($license.GroupsAssigningLicense.Count -eq 0)
        {
            return $true
        }

        #If the collection contains the ID of the user object, this means the license is assigned directly
        #Note: the license may also be assigned through one or more groups in addition to being assigned directly
        foreach ($assignmentSource in $license.GroupsAssigningLicense)
        {
            if ($assignmentSource -ieq $user.ObjectId)
            {
                return $true
            }
        }
        return $false
    }
    return $false
}
#Returns TRUE if the user is inheriting the license from a specific group
function UserHasLicenseAssignedFromThisGroup
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
        #This could be a group object or a user object (contrary to what the name suggests)
        foreach ($assignmentSource in $license.GroupsAssigningLicense)
        {
            #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
            #Note: the license may also be assigned directly in addition to being inherited
            if ($assignmentSource -ieq $groupId)
            {
                return $true
            }
        }
        return $false
    }
    return $false
}

#Returns the license object corresponding to the skuId. Returns NULL if not found
function GetUserLicense
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)
    #we look for the specific license SKU in all licenses assigned to the user
    foreach($license in $user.Licenses)
    {
        if ($license.AccountSkuId -ieq $skuId)
        {
            return $license
        }
    }
    return $null
}

#produces a list of disabled service plan names for a set of plans we want to leave enabled
function GetDisabledPlansForSKU
{
    Param([string]$skuId, [string[]]$enabledPlans)

    $allPlans = Get-MsolAccountSku | where {$_.AccountSkuId -ieq $skuId} | Select -ExpandProperty ServiceStatus | Where {$_.ProvisioningStatus -ine "PendingActivation"} | Select -ExpandProperty ServicePlan | Select -ExpandProperty ServiceName
    $disabledPlans = $allPlans | Where {$enabledPlans -inotcontains $_}

    return $disabledPlans
}

function GetUnexpectedEnabledPlansForUser
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [string[]]$expectedDisabledPlans)

    $license = GetUserLicense $user $skuId

    $extraPlans = @();

    if($license -ne $null)
    {
        $userDisabledPlans = $license.ServiceStatus | where {$_.ProvisioningStatus -ieq "Disabled"} | Select -ExpandProperty ServicePlan | Select -ExpandProperty ServiceName

        $extraPlans = $expectedDisabledPlans | where {$userDisabledPlans -notcontains $_}
    }
    return $extraPlans
}
#END: helper functions

#BEGIN: executing the script
#the group to be processed
$groupId = "48ca647b-7e4d-41e5-aa66-40cab1e19101"

#license to be removed - Office 365 E3
$skuId = "contoso:ENTERPRISEPACK"

#minimum set of service plans we know are inherited from groups - we want to make sure that there aren't any users who have more services enabled
#which could mean that they may lose access after we remove direct licenses
$servicePlansFromGroups = ("EXCHANGE_S_ENTERPRISE", "SHAREPOINTENTERPRISE", "OFFICESUBSCRIPTION")

$expectedDisabledPlans = GetDisabledPlansForSKU $skuId $servicePlansFromGroups

#process all members in the group
Get-MsolGroupMember -All -GroupObjectId $groupId |
    #get full info about each user in the group
    Get-MsolUser -ObjectId {$_.ObjectId} |
    Foreach {
        $user = $_;
        $operationResult = "";

        #check if Direct license exists on the user
        if (UserHasLicenseAssignedDirectly $user $skuId)
        {
            #check if the license is assigned from this group, as expected
            if (UserHasLicenseAssignedFromThisGroup $user $skuId $groupId)
            {
                #check if there are any extra plans we didn't expect - we are being extra careful not to remove unexpected services
                $extraPlans = GetUnexpectedEnabledPlansForUser $user $skuId $expectedDisabledPlans
                if ($extraPlans.Count -gt 0)
                {
                    $operationResult = "User has extra plans that may be lost - license removal was skipped. Extra plans: $extraPlans"
                }
                else
                {
                    #remove the direct license from user
                    Set-MsolUserLicense -ObjectId $user.ObjectId -RemoveLicenses $skuId
                    $operationResult = "Removed direct license from user."   
                }

            }
            else
            {
                $operationResult = "User does not inherit this license from this group. License removal was skipped."
            }
        }
        else
        {
            $operationResult = "User has no direct license to remove. Skipping."
        }

        #format output
        New-Object Object |
                    Add-Member -NotePropertyName UserId -NotePropertyValue $user.ObjectId -PassThru |
                    Add-Member -NotePropertyName OperationResult -NotePropertyValue $operationResult -PassThru
    } | Format-Table
#END: executing the script
```

Salida:
```
UserId                               OperationResult                                                                                
------                               ---------------                                                                                
7c7f860f-700a-462a-826c-f50633931362 Removed direct license from user.                                                              
0ddacdd5-0364-477d-9e4b-07eb6cdbc8ea User has extra plans that may be lost - license removal was skipped. Extra plans: SHAREPOINTWAC
aadbe4da-c4b5-4d84-800a-9400f31d7371 User has no direct license to remove. Skipping.                                                
```

## <a name="next-steps"></a>pasos siguientes

Para más información sobre el conjunto de características de administración de licencias mediante grupos, consulte lo siguiente:

* [¿En qué consisten las licencias basadas en grupos de Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Asignación de licencias a un grupo en Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identificación y resolución de problemas de licencias de un grupo en Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Migración de usuarios individuales con licencia a licencias basadas en grupos en Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Escenarios adicionales de licencias basadas en grupos de Azure Active Directory](active-directory-licensing-group-advanced.md)
