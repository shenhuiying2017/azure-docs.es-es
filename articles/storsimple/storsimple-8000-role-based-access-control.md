---
title: Uso del control de acceso basado en rol en StorSimple | Microsoft Docs
description: "Describe cómo usar el control de acceso basado en rol de Azure (RBAC) en el contexto de StorSimple."
services: storsimple
documentationcenter: 
author: alkohli
manager: jconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: d040849360a47c611d44b3a5d7649c685dcc8068
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2017
---
# <a name="role-based-access-control-for-storsimple"></a>Control de acceso basado en rol en StorSimple

En este artículo se proporciona una breve descripción de cómo se puede usar el control de acceso basado en rol (RBAC) de Azure en un dispositivo StorSimple. RBAC ofrece administración de acceso específico para Azure. Use RBAC para conceder la cantidad justa de acceso a los usuarios de StorSimple para realizar su trabajo, en lugar de darles a todos acceso sin restricciones. Para más información sobre los conceptos básicos de administración de acceso en Azure, consulte [Introducción al control de acceso basado en roles en Azure Portal](../active-directory/role-based-access-control-what-is.md).

Este artículo se aplica solo a los dispositivos de la serie StorSimple 8000 que ejecutan Update 3.0 o posterior en Azure Portal.

## <a name="rbac-roles-for-storsimple"></a>Roles de RBAC en StorSimple

RBAC puede asignarse en función de los roles. Los roles garantizan determinados niveles de permiso en función de los recursos disponibles en el entorno. Existen dos tipos de roles que pueden elegir los usuarios de StorSimple: integrado o personalizado.

* **Roles integrados**: los roles integrados pueden ser propietario, colaborador, lector o administrador de acceso de usuario. Para más información, consulte [Roles integrados para el control de acceso basado en rol de Azure](../active-directory/role-based-access-control-what-is.md#built-in-roles).

* **Roles personalizados**: si los roles integrados no sirven para sus necesidades, puede crear roles personalizados de RBAC para StorSimple. Para crear un rol personalizado de RBAC, comience con un rol integrado, edítelo y, a continuación, impórtelo de nuevo en el entorno. La descarga y la carga del rol se administran mediante Azure PowerShell o la CLI de Azure. Para más información, consulte [Creación de roles personalizados para el control de acceso basado en roles de Azure](../active-directory/role-based-access-control-custom-roles.md).

Para ver los distintos roles disponibles para usuarios de dispositivos StorSimple en Azure Portal, vaya a su servicio de administrador de dispositivos de StorSimple y, a continuación, vaya a **Control de acceso (IAM) > Roles**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Creación de un rol personalizado para el administrador de la infraestructura de StorSimple

En el ejemplo siguiente, empezaremos con el rol integrado **lector** que permite a los usuarios ver todos los ámbitos de recursos, pero no editarlos ni crear otros nuevos. A continuación, extenderemos este rol para crear un nuevo rol personalizado de administrador de infraestructura de StorSimple. Este rol se asigna a los usuarios que pueden administrar la infraestructura de los dispositivos StorSimple.

1. Ejecute Windows PowerShell como administrador.

2. Inicie sesión en Azure.

    `Login-AzureRMAccount`

3. Exporte el rol de lector como una plantilla JSON en el equipo.

    ```
    Get-AzureRMRoleDefinition -Name "Reader"

    Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Abra el archivo JSON en Visual Studio. Observará que un rol de RBAC consta normalmente de tres secciones principales: **Actions**, **NotActions** y **AssignableScopes**.

    En la sección **Action** se enumeran todas las operaciones permitidas para este rol. Cada acción se asigna desde un proveedor de recursos. En una infraestructura de administración de StorSimple, use el proveedor de recursos `Microsoft.StorSimple`.

    Use PowerShell para ver todos los proveedores de recursos disponibles y registrados en su suscripción.

    `Get-AzureRMResourceProvider`

    También puede comprobar todos los cmdlets de PowerShell disponibles para administrar los proveedores de recursos.

    En las secciones **NotActions**, se enumeran todas las acciones restringidas para un rol determinado de RBAC. En este ejemplo, ninguna acción está restringida.
    
    En la sección **AssignableScopes**, se enumeran los identificadores de la suscripción. Asegúrese de que el rol de RBAC contenga los identificadores de suscripción explícitos donde se utilice. Si no se especifica el identificador de suscripción correcto, no se le permitirá importar el rol en su suscripción.

    Edite el archivo teniendo en cuenta las consideraciones anteriores.

    ```
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Importe de nuevo el rol personalizado de RBAC en el entorno.

    `New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Este rol debe aparecer ahora en la lista de roles en la hoja **Control de acceso**.

![Visualización de los roles de RBAC](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Para más información, vaya a [Creación de un rol personalizado de RBAC con PowerShell](../active-directory/role-based-access-control-create-custom-roles-for-internal-external-users.md#create-a-custom-rbac-role-to-open-support-requests-using-powershell).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Salida de ejemplo de la creación de roles personalizados mediante PowerShell

```
PS C:\WINDOWS\system32> Login-AzureRMAccount

Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :

PS C:\WINDOWS\system32> Get-AzureRMRoleDefinition -Name "Reader"

Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}

PS C:\WINDOWS\system32> Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json

PS C:\WINDOWS\system32> New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"

Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}

PS C:\WINDOWS\system32>
```

## <a name="add-users-to-the-custom-role"></a>Adición de usuarios al rol personalizado

Puede conceder acceso desde el recurso, el grupo de recursos o la suscripción que constituya el ámbito de la asignación de roles. Al proporcionar acceso, tenga en cuenta que el acceso concedido en el nodo primario lo hereda el elemento secundario. Para más información, vaya a [Jerarquía de recursos de Azure y herencia de acceso](../active-directory/role-based-access-control-what-is.md#resource-hierarchy-and-access-inheritance).

1. Vaya a **Control de acceso (IAM)**. Haga clic en **Agregar** en la hoja Control de acceso.

    ![Adición de acceso al rol de RBAC](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Seleccione el rol que quiere asignar; en este caso es el **administrador de infraestructura de StorSimple**.

3. Seleccione el usuario, el grupo o la aplicación en el directorio al que desea conceder acceso. Puede buscar en el directorio con los nombres para mostrar, direcciones de correo electrónico e identificadores de objeto.

4. Seleccione **Guardar** para crear la asignación.

    ![Adición de permisos al rol de RBAC](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

Una notificación **Agregando usuario** realiza un seguimiento del progreso. Después de que el usuario se ha agregado correctamente, se actualiza la lista de usuarios en el control de acceso.

## <a name="view-permissions-for-the-custom-role"></a>Visualización de los permisos del rol personalizado

Una vez creado este rol, puede ver los permisos que tiene asociados en Azure Portal.

1. Para ver los permisos asociados a este rol, vaya a **Control de acceso (IAM) > Roles > Administrador de infraestructura de StorSimple**. Se muestra la lista de usuarios con este rol.

2. Seleccione un usuario administrador de infraestructura de StorSimple y haga clic en **Permisos**.

    ![Visualización de los permisos del rol de administrador de infraestructura de StorSimple](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Se muestran los permisos asociados a este rol.

    ![Visualización de los usuarios con el rol de administrador de infraestructura de StorSimple](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Pasos siguientes

Aprenda a [asignar roles personalizados para usuarios internos y externos](../active-directory/role-based-access-control-create-custom-roles-for-internal-external-users.md).

