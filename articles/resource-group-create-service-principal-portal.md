<properties
   pageTitle="Creación de entidad de servicio en el portal | Microsoft Azure"
   description="Describe cómo crear una nueva aplicación de Active Directory y una entidad de servicio que puede utilizarse con el control de acceso basado en roles en el Administrador de recursos de Azure para administrar el acceso a los recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/19/2016"
   ms.author="tomfitz"/>

# Uso del portal para crear una aplicación de Active Directory y una entidad de servicio con acceso a los recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI de Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)


Si tiene una aplicación que necesita tener acceso a ciertos recursos o modificarlos, puede configurar una aplicación de Active Directory (AD) y asignarle los permisos requeridos. En este tema se muestra cómo realizar tales pasos a través del portal. Actualmente, debe usar el portal clásico para crear una nueva aplicación de Active Directory y, después, cambiar al Portal de Azure para asignar un rol a dicha aplicación.

> [AZURE.NOTE] Quizá le resulte más fácil configurar la aplicación de AD y la entidad de servicio mediante [PowerShell](resource-group-authenticate-service-principal.md) o la [CLI de Azure](resource-group-authenticate-service-principal-cli.md), especialmente si desea utilizar un certificado para la autenticación. En este tema se describe cómo utilizar un certificado.

Para ver una explicación de los conceptos de Active Directory, consulte [Objetos Application y objetos ServicePrincipal](./active-directory/active-directory-application-objects.md). Para obtener más información acerca de la autenticación de Active Directory, vea [Escenarios de autenticación en Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Si desea conocer los pasos detallados de la integración de una aplicación en Azure para administrar recursos, consulte [Guía del desarrollador para la autorización con la API de Azure Resource Manager](resource-manager-api-authentication.md).

## Creación de una aplicación de Active Directory

1. Inicie sesión en su cuenta de Azure a través del [portal clásico](https://manage.windowsazure.com/).

2. Seleccione **Active Directory** en el panel izquierdo.

     ![seleccionar Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. Seleccione el directorio de Active Directory que desea usar para crear la nueva aplicación. Si tiene más de uno, normalmente se recomienda crear la aplicación en el directorio donde se encuentre la suscripción. Solo puede conceder acceso a recursos en su suscripción para aplicaciones que se encuentren en el mismo directorio que dicha suscripción.

     ![elegir directorio](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
    Si necesita encontrar el directorio de su suscripción, seleccione **Configuración** y busque el nombre del directorio.
   
     ![buscar directorio predeterminado](./media/resource-group-create-service-principal-portal/show-default-directory.png)

3. Para ver las aplicaciones en su directorio, haga clic en **Aplicaciones**.

     ![ver aplicaciones](./media/resource-group-create-service-principal-portal/view-applications.png)

4. Si no ha creado una aplicación en ese directorio previamente, debería ver algo similar a la siguiente imagen. Haga clic en **AGREGAR UNA APLICACIÓN**.

     ![agregar aplicación](./media/resource-group-create-service-principal-portal/create-application.png)

     O bien, haga clic en **Agregar** en el panel inferior.

     ![agregar](./media/resource-group-create-service-principal-portal/add-icon.png)

5. Seleccione el tipo de aplicación que desea crear. Para este tutorial, seleccione **Agregar una aplicación que mi organización está desarrollando**.

     ![nueva aplicación](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. Dé un nombre a la aplicación y seleccione el tipo al que esta pertenece. Para este tutorial, seleccione **APLICACIÓN WEB Y/O API WEB** y haga clic en el botón Siguiente. Si selecciona **APLICACIÓN DE CLIENTE NATIVO**, los pasos restantes de este artículo no coincidirán con las opciones que verá.

     ![aplicación de nombre](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. Rellene las propiedades de la aplicación. Para **DIRECCIÓN URL DE INICIO DE SESIÓN**, proporcione el identificador URI para un sitio web que describe la aplicación. No se valida la existencia del sitio web. Para **URI DE ID. DE APLICACIÓN**, proporcione el URI que identifica la aplicación.

     ![propiedades de la aplicación](./media/resource-group-create-service-principal-portal/app-properties.png)

Ha creado la aplicación.

## Obtención del identificador del cliente y la clave de autenticación

Al iniciar sesión mediante programación, necesitará el identificador de la aplicación. Si esta se ejecuta con sus propias credenciales, también precisará una clave de autenticación.

1. Haga clic en la pestaña **Configurar** para configurar la contraseña de su aplicación.

     ![configurar aplicación](./media/resource-group-create-service-principal-portal/application-configure.png)

2. Copie el valor de **ID. DE CLIENTE**.
  
     ![id. de cliente](./media/resource-group-create-service-principal-portal/client-id.png)

3. Si la aplicación se ejecuta con sus propias credenciales, desplácese hacia abajo, hasta la sección **Claves**, y seleccione cuánto tiempo desea que la contraseña sea válida.

     ![claves](./media/resource-group-create-service-principal-portal/create-key.png)

4. Seleccione **Guardar** para crear la clave.

     ![guardar](./media/resource-group-create-service-principal-portal/save-icon.png)

     Se muestra la clave guardada, y tiene la posibilidad de copiarla. No podrá recuperar la clave más tarde, por lo que querrá recuperarla ahora.

     ![clave guardada](./media/resource-group-create-service-principal-portal/save-key.png)

## Obtención del identificador de inquilino

Al iniciar sesión mediante programación, tiene que transferir el identificador de inquilino con la solicitud de autenticación. En el caso de las aplicaciones web y las de API web, para recuperar el identificador de inquilino, seleccione **Ver extremos** en la parte inferior de la pantalla y recupere el identificador, tal y como se muestra a continuación.

   ![id. de inquilino](./media/resource-group-create-service-principal-portal/save-tenant.png)

También puede recuperar el identificador de inquilino mediante PowerShell:

    Get-AzureRmSubscription

O de la CLI de Azure:

    azure account show --json

## Establecimiento de permisos delegados

Si la aplicación tiene acceso a recursos en nombre del usuario que ha iniciado sesión, debe conceder a la aplicación el permiso delegado para tener acceso a otras aplicaciones. Esto se puede hacer en la sección de **permisos para otras aplicaciones** de la pestaña **Configurar**. De forma predeterminada, ya hay habilitado un permiso delegado para Azure Active Directory. Deje este permiso delegado sin cambios.

1. Seleccione **Agregar una aplicación**.

2. En la lista, seleccione **Azure Service Management API**. Seguidamente, seleccione el icono de proceso terminado.

      ![seleccionar aplicación](./media/resource-group-create-service-principal-portal/select-app.png)

3. En la lista desplegable de permisos delegados, seleccione **Access Azure Service Management as organization** (Acceder a la administración de servicios de Azure como organización).

      ![seleccionar permiso](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Guarde el cambio.

## Configuración de aplicación multiinquilino

Si los usuarios de otros directorios de Azure Active Directory pueden dar su consentimiento a la aplicación e iniciar sesión en ella, debe habilitar los servicios multiinquilino. En la pestaña **Configurar**, establezca **La aplicación es multiinquilino** en **Sí**.

![multiinquilino](./media/resource-group-create-service-principal-portal/multi-tenant.png)

## Asignación de aplicación a un rol

Si la aplicación se ejecuta con sus propias credenciales, debe asignarla a un rol. Debe decidir qué rol representa los permisos adecuados para la aplicación. Para obtener más información acerca de los roles disponibles, vea [RBAC: Roles integrados](./active-directory/role-based-access-built-in-roles.md).

Para asignar un rol, debe tener acceso `Microsoft.Authorization/*/Write` que se concede a través del rol [Propietario](./active-directory/role-based-access-built-in-roles.md#owner) o del rol [Administrador de acceso de usuario](./active-directory/role-based-access-built-in-roles.md#user-access-administrator).

Puede establecer el ámbito en el nivel de suscripción, grupo de recursos o recurso. Los permisos se heredan en los niveles inferiores de ámbito (por ejemplo, el hecho de agregar una aplicación al rol Lector para un grupo de recursos significa que esta puede leer el grupo de recursos y los recursos que contenga).

1. Para asignar la aplicación a un rol, cambie desde el portal clásico al [Portal de Azure](https://portal.azure.com).

1. En el portal, desplácese hasta el nivel de ámbito al que desea asignar la aplicación. En este tema, puede navegar a un grupo de recursos y, en la hoja del grupo de recursos, seleccione el icono **Acceso**.

     ![seleccionar usuarios](./media/resource-group-create-service-principal-portal/select-users.png)

2. Seleccione **Agregar**.

     ![seleccionar agregar](./media/resource-group-create-service-principal-portal/select-add.png)

3. Seleccione el rol **lector** (o el rol que desea asignar a la aplicación).

     ![seleccionar rol](./media/resource-group-create-service-principal-portal/select-role.png)

4. En primer lugar, verá la lista de usuarios que puede agregar al rol, no verá las aplicaciones. Solo verá el grupo y los usuarios.

     ![mostrar usuarios](./media/resource-group-create-service-principal-portal/show-users.png)

5. Para que la aplicación aparezca, debe buscarla. Comience a escribir el nombre de la aplicación y cambiará la lista de opciones disponibles. Seleccione la aplicación cuando la vea en la lista.

     ![asignar a rol](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. Seleccione **Aceptar** para finalizar la asignación de un rol. Ahora debería ver la aplicación en la lista de usos asignados a un rol para el grupo de recursos.

     ![mostrar](./media/resource-group-create-service-principal-portal/show-app.png)

Para obtener más información acerca de cómo asignar usuarios y aplicaciones a los roles a través del portal, consulte [Administración del acceso con el Portal de administración de Azure](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal).

## Aplicaciones de ejemplo

Las aplicaciones de ejemplo siguientes muestran cómo iniciar sesión como entidad de servicio.

**.NET**

- [Implementación de una máquina virtual habilitada para SSH con una plantilla con .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Administración de recursos y grupos de recursos de Azure con .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Introducción a recursos - Implementación mediante la plantilla de Azure Resource Manager - en Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Introducción a recursos - Administración de grupo de recursos - en Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Implementación de una máquina virtual habilitada para SSH con una plantilla en Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Administración de recursos y grupos de recursos de Azure con Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [Implementación de una máquina virtual habilitada para SSH con una plantilla en Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Administración de recursos y grupos de recursos de Azure con Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Implementación de una máquina virtual habilitada para SSH con una plantilla en Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Administración de recursos y grupos de recursos de Azure con Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## Pasos siguientes

- Para obtener información sobre cómo especificar directivas de seguridad, consulte [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).
- Para ver una demostración en vídeo de estos pasos, consulte [Enabling Programmatic Management of an Azure Resource with Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).

<!---HONumber=AcomDC_0720_2016-->