<properties pageTitle="Managing Role-Based Access Control with Windows PowerShell" metaKeywords="ResourceManager, PowerShell, Azure PowerShell, RBAC" description="Managing role-based access control with Windows PowerShell" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Windows PowerShell" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# Administrar el control de acceso basado en roles con Windows PowerShell

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/es-es/documentation/articles/xplat-cli-rbac.md" title="CLI entre plataformas">CLI entre plataformas</a></div>

El control de acceso basado en roles (RBAC) del portal de vista previa de Azure y la API del administrador de recursos de Azure le permite administrar el acceso a su suscripción en un nivel específico. Con esta característica, puede conceder acceso a usuarios, grupos o entidades de seguridad de servicio de Active Directory asignándoles roles en un ámbito determinado.

En este tutorial, aprenderá a usar Windows PowerShell para administrar RBAC. Le guía por el proceso de crear y comprobar asignaciones de roles.

**Duración aproximada:** 15 minutos

## Requisitos previos

Para poder usar Windows PowerShell para administrar RBAC, necesita lo siguiente:

-   La versión 3.0 o 4.0 de Windows PowerShell. Para ver la versión de Windows PowerShell, escriba:`$PSVersionTable` y compruebe que el valor de `PSVersion` sea 3.0 o 4.0. Para instalar una versión compatible, vea [Windows Management Framework 3.0][Windows Management Framework 3.0] o [Windows Management Framework 4.0][Windows Management Framework 4.0].

-   La versión 0.8.8 o posterior de Azure PowerShell. Para instalar la última versión y asociarla a la suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell].

Este tutorial está diseñado para los principiantes de Windows PowerShell, pero se asume que se conocen los conceptos básicos, como los módulos, los cmdlets y las sesiones. Para obtener más información acerca de Windows PowerShell, consulte [Getting Started with Windows PowerShell][Getting Started with Windows PowerShell].

Para obtener ayuda detallada con cualquier cmdlet que aparezca en este tutorial, use el cmdlet Get-Help.

    Get-Help <cmdlet-name> -Detailed

Por ejemplo, para obtener ayuda para el cmdlet Add-AzureAccount, escriba:

    Get-Help Add-AzureAccount -Detailed

Lea también los siguientes tutoriales para aprender a configurar y usar el Administrador de recursos de Azure en Windows PowerShell:

-   [Instalación y configuración de Azure PowerShell][1]
-   [Uso de Windows PowerShell con el Administrador de recursos][Uso de Windows PowerShell con el Administrador de recursos]

## Apartados de este tutorial

-   [Conectarse a sus suscripciones][Conectarse a sus suscripciones]
-   [Comprobar asignaciones de roles existentes][Comprobar asignaciones de roles existentes]
-   [Crear una asignación de rol][Crear una asignación de rol]
-   [Comprobar los permisos][Comprobar los permisos]
-   [Pasos siguientes][Pasos siguientes]

## <span id="connect"></span></a>Conectarse a sus suscripciones

Como RBAC solo funciona con el Administrador de recursos de Azure, lo primero que hay que hacer es pasar al modo Administrador de recursos de Azure. Para ello, escriba:

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Para más información, vea [Uso de Windows PowerShell con el Administrador de recursos][Uso de Windows PowerShell con el Administrador de recursos].

Para conectarse a sus suscripciones de Azure, escriba:

    PS C:\> Add-AzureAccount

En el control emergente del explorador, escriba el nombre de usuario y la contraseña de su cuenta de Azure. PowerShell obtendrá todas las suscripciones que tenga con esta cuenta y se configurará para usar la primera como predeterminada. Con RBAC, solo podrá obtener las suscripciones donde tiene permisos por ser administrador adjunto o por tener una asignación de rol.

Si tiene varias suscripciones y quiere cambiar a otra, escriba:

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

Para más información, vea [Instalación y configuración de Azure PowerShell][1].

## <span id="check"></span></a>Comprobar asignaciones de roles existentes

Ahora vamos a ver qué asignaciones de roles existen en la suscripción. Tipo:

    PS C:\> Get-AzureRoleAssignment

Esto devolverá todas las asignaciones de roles que haya en la suscripción. Cabe destacar dos cosas:

1.  Necesitará acceso de lectura en el nivel de la suscripción.
2.  Si la suscripción tiene muchas asignaciones de roles, se puede tardar un rato en recuperarlas todas.

También puede comprobar las asignaciones de roles existentes para una definición de rol determinada, en un ámbito concreto y para un cierto usuario. Tipo:

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Esto devolverá toda las asignaciones de roles para un usuario determinado de su inquilino de AD, que tiene una asignación de rol de "Owner" para el grupo de recursos "group1". La asignación de rol puede venir de dos sitios:

1.  Una asignación de rol de "Owner" al usuario para el grupo de recursos.
2.  Una asignación de rol de "Owner" al usuario para el elemento primario del grupo de recursos (en este caso, la suscripción) porque, si tiene permisos en un nivel determinado, tendrá los mismos permisos en todos los niveles secundarios.

Todos los parámetros de este cmdlet son opcionales. Puede combinarlos para comprobar asignaciones de roles con distintos filtros.

## <span id="create"></span></a>Crear una asignación de rol

Para crear una asignación de rol, tiene que pensar en lo siguiente:

