<properties pageTitle="Managing Role-Based Access Control with Azure Cross-Platform Command-Line Interface" metaKeywords="ResourceManager, Azure cross-platform command-line interface, Azure command-line, azure command-line, azure cli, RBAC" description="Managing role-based access control with cross-platform command-line interface" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Cross-Platform Command-Line Interface" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# Administrar el control de acceso basado en roles con la interfaz de línea de comandos entre plataformas

<div class="dev-center-tutorial-selector sublanding"><a href="/es-es/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/es-es/documentation/articles/xplat-cli-rbac.md" title="CLI entre plataformas">CLI entre plataformas</a></div>

El control de acceso basado en roles (RBAC) del portal de vista previa de Azure y la API del administrador de recursos de Azure le permite administrar el acceso a su suscripción y sus recursos en un nivel específico. Con esta característica, puede conceder acceso a usuarios, grupos o entidades de seguridad de servicio de Active Directory asignándoles roles en un ámbito determinado.

En este tutorial, aprenderá a usar la interfaz de la línea de comandos entre plataformas (xplat-cli) de Azure para administrar el control de acceso basado en roles. Le guía por el proceso de crear y comprobar asignaciones de roles.

**Duración aproximada:** 15 minutos

## Requisitos previos

Para poder usar xplat-cli para administrar RBAC, necesita lo siguiente:

-   La versión 0.8.8 o una posterior de la interfaz de la línea de comandos entre plataformas de Azure. Para instalar la última versión y asociarla con su suscripción de Azure, vea [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure][Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure].
-   Lea también los siguientes tutoriales para aprender a configurar y usar el Administrador de recursos de Azure en la interfaz de la línea de comandos entre plataformas de Azure: [Uso de la interfaz de la línea de comandos entre plataformas de Azure con Resource Manager][Uso de la interfaz de la línea de comandos entre plataformas de Azure con Resource Manager]

## Apartados de este tutorial

-   [Conectarse a sus suscripciones][Conectarse a sus suscripciones]
-   [Comprobar asignaciones de roles existentes][Comprobar asignaciones de roles existentes]
-   [Crear una asignación de rol][Crear una asignación de rol]
-   [Comprobar los permisos][Comprobar los permisos]
-   [Pasos siguientes][Pasos siguientes]

## <span id="connect"></span></a>Conectarse a sus suscripciones

Como RBAC solo funciona con el Administrador de recursos de Azure, lo primero que hay que hacer es pasar al modo Administrador de recursos de Azure. Tipo:

    azure config mode arm

Si quiere más información, vea [Uso de la interfaz de la línea de comandos para varias plataformas de Azure con el Administrador de recursos][Uso de la interfaz de la línea de comandos entre plataformas de Azure con Resource Manager].

Para conectarse a sus suscripciones de Azure, escriba:

    azure login -u <username>

En el símbolo de la línea de comandos, escriba la contraseña de su cuenta de Azure (use solo una cuenta de organización). Xplat-cli obtendrá todas las suscripciones que tenga con esta cuenta y se configurará para usar la primera como predeterminada. Con el control de acceso basado en roles, solo podrá obtener las suscripciones donde tiene permisos por ser administrador adjunto o por tener una asignación de rol.

Si tiene varias suscripciones y quiere cambiar a otra, escriba:

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

Si quiere más información, vea [Instalación y configuración de la interfaz de línea de comandos entre plataformas de Azure][Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure].

## <span id="check"></span></a>Comprobar asignaciones de roles existentes

Ahora vamos a ver qué asignaciones de roles existen en la suscripción. Tipo:

    azure role assignment list

Esto devolverá todas las asignaciones de roles que haya en la suscripción. Cabe destacar dos cosas:

1.  Necesitará acceso de lectura en el nivel de la suscripción.
2.  Si la suscripción tiene muchas asignaciones de roles, se puede tardar un rato en recuperarlas todas.

También puede comprobar las asignaciones de roles existentes para una definición de rol determinada, en un ámbito concreto y para un cierto usuario. Tipo:

    azure role assignment list -g group1 --upn <user's email> -o Owner

Esto devolverá toda las asignaciones de roles para un usuario determinado de su directorio de Azure AD, que tiene una asignación de rol de "Owner" para el grupo de recursos "group1". La asignación de rol puede venir de dos sitios:

1.  Una asignación de rol de "Owner" al usuario para el grupo de recursos.
2.  Una asignación de rol de "Owner" al usuario para el elemento primario del grupo de recursos (en este caso, la suscripción) porque, si tiene permisos en un recurso primario determinado, tendrá los mismos permisos en todos los recursos secundarios.

