<properties 
    pageTitle="Tutorial: integración de Azure Active Directory con ScreenSteps | Microsoft Azure" 
    description="Aprenda cómo usar ScreenSteps con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/26/2016" 
    ms.author="jeedes" />


#<a name="tutorial:-azure-active-directory-integration-with-screensteps"></a>Tutorial: Integración de Azure Active Directory con ScreenSteps
  
El objetivo de este tutorial es mostrar la integración de Azure y ScreenSteps.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de ScreenSteps
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a ScreenSteps podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de ScreenSteps (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para ScreenSteps
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-screensteps-tutorial/IC778516.png "Scenario")
##<a name="enabling-the-application-integration-for-screensteps"></a>Habilitación de la integración de aplicaciones para ScreenSteps
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para ScreenSteps.

###<a name="to-enable-the-application-integration-for-screensteps,-perform-the-following-steps:"></a>Siga estos pasos para habilitar la integración de aplicaciones para ScreenSteps:

1.  En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.

    ![Applications](./media/active-directory-saas-screensteps-tutorial/IC700994.png "Applications")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-screensteps-tutorial/IC749321.png "Add application")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-screensteps-tutorial/IC749322.png "Add an application from gallerry")

6.  En el **cuadro de búsqueda**, escriba **ScreenSteps**.

    ![Galería de aplicaciones](./media/active-directory-saas-screensteps-tutorial/IC778517.png "Application gallery")

7.  En el panel de resultados, seleccione **ScreenSteps** y haga clic en **Completar** para agregar la aplicación.

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##<a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en ScreenSteps con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

###<a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure clásico, en la página de integración de aplicaciones de **ScreenSteps**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configure single sign-on")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en ScreenSteps?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configure single sign-on")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de ScreenSteps**, escriba su dirección URL con el siguiente patrón "*https://\<nombre-inquilino\>.ScreenSteps.com*" y haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configure app URL")

4.  En la página **Configuración de inicio de sesión único en ScreenSteps**, para descargar el certificado, haga clic en **Descargar certificado** y guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configure single sign-on")

5.  En otra ventana del explorador web, inicie sesión en su sitio de la compañía de ScreenSteps como administrador.

6.  Haga clic en **Administración de cuentas**.

    ![Administración de cuentas](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Account management")

7.  Haga clic en **Autenticación remota**.

    ![Autenticación remota](./media/active-directory-saas-screensteps-tutorial/IC778524.png "Remote authentication")

8.  Haga clic en **Crear extremo de autenticación**.

    ![Autenticación remota](./media/active-directory-saas-screensteps-tutorial/IC778525.png "Remote authentication")

9.  En la sección **Crear un punto de conexión de autenticación** , siga estos pasos:

    ![Crear un punto de conexión de autenticación](./media/active-directory-saas-screensteps-tutorial/IC778526.png "Create an authentication endpoint")

    1.  En el cuadro de texto **Título** , escriba un título.
    2.  En la lista **Modo**, seleccione **SAML**.
    3.  Haga clic en **Crear**.

10. En la sección **Punto de conexión de autenticación remota** , siga estos pasos:

    ![Punto de conexión de autenticación remota](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Remote authentication endpoint")

    1.  En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en ScreenSteps**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión remoto**.
    2.  En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en ScreenSteps**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **URL de cierre de sesión**.
    3.  Haga clic en **Elegir un archivo**y luego cargue el certificado descargado.
    4.  Haga clic en **Update**(Actualizar).

11. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configure single sign-on")
##<a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en **ScreenSteps**, deben aprovisionarse en **ScreenSteps**.  
En el caso de **ScreenSteps**, el aprovisionamiento es una tarea manual.

###<a name="to-provision-a-user-account-to-screensteps,-perform-the-following-steps:"></a>Para aprovisionar cuentas de usuario a ScreenSteps, realice los siguientes pasos:

1.  Inicie sesión en su inquilino de **ScreenSteps** .

2.  Haga clic en **Administración de cuentas**.

    ![Administración de cuentas](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Account management")

3.  Haga clic en **Usuarios**.

    ![Usuarios](./media/active-directory-saas-screensteps-tutorial/IC778544.png "Users")

4.  Haga clic en **Crear un usuario**.

    ![Todos los usuarios](./media/active-directory-saas-screensteps-tutorial/IC778545.png "All Users")

5.  Desde la lista **Rol de usuario** , seleccione un rol para el usuario.

6.  En la sección de Rol de usuario, escriba el **nombre**, los **apellidos**, el **correo electrónico**, el **inicio de sesión**, la **contraseña** y la **confirmación de contraseña** de una cuenta válida de AAD que quiera aprovisionar en los cuadros de texto relacionados.

    ![Nuevo usuario](./media/active-directory-saas-screensteps-tutorial/IC778546.png "New user")

7.  En la sección Grupos, seleccione **Grupo de autenticación (SAML)** y haga clic en **Crear usuario**.

    ![Grupos](./media/active-directory-saas-screensteps-tutorial/IC778547.png "Groups")

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de ScreenSteps ofrecida por ScreenSteps para aprovisionar cuentas de usuario de AAD.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###<a name="to-assign-users-to-screensteps,-perform-the-following-steps:"></a>Para asignar usuarios a ScreenSteps, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **ScreenSteps**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-screensteps-tutorial/IC773094.png "Assign users")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.

    ![Asignar usuarios](./media/active-directory-saas-screensteps-tutorial/IC778548.png "Assign users")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


<!--HONumber=Oct16_HO2-->