-   A quién le quiere asignar el rol: puede usar los siguientes cmdlets de Azure Active Directory para ver qué usuarios, grupos y entidades de seguridad de servicio tiene en su inquilino de AD.

    `PS C:\> Get-AzureADUser
    PS C:\> Get-AzureADGroup
    PS C:\> Get-AzureADGroupMember
    PS C:\> Get-AzureADServicePrincipal` 

-   Qué rol quiere asignar: puede usar el siguiente cmdlet para ver las definiciones de rol admitidas.

    `PS C:\> Get-AzureRoleDefinition`

-   Qué ámbito quiere asignar; hay tres niveles de ámbitos:

    -   La suscripción actual
    -   Un grupo de recursos; para obtener una lista de grupos de recursos, escriba `PS C:\> Get-AzureResourceGroup`
    -   Un recurso; para obtener una lista de recursos, escriba `PS C:\> Get-AzureResource`

Luego use `New-AzureRoleAssignment` para crear una asignación de rol. Por ejemplo:

-   Esto creará una asignación de rol en el nivel de suscripción actual para un usuario como lector.

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Reader`

-   Esto creará una asignación de rol en un nivel de grupo de recursos.

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Contributor -ResourceGroupName group1`

-   Esto creará una asignación de rol en un nivel de recursos.

    `PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId`

## <span id="verify"></span></a>Comprobar los permisos

Después de comprobar que su cuenta tiene asignaciones de roles, puede ver los permisos que conceden estas asignaciones de roles ejecutando lo siguiente:

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

Estos dos cmdlets solo devuelven los grupos de recursos o los recursos donde tiene permiso de lectura. También le mostrarán los permisos que tiene.

Si intenta ejecutar otro cmdlet, como `New-AzureResourceGroup`, obtendrá un error de acceso denegado si no tiene el permiso.

## <span id="next"></span></a>Pasos siguientes

Si quiere más información sobre cómo administrar el control de acceso basado en roles con Windows PowerShell y otros temas relacionados:

-   [Control de acceso basado en roles en Windows Azure][Control de acceso basado en roles en Windows Azure]
-   [Azure Resource Manager Cmdlets][Azure Resource Manager Cmdlets]: aprenda a usar los cmdlets del módulo AzureResourceManager.
-   [Uso del grupo de recursos para administrar los recursos de Azure][Uso del grupo de recursos para administrar los recursos de Azure]: aprenda a crear y administrar grupos de recursos en el portal de administración de Azure.
-   [Blog sobre Azure][Blog sobre Azure] (en inglés): obtenga información acerca de las nuevas características de Azure.
-   [Blog sobre Windows PowerShell][Blog sobre Windows PowerShell] (en inglés): obtenga información acerca de las nuevas características de Windows PowerShell.
-   ["Hey, Scripting Guy!" Blog]["Hey, Scripting Guy!" Blog]: lea sugerencias y trucos del mundo real en la comunidad de Windows PowerShell.
-   [Configuración del control de acceso basado en roles usando XPLAT CLI][Configuración del control de acceso basado en roles usando XPLAT CLI]
-   [Solución de problemas del control de acceso basado en roles][Solución de problemas del control de acceso basado en roles]

  [Windows PowerShell]: /es-es/documentation/articles/powershell-rbac.md "Windows PowerShell"
  [CLI entre plataformas]: /es-es/documentation/articles/xplat-cli-rbac.md "CLI entre plataformas"
  [Windows Management Framework 3.0]: http://www.microsoft.com/es-es/download/details.aspx?id=34595
  [Windows Management Framework 4.0]: http://www.microsoft.com/es-es/download/details.aspx?id=40855
  [Instalación y configuración de Azure PowerShell]: http://www.windowsazure.com/es-es/documentation/articles/install-configure-powershell/
  [Getting Started with Windows PowerShell]: http://technet.microsoft.com/es-es/library/hh857337.aspx
  [1]: http://azure.microsoft.com/es-es/documentation/articles/install-configure-powershell/
  [Uso de Windows PowerShell con el Administrador de recursos]: http://azure.microsoft.com/es-es/documentation/articles/powershell-azure-resource-manager/
  [Conectarse a sus suscripciones]: #connect
  [Comprobar asignaciones de roles existentes]: #check
  [Crear una asignación de rol]: #create
  [Comprobar los permisos]: #verify
  [Pasos siguientes]: #next
  [Control de acceso basado en roles en Windows Azure]: http://azure.microsoft.com/es-es/documentation/articles/role-based-access-control-configure/
  [Azure Resource Manager Cmdlets]: http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409
  [Uso del grupo de recursos para administrar los recursos de Azure]: http://azure.microsoft.com/es-es/documentation/articles/azure-preview-portal-using-resource-groups
  [Blog sobre Azure]: http://blogs.msdn.com/windowsazure
  [Blog sobre Windows PowerShell]: http://blogs.msdn.com/powershell
  ["Hey, Scripting Guy!" Blog]: http://blogs.technet.com/b/heyscriptingguy/
  [Configuración del control de acceso basado en roles usando XPLAT CLI]: http://azure.microsoft.com/es-es/documentation/articles/role-based-access-control-xplat-cli/
  [Solución de problemas del control de acceso basado en roles]: http://azure.microsoft.com/es-es/documentation/articles/role-based-access-control-troubleshooting/