Todos los parámetros de este cmdlet son opcionales. Puede combinarlos para comprobar asignaciones de roles con distintos filtros.

## <span id="create"></span></a>Crear una asignación de rol

Para crear una asignación de rol, tiene que pensar en lo siguiente:

-   A quién le quiere asignar el rol: puede usar los siguientes cmdlets de Azure Active Directory para ver qué usuarios, grupos y entidades de seguridad de servicio tiene en su directorio.

    `azure ad user list
    azure ad user show
    azure ad group list
    azure ad group show
    azure ad group member list
    azure ad sp list
    azure ad sp show`

-   Qué rol quiere asignar: puede usar el siguiente cmdlet para ver las definiciones de rol admitidas.

    `azure role list`

-   Qué ámbito quiere asignar; hay tres niveles de ámbitos:

    -   La suscripción actual
    -   Un grupo de recursos; para obtener una lista de grupos de recursos, escriba `azure group list`
    -   Un recurso; para obtener una lista de recursos, escriba `azure resource list`

Luego use `azure role assignment create` para crear una asignación de rol. Por ejemplo:

-   Esto creará una asignación de rol en el nivel de suscripción actual para un usuario como lector:

    `azure role assignment create --upn <user's email> -o Reader`

-   Esto creará una asignación de rol en un nivel de grupo de recursos:

    `PS C:\> azure role assignment create --upn <user's email> -o Contributor -g group1`

-   Esto creará una asignación de rol en un nivel de recursos:

    `azure role assignment create --upn <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <span id="verify"></span></a>Comprobar los permisos

Después de comprobar que su cuenta tiene asignaciones de roles, puede ver los permisos que conceden estas asignaciones de roles ejecutando lo siguiente:

    PS C:\> azure group list
    PS C:\> azure resource list

Estos dos cmdlets solo devuelven los grupos de recursos o los recursos donde tiene permiso de lectura. También le mostrarán los permisos que tiene.

Si intenta ejecutar otros cmdlets, como `azure group create`, obtendrá un error de acceso denegado si no tiene el permiso.

## <span id="next"></span></a>Pasos siguientes

Si quiere más información sobre cómo administrar el control de acceso basado en roles con xplat-cli y otros temas relacionados:

-   [Control de acceso basado en roles en Windows Azure][Control de acceso basado en roles en Windows Azure]
-   [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure][Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure]
-   [Uso de la interfaz de la línea de comandos entre plataformas de Azure con Resource Manager][Uso de la interfaz de la línea de comandos entre plataformas de Azure con Resource Manager]
-   [Uso del grupo de recursos para administrar los recursos de Azure][Uso del grupo de recursos para administrar los recursos de Azure]: aprenda a crear y administrar grupos de recursos en el portal de administración de Azure.
-   [Blog sobre Azure][Blog sobre Azure] (en inglés): obtenga información acerca de las nuevas características de Azure.
-   [Configuración del control de acceso basado en roles usando Windows PowerShell][Configuración del control de acceso basado en roles usando Windows PowerShell]
-   [Solución de problemas del control de acceso basado en roles][Solución de problemas del control de acceso basado en roles]

  [Windows PowerShell]: /es-es/documentation/articles/powershell-rbac.md "Windows PowerShell"
  [CLI entre plataformas]: /es-es/documentation/articles/xplat-cli-rbac.md "CLI entre plataformas"
  [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure]: http://azure.microsoft.com/es-es/documentation/articles/xplat-cli/
  [Uso de la interfaz de la línea de comandos entre plataformas de Azure con Resource Manager]: http://azure.microsoft.com/es-es/documentation/articles/xplat-cli-azure-resource-manager/
  [Conectarse a sus suscripciones]: #connect
  [Comprobar asignaciones de roles existentes]: #check
  [Crear una asignación de rol]: #create
  [Comprobar los permisos]: #verify
  [Pasos siguientes]: #next
  [Control de acceso basado en roles en Windows Azure]: http://azure.microsoft.com/es-es/documentation/articles/role-based-access-control-configure/
  [Uso del grupo de recursos para administrar los recursos de Azure]: http://azure.microsoft.com/es-es/documentation/articles/azure-preview-portal-using-resource-groups
  [Blog sobre Azure]: http://blogs.msdn.com/windowsazure
  [Configuración del control de acceso basado en roles usando Windows PowerShell]: http://azure.microsoft.com/es-es/documentation/articles/role-based-access-control-powershell/
  [Solución de problemas del control de acceso basado en roles]: http://azure.microsoft.com/es-es/documentation/articles/role-based-access-control-troubleshooting/
