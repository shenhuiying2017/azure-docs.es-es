<properties
   pageTitle="Creación de aplicación de AD y entidad de servicio en el portal | Microsoft Azure"
   description="Describe cómo crear una nueva aplicación de Active Directory y una entidad de servicio que puede utilizarse con el control de acceso basado en roles en el Administrador de recursos de Azure para administrar el acceso a los recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/29/2015"
   ms.author="tomfitz"/>

# Creación de aplicación de Active Directory y entidad de servicio mediante el portal

## Información general
Si tiene una aplicación que necesita tener acceso a un recurso o modificarlo en su suscripción, puede usar el portal para crear una aplicación de Active Directory y asignarla a un rol con los permisos correctos. Si crea una aplicación de Active Directory a través del portal, en realidad se crea la aplicación y una entidad de servicio. La entidad de servicio se utiliza al establecer los permisos.

En este tema se muestra cómo crear una nueva aplicación y entidad de servicio mediante el portal de Azure. Actualmente, debe usar el portal de Microsoft Azure para crear una nueva aplicación de Active Directory. Esta capacidad se agregará al portal de vista previa de Azure en una versión posterior. Puede utilizar el portal de vista previa para asignar la aplicación a un rol.

## Conceptos
1. Azure Active Directory (AAD): un servicio de administración de identidades y acceso creado para la nube. Para obtener más información, consulte [¿Qué es Azure Active Directory?](active-directory/active-directory-whatis.md)
2. Entidad de servicio: una instancia de una aplicación en un directorio.
3. Aplicación de AD: un registro de directorio en AAD que identifica una aplicación en AAD. 

Para obtener una explicación más detallada de las aplicaciones y entidades de servicio, consulte [Objetos de aplicación y de entidad de servicio](active-directory/active-directory-application-objects.md). Para obtener más información acerca de la autenticación de Active Directory, vea [Escenarios de autenticación en Azure AD](active-directory/active-directory-authentication-scenarios.md).


## Creación de los objetos de la aplicación y la entidad de servicio

1. Inicie sesión en su cuenta de Azure a través del [portal](https://manage.windowsazure.com/).

2. Seleccione **Active Directory** en el panel izquierdo.

     ![seleccionar Active Directory][1]

3. Seleccione el directorio que desea utilizar para crear la nueva aplicación.

     ![elegir directorio][2]

3. Para ver las aplicaciones en su directorio, haga clic en **Aplicaciones**.

     ![ver aplicaciones][11]

4. Si no ha creado una aplicación en ese directorio previamente, debería ver algo similar a la siguiente imagen. Haga clic en **AGREGAR UNA APLICACIÓN**.

     ![agregar aplicación][6]

     O bien, haga clic en **Agregar** en el panel inferior.

     ![agregar][12]

5. Seleccione el tipo de aplicación que desea crear. Para este tutorial, no usaremos una aplicación desde la galería.

     ![nueva aplicación][10]

6. Rellene el nombre de la aplicación y seleccione el tipo de aplicación que desea utilizar. Puesto que se va a utilizar la entidad de servicio de esta aplicación para autenticar con el Administrador de recursos de Azure, se elegirá crear una **APLICACIÓN WEB Y/O API WEB** y haremos clic en el botón siguiente.

     ![aplicación de nombre][9]

7. Rellene las propiedades de la aplicación. Para **DIRECCIÓN URL DE INICIO DE SESIÓN**, proporcione el URI para un sitio web que describe la aplicación. No se valida la existencia del sitio web. Para **URI DE ID. DE APLICACIÓN**, proporcione el URI que identifica la aplicación. No se valida la singularidad o la existencia del extremo. Haga clic en **Completo** para crear la aplicación de AAD.

     ![propiedades de la aplicación][4]

## Creación de una clave de autenticación para la aplicación
El portal ahora debería tener la aplicación seleccionada.

1. Haga clic en la pestaña **Configurar** para configurar la contraseña de su aplicación.

     ![configurar aplicación][3]

2. Desplácese hacia abajo hasta la sección **Claves** y seleccione cuánto tiempo desea que la contraseña sea válida.

     ![claves][7]

3. Seleccione **Guardar** para crear la clave.

     ![guardar][13]

     Se muestra la clave guardada, y tiene la posibilidad de copiarla.

     ![clave guardada][8]

4. Ahora puede usar su clave para autenticarse como una entidad de servicio. Necesitará su **ID. DE CLIENTE** además su **CLAVE** para iniciar sesión. Vaya al **ID. DE CLIENTE** y cópielo.
  
     ![id. de cliente][5]


La aplicación está ahora lista y la entidad de servicio creada en el inquilino. Al iniciar sesión como una entidad de servicio asegúrese de usar lo siguiente:

* **ID. DE CLIENTE**: como nombre de usuario.
* **CLAVE**: como contraseña.

## Asignación de la aplicación a un rol

Puede utilizar el [portal de vista previa](https://portal.azure.com) para asignar la aplicación de Active Directory a un rol que tenga acceso al recurso al que necesita tener acceso. Para obtener información sobre la asignación de la aplicación a un rol, consulte [Control de acceso basado en roles de Azure Active Directory](active-directory/role-based-access-control-configure.md).

## Obtención de token de acceso en el código

Si está utilizando. NET, puede recuperar el token de acceso para su aplicación con el código siguiente.

En primer lugar, debe instalar la biblioteca de autenticación de Active Directory en el proyecto de Visual Studio. La manera más fácil de hacerlo es utilizar el paquete de NuGet. Abra la ventana Consola del Administrador de paquetes y escriba los siguientes comandos.

    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
    PM> Update-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Safe

En la aplicación, agregue un método similar al siguiente para recuperar el token.

    public static string GetAccessToken()
    {
        var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
        var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
        var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

        if (result == null) {
            throw new InvalidOperationException("Failed to obtain the JWT token");
        }

        string token = result.AccessToken;

        return token;
    }

## Pasos siguientes

- Para obtener información sobre cómo especificar directivas de seguridad, consulte [Administración y auditoría del acceso a los recursos](resource-group-rbac.md).  
- Para ver una demostración en vídeo de estos pasos, consulte [Habilitación de la administración mediante programación de un recurso de Azure con Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).
- Para obtener información acerca del uso de Azure PowerShell o la CLI de Azure para trabajar con aplicaciones de Active Directory y entidades de servicio, incluida la forma de utilizar un certificado para la autenticación, consulte [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](./resource-group-authenticate-service-principal.md).
- Para obtener instrucciones sobre cómo implementar la seguridad con el Administrador de recursos de Azure, consulte [Consideraciones de seguridad para el Administrador de recursos de Azure](best-practices-resource-manager-security.md).


<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png

<!---HONumber=Nov15_HO2-->