<properties 
	pageTitle="Administración del control de acceso basado en roles con la interfaz de la línea de comandos entre plataformas de Azure" 
	description="Administrar el control de acceso basado en roles con la interfaz de línea de comandos entre plataformas" 
	services="" 
	documentationCenter="" 
	authors="guangyang" 
	manager="terrylan" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="command-line-interface" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2014" 
	ms.author="guayan"/>

# Administración del control de acceso basado en rol con la interfaz de línea de comandos multiplataforma #

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/es-es/documentation/articles/xplat-cli-rbac.md" title="Cross-Platform CLI">CLI multiplataforma</a></div>

El control de acceso basado en rol (RBAC) del Portal de vista previa de Azure y la API del Administrador de recursos de Azure le permite administrar el acceso a su suscripción y sus recursos en un nivel específico. Con esta característica, puede conceder acceso a usuarios, grupos o entidades de seguridad de servicio de Active Directory asignándoles roles en un ámbito determinado.

En este tutorial, aprenderá a usar la interfaz de la línea de comandos multiplataforma (xplat-cli) de Azure para administrar el control de acceso basado en rol. Le guía por el proceso de crear y comprobar asignaciones de roles.

**Duración aproximada:** 15 minutos

## Requisitos previos ##

Para poder usar xplat-cli para administrar RBAC, necesita lo siguiente:

- La versión 0.8.8 o una posterior de la interfaz de la línea de comandos multiplataforma de Azure. Para instalar la última versión y asociarla con su suscripción de Azure, vea [Instalación y configuración de la interfaz de la línea de comandos multiplataforma de Azure](http://azure.microsoft.com/documentation/articles/xplat-cli/).
- Lea también los siguientes tutoriales para aprender a configurar y usar el Administrador de recursos de Azure en la interfaz de la línea de comandos multiplataforma de Azure: [Uso de la interfaz de la línea de comandos entre plataformas de Azure con Resource Manager](http://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/)

## Apartados de este tutorial ##

* [Conectarse a sus suscripciones](#connect)
* [Comprobar asignaciones de roles existentes](#check)
* [Crear una asignación de rol](#create)
* [Comprobar los permisos](#verify)
* [Pasos siguientes](#next)

## <a id="connect"></a>Conectarse a sus suscripciones ##

Como RBAC solo funciona con el Administrador de recursos de Azure, lo primero que hay que hacer es pasar al modo Administrador de recursos de Azure. Escriba:

    azure config mode arm

Si quiere más información, vea [Uso de la interfaz de la línea de comandos para varias plataformas de Azure con el Administrador de recursos](http://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/)

Para conectarse a sus suscripciones de Azure, escriba:

    azure login -u <username>

En el símbolo de la línea de comandos, escriba la contraseña de su cuenta de Azure (use solo una cuenta de organización). Xplat-cli obtendrá todas las suscripciones que tenga con esta cuenta y se configurará para usar la primera como predeterminada. Con el control de acceso basado en roles, solo podrá obtener las suscripciones donde tiene permisos por ser administrador adjunto o por tener una asignación de rol. 

Si tiene varias suscripciones y quiere cambiar a otra, escriba:

    # Se mostrarán las suscripciones en la cuenta.
    azure account list
    # Utilice el nombre de la suscripción para seleccionar aquella en la que desea trabajar.
    azure account set <nombre de la suscripción>

Si quiere más información, vea [Instalación y configuración de la interfaz de línea de comandos multiplataforma de Azure](http://azure.microsoft.com/documentation/articles/xplat-cli/).

## <a id="check"></a>Comprobación de asignaciones de rol existentes ##

Ahora vamos a ver qué asignaciones de roles existen en la suscripción. Escriba:

    azure role assignment list

Esto devolverá todas las asignaciones de roles que haya en la suscripción. Cabe destacar dos cosas:

1. Necesitará acceso de lectura en el nivel de la suscripción.
2. Si la suscripción tiene muchas asignaciones de roles, se puede tardar un rato en recuperarlas todas.

También puede comprobar las asignaciones de roles existentes para una definición de rol determinada, en un ámbito concreto y para un cierto usuario. Escriba:

    azure role assignment list -g group1 --upn <user email> -o Owner

Esto devolverá toda las asignaciones de roles para un usuario determinado de su directorio de Azure AD, que tiene una asignación de rol de "Owner" para el grupo de recursos "group1". La asignación de rol puede venir de dos sitios:

1. Una asignación de rol de "Owner" al usuario para el grupo de recursos.
2. Una asignación de rol de "Owner" al usuario para el elemento primario del grupo de recursos (en este caso, la suscripción) porque, si tiene permisos en un recurso primario determinado, tendrá los mismos permisos en todos los recursos secundarios.

Todos los parámetros de este cmdlet son opcionales. Puede combinarlos para comprobar asignaciones de roles con distintos filtros.

## <a id="create"></a>Creación de una asignación de rol ##

Para crear una asignación de rol, tiene que pensar en lo siguiente:

- A quién le quiere asignar el rol: puede usar los siguientes cmdlets de Azure Active Directory para ver qué usuarios, grupos y entidades de seguridad de servicio tiene en su directorio.

    `azure ad user list
    azure ad user show
    azure ad group list
    azure ad group show
    azure ad group member list
    azure ad sp list
    azure ad sp show`

- Qué rol quiere asignar: puede usar el siguiente cmdlet para ver las definiciones de rol admitidas.

    `azure role list`

- Qué ámbito quiere asignar: hay tres niveles de ámbitos:

    - La suscripción actual
    - Un grupo de recursos. para obtener una lista de grupos de recursos, escriba `azure group list`.
    - Un recurso. para obtener una lista de recursos, escriba `azure resource list`.

A continuación, use 'azure role assignment create' para crear una asignación de rol. Por ejemplo:

 - Esto creará una asignación de rol en el nivel de suscripción actual para un usuario como lector:

    `azure role assignment create --upn <user's email> -o Reader`

- Esto creará una asignación de rol en un nivel de grupo de recursos:

    `PS C:\> azure role assignment create --upn <correo electrónico del usuario> -o Contributor -g group1`

- Esto creará una asignación de rol en un nivel de recursos:

    `azure role assignment create --upn <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <a id="verify"></a>Comprobación de los permisos ##

Después de comprobar que su cuenta tiene asignaciones de roles, puede ver los permisos que conceden estas asignaciones de roles ejecutando lo siguiente:

    PS C:\> azure group list
    PS C:\> azure resource list

Estos dos cmdlets solo devuelven los grupos de recursos o los recursos donde tiene permiso de lectura. También le mostrarán los permisos que tiene.

Si intenta ejecutar otros cmdlets, como `azure group create`, obtendrá un error de acceso denegado si no tiene el permiso.

## <a id="next"></a>Pasos siguientes ##

Si quiere más información sobre cómo administrar el control de acceso basado en roles con xplat-cli y otros temas relacionados:

- [Control de acceso basado en roles en Windows Azure](http://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)
- [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure](http://azure.microsoft.com/documentation/articles/xplat-cli/)
- [Uso de la interfaz de la línea de comandos entre plataformas de Azure con Resource Manager](http://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/)
- [Uso del grupo de recursos para administrar los recursos de Azure](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups): Obtenga información acerca de la creación y administración de grupos de recursos en el Portal de administración de Azure.
- [Blog sobre Azure](http://blogs.msdn.com/windowsazure) (en inglés): obtenga información acerca de las nuevas características de Azure.
- [Configuración del control de acceso basado en roles usando Windows PowerShell](http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/)
- [Solución de problemas del control de acceso basado en roles](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/)

<!--HONumber=46--> 

<!--HONumber=46--> 
