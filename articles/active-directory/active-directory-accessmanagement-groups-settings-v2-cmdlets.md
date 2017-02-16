---
title: "Cmdlets de la versión preliminar de PowerShell de Azure Active Directory para la administración de grupos en Azure AD | Microsoft Docs"
description: "En esta página se proporcionan ejemplos de PowerShell para ayudarlo a administrar grupos de Azure Active Directory."
keywords: "Azure AD, Azure Active Directory, PowerShell, grupos, administración de grupos"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7a5023dc-2727-4c25-8254-b531fc3244ac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 4f12fdf6732d44903fb1e971f05d0233d7aff08e


---
# <a name="azure-active-directory-preview-cmdlets-for-group-management"></a>Cmdlets del módulo de vista previa de Azure Active Directory para administrar grupos
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-groups-create-azure-portal.md)
> * [Portal de Azure clásico](active-directory-accessmanagement-manage-groups.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

En el siguiente documento se proporcionan ejemplos de cómo usar PowerShell para administrar grupos en Azure Active Directory (Azure AD).  También se ofrece información sobre cómo configurar el módulo de vista previa de Azure AD PowerShell. En primer lugar, debe [descargar el módulo de Azure AD PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=828627).

## <a name="installing-the-azure-ad-powershell-module"></a>Instalación del módulo de Azure AD PowerShell
Para instalar el módulo de vista previa de Azure AD PowerShell, use los siguientes comandos:

    PS C:\Windows\system32> install-module azureadpreview

Para comprobar que se ha instalado el módulo de vista previa, ejecute el siguiente comando:

    PS C:\Windows\system32> get-module azureadpreview

    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     1.1.146.0  azureadpreview                      {Add-AzureADAdministrati...}

Ahora puede empezar a usar los cmdlets del módulo. Para ver una descripción completa de los cmdlets del módulo de vista previa de Azure AD, consulte la [documentación de referencia en línea](https://msdn.microsoft.com/library/azure/mt757216.aspx).

## <a name="connecting-to-the-directory"></a>Conexión al directorio
Antes de empezar a administrar los grupos mediante los cmdlets de vista previa de Azure AD PowerShell, debe conectar la sesión de PowerShell al directorio que quiera administrar. Para ello, ejecute el siguiente comando:

    PS C:\Windows\system32> Connect-AzureAD

El cmdlet le pedirá las credenciales que quiera utilizar para acceder al directorio. En este ejemplo, vamos a utilizar karen@drumkit.onmicrosoft.com para tener acceso al directorio de demostración. El cmdlet devolverá una confirmación para mostrar que la sesión se ha conectado correctamente al directorio:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Ahora puede empezar a usar los cmdlets de vista previa de Azure AD para administrar grupos en el directorio.

## <a name="retrieving-groups"></a>Recuperación de grupos
Para recuperar grupos que ya se encuentren en el directorio, puede usar el cmdlet Get-AzureADGroups. Para recuperar todos los grupos del directorio, ejecute el cmdlet sin parámetros:

    PS C:\Windows\system32> get-azureadgroup

El cmdlet devolverá todos los grupos del directorio conectado.

Puede usar el parámetro -objectID si quiere recuperar un grupo específico para el que especifica el id. de objeto del grupo:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

El cmdlet devolverá ahora el grupo cuyo id. de objeto coincida con el valor del parámetro especificado:

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Puede buscar un grupo específico mediante el parámetro - filter. Este parámetro toma una cláusula de filtro ODATA y devuelve todos los grupos que coinciden con él, como en el ejemplo siguiente:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Tenga en cuenta que los cmdlets de Azure AD PowerShell implementan el estándar de consulta OData. Puede obtener más información [aquí](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="creating-groups"></a>Creación de grupos
Para crear un nuevo grupo en el directorio, use el cmdlet New-AzureADGroup. Este cmdlet crea un nuevo grupo de seguridad llamado "Marketing":

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="updating-groups"></a>Actualización de grupos
Para actualizar un grupo que ya exista, utilice el cmdlet Set-AzureADGroup. En este ejemplo, vamos a cambiar la propiedad DisplayName del grupo "Administradores de Intune". En primer lugar, buscaremos el grupo mediante el cmdlet Get-AzureADGroup y aplicaremos un filtro usando el atributo DisplayName:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Después, vamos a cambiar la propiedad Description del nuevo valor de "Administradores de dispositivos de Intune":

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Ahora, si encontramos el grupo nuevo, veremos que la propiedad Description se actualiza para reflejar el nuevo valor:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="deleting-groups"></a>Eliminación de grupos
Para eliminar grupos del directorio, use el cmdlet Remove-AzureADGroup de la siguiente manera:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="managing-members-of-groups"></a>Administración de miembros de grupos
Si necesita agregar nuevos miembros a un grupo, utilice el cmdlet Add-AzureADGroupMember. Este comando agrega un miembro al grupo de administradores de Intune que utilizamos en el ejemplo anterior:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

El parámetro -ObjectId es el id. de objeto del grupo al que desea agregar un miembro y RefObjectId es el del usuario que desea agregar como miembro al grupo.

Para obtener los miembros existentes de un grupo, use el cmdlet Get-AzureADGroupMember, como en este ejemplo:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User

Para quitar al miembro que agregamos anteriormente al grupo, use el cmdlet Remove-AzureADGroupMember, como se muestra aquí:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Para comprobar si un usuario pertenece a uno o varios grupos, use el cmdlet Select-AzureADGroupIdsUserIsMemberOf. Este cmdlet toma como parámetros el id. de objeto del usuario que se comprueba si pertenece a uno o varios grupos, así como una lista de grupos para realizar dicha comprobación. La lista de grupos debe proporcionarse como una variable compleja del tipo Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck, así que, antes, debemos creamos una variable con ese tipo:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Después, proporcionamos los valores de los id. de grupo para comprobar el atributo GroupIds de esta variable compleja:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Ahora, si queremos comprobar si un usuario con el id. de objeto 72cd4bbd-2594-40a2-935c-016f3cfeeeea pertenece a uno de los grupos de $g, debemos usar lo siguiente:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


El valor devuelto es una lista con los grupos de los que es miembro este usuario. También puede aplicar este método para comprobar si determinados contactos, grupos o entidades de seguridad pertenecen a los grupos de una lista concreta. Para ello, utilice Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf o Select-AzureADGroupIdsServicePrincipalIsMemberOf.

## <a name="managing-owners-of-groups"></a>Administración de propietarios de grupos
Para agregar propietarios a un grupo, utilice el cmdlet Add-AzureADGroupOwner:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

El parámetro -ObjectId es el id. de objeto del grupo al que desea agregar un propietario y RefObjectId es el del usuario que desea agregar como propietario al grupo.

Para recuperar los propietarios de un grupo, utilice Get-AzureADGroupOwner:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

El cmdlet devolverá la lista de propietarios del grupo especificado:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Si desea quitar un propietario de un grupo, utilice Remove-AzureADGroupOwner:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="next-steps"></a>Pasos siguientes
Puede encontrar más documentación de Azure Active Directory PowerShell en el artículo sobre los [cmdlets de Azure Active Directory](http://go.microsoft.com/fwlink/p/?LinkId=808260).

* [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)



<!--HONumber=Dec16_HO4-->


