<properties pageTitle="Administración del control de acceso basado en rol con Windows PowerShell" metaKeywords="ResourceManager, PowerShell, Azure PowerShell, RBAC" description="Administrar el control de acceso basado en roles con Windows PowerShell" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Windows PowerShell" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="11/03/2014" ms.author="guayan" />

# Administración del control de acceso basado en rol con Windows PowerShell #

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/es-es/documentation/articles/xplat-cli-rbac.md" title="Cross-Platform CLI">CLI multiplataforma</a></div>

El control de acceso basado en roles (RBAC) del portal de vista previa de Azure y la API del administrador de recursos de Azure le permite administrar el acceso a su suscripción en un nivel específico. Con esta característica, puede conceder acceso a usuarios, grupos o entidades de seguridad de servicio de Active Directory asignándoles roles en un ámbito determinado.

En este tutorial, aprenderá a usar Windows PowerShell para administrar RBAC. Le guía por el proceso de crear y comprobar asignaciones de roles.

**Duración aproximada:** 15 minutos

## Requisitos previos ##

Para poder usar Windows PowerShell para administrar RBAC, necesita lo siguiente:

- Windows PowerShell, versión 3.0 o 4.0. Para saber cuál es la versión de Windows PowerShell, escriba: $PSVersionTable y compruebe si el valor de ´PSVersion´ es 3.0 o 4.0. Para instalar una versión compatible, consulte [Windows Management Framework 3.0 ](http://www.microsoft.com/es-es/download/details.aspx?id=34595) o [Windows Management Framework 4.0](http://www.microsoft.com/es-es/download/details.aspx?id=40855).

- La versión 0.8.8 o posterior de Azure PowerShell. Para instalar la última versión y asociarla a la suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](http://www.windowsazure.com/es-es/documentation/articles/install-configure-powershell/).

Este tutorial está diseñado para los principiantes de Windows PowerShell, pero se asume que se conocen los conceptos básicos, como los módulos, los cmdlets y las sesiones. Para obtener más información acerca de Windows PowerShell, consulte [Introducción a Windows PowerShell](http://technet.microsoft.com/es-es/library/hh857337.aspx).

Para obtener ayuda detallada con cualquier cmdlet que aparezca en este tutorial, use el cmdlet Get-Help. 

	Get-Help <cmdlet-name> -Detailed

Por ejemplo, para obtener ayuda para el cmdlet Add-AzureAccount, escriba:

	Get-Help Add-AzureAccount -Detailed

Lea también los siguientes tutoriales para aprender a configurar y usar el Administrador de recursos de Azure en Windows PowerShell:

- [Instalación y configuración de Azure PowerShell](http://azure.microsoft.com/es-es/documentation/articles/install-configure-powershell/)
- [Uso de Windows PowerShell con el Administrador de recursos](http://azure.microsoft.com/es-es/documentation/articles/powershell-azure-resource-manager/)

## Apartados de este tutorial ##

* [Conectarse a sus suscripciones](#connect)
* [Comprobar asignaciones de roles existentes](#check)
* [Crear una asignación de rol](#create)
* [Comprobar los permisos](#verify)
* [Pasos siguientes](#next)

## <a id="connect"></a>Conectarse a sus suscripciones ##

Como RBAC solo funciona con el Administrador de recursos de Azure, lo primero que hay que hacer es pasar al modo Administrador de recursos de Azure. Para ello, escriba:

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Para más información, vea [Uso de Windows PowerShell con el Administradir de recursos](http://azure.microsoft.com/es-es/documentation/articles/powershell-azure-resource-manager/).

Para conectarse a sus suscripciones de Azure, escriba:

    PS C:\> Add-AzureAccount

En el control emergente del explorador, escriba el nombre de usuario y la contraseña de su cuenta de Azure. PowerShell obtendrá todas las suscripciones que tenga con esta cuenta y se configurará para usar la primera como predeterminada. Con RBAC, solo podrá obtener las suscripciones donde tiene permisos por ser administrador adjunto o por tener una asignación de rol. 

Si tiene varias suscripciones y quiere cambiar a otra, escriba:

    # Se mostrarán las suscripciones en la cuenta.
    PS C:\> Get-AzureSubscription
    # Utilice el nombre de la suscripción para seleccionar aquella en la que desea trabajar.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

Para más información, vea [Instalación y configuración de Azure PowerShell](http://azure.microsoft.com/es-es/documentation/articles/install-configure-powershell/).

## <a id="check"></a>Comprobación de asignaciones de rol existentes ##

Ahora vamos a ver qué asignaciones de roles existen en la suscripción. Escriba:

    PS C:\> Get-AzureRoleAssignment

Esto devolverá todas las asignaciones de roles que haya en la suscripción. Cabe destacar dos cosas:

1. Necesitará acceso de lectura en el nivel de la suscripción.
2. Si la suscripción tiene muchas asignaciones de roles, se puede tardar un rato en recuperarlas todas.

También puede comprobar las asignaciones de roles existentes para una definición de rol determinada, en un ámbito concreto y para un cierto usuario. Escriba:

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Esto devolverá toda las asignaciones de roles para un usuario determinado de su inquilino de AD, que tiene una asignación de rol de "Owner" para el grupo de recursos "group1". La asignación de rol puede venir de dos sitios:

1. Una asignación de rol de "Owner" al usuario para el grupo de recursos.
2. Una asignación de rol de "Owner" al usuario para el elemento primario del grupo de recursos (en este caso, la suscripción) porque, si tiene permisos en un nivel determinado, tendrá los mismos permisos en todos los niveles secundarios.

Todos los parámetros de este cmdlet son opcionales. Puede combinarlos para comprobar asignaciones de roles con distintos filtros.

## <a id="create"></a>Creación de una asignación de rol ##

Para crear una asignación de rol, tiene que pensar en lo siguiente:

- A quién le quiere asignar el rol: puede usar los siguientes cmdlets de Azure Active Directory para ver qué usuarios, grupos y entidades de seguridad de servicio tiene en su inquilino de AD.

    `PS C:\> Get-AzureADUser
    PS C:\> Get-AzureADGroup
    PS C:\> Get-AzureADGroupMember
    PS C:\> Get-AzureADServicePrincipal` 

- Qué rol quiere asignar: puede usar el siguiente cmdlet para ver las definiciones de rol admitidas.

    `PS C:\> Get-AzureRoleDefinition`

- Qué ámbito quiere asignar: hay tres niveles de ámbitos.

    - La suscripción actual
    - Un grupo de recursos, para obtener una lista de grupos de recursos, escriba `PS C:\> Get-AzureResourceGroup`.
    - Un recurso, para obtener una lista de recursos, escriba `PS C:\> Get-AzureResource`.

Use luego ´New-AzureRoleAssignment´ para crear una asignación de rol. Por ejemplo:

 - Esto creará una asignación de rol en el nivel de suscripción actual para un usuario como lector.

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Reader`

- Esto creará una asignación de rol en un nivel de grupo de recursos.

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Contributor -ResourceGroupName group1`

- Esto creará una asignación de rol en un nivel de recursos.

    `PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId`

## <a id="verify"></a>Comprobación de los permisos ##

Después de comprobar que su cuenta tiene asignaciones de roles, puede ver los permisos que conceden estas asignaciones de roles ejecutando lo siguiente:

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

Estos dos cmdlets solo devuelven los grupos de recursos o los recursos donde tiene permiso de lectura. También le mostrarán los permisos que tiene.

Si intenta ejecutar otro cmdlet, como `New-AzureResourceGroup`, obtendrá un error de acceso denegado si no tiene el permiso.

## <a id="next"></a>Pasos siguientes ##

Si quiere más información sobre cómo administrar el control de acceso basado en roles con Windows PowerShell y otros temas relacionados:
 
- [Control de acceso basado en roles en Windows Azure](http://azure.microsoft.com/es-es/documentation/articles/role-based-access-control-configure/)
- [Azure Resource Manager Cmdlets](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409): Obtenga información acerca del uso de los cmdlets en el módulo AzureResourceManager.
- [Uso del grupo de recursos para administrar los recursos de Azure](http://azure.microsoft.com/es-es/documentation/articles/azure-preview-portal-using-resource-groups): Obtenga información acerca de la creación y administración de grupos de recursos en el Portal de administración de Azure.
- [Blog sobre Azure](http://blogs.msdn.com/windowsazure) (en inglés): obtenga información acerca de las nuevas características de Azure.
- [Blog sobre Windows PowerShell](http://blogs.msdn.com/powershell) (en inglés): obtenga información acerca de las nuevas características de Windows PowerShell.
- ["Hey, Scripting Guy!" Blog](http://blogs.technet.com/b/heyscriptingguy/): Obtenga sugerencias y trucos del mundo real de la comunidad de Windows PowerShell.
- [Configuración del control de acceso basado en roles usando XPLAT CLI](http://azure.microsoft.com/es-es/documentation/articles/role-based-access-control-xplat-cli/)
- [Solución de problemas del control de acceso basado en roles](http://azure.microsoft.com/es-es/documentation/articles/role-based-access-control-troubleshooting/)
