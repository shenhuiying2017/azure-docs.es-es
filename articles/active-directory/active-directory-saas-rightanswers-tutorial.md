<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con RightAnswers | Microsoft Azure" 
    description="Aprenda cómo usar RightAnswers con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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


#<a name="tutorial:-azure-active-directory-integration-with-rightanswers"></a>Tutorial: Integración de Azure Active Directory con RightAnswers
  
El objetivo de este tutorial es mostrar la integración de Azure y RightAnswers. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en RightAnswers
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a RightAnswers podrán realizar un inicio de sesión único en la aplicación con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para RightAnswers
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-rightanswers-tutorial/IC802925.png "Scenario")
##<a name="enabling-the-application-integration-for-rightanswers"></a>Habilitación de la integración de aplicaciones para RightAnswers
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para RightAnswers.

###<a name="to-enable-the-application-integration-for-rightanswers,-perform-the-following-steps:"></a>Siga estos pasos para habilitar la integración de aplicaciones para RightAnswers:

1.  En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-rightanswers-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.

    ![Applications](./media/active-directory-saas-rightanswers-tutorial/IC700994.png "Applications")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-rightanswers-tutorial/IC749321.png "Add application")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-rightanswers-tutorial/IC749322.png "Add an application from gallerry")

6.  En el **cuadro de búsqueda**, escriba **RightAnswers**.

    ![Galería de aplicaciones](./media/active-directory-saas-rightanswers-tutorial/IC802926.png "Application Gallery")

7.  En el panel de resultados, seleccione **RightAnswers** y haga clic en **Completar** para agregar la aplicación.
##<a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en RightAnswers con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

###<a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure clásico, en la página de integración de aplicaciones de **RightAnswers**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-rightanswers-tutorial/IC802927.png "Configure Single Sign-On")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en RightAnswers?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-rightanswers-tutorial/IC802928.png "Configure Single Sign-On")

3.  En la página **Configurar las opciones de la aplicación**, en el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL utilizada por los usuarios para iniciar sesión en su aplicación RightAnswers (por ejemplo: *https://fortify.rightanswers.com/portal/ss/*) y haga clic en **Siguiente**.

    ![Configurar las opciones de la aplicación](./media/active-directory-saas-rightanswers-tutorial/IC802929.png "Configure App Settings")

4.  En la página **Configurar inicio de sesión único en RightAnswers**, para descargar los metadatos, haga clic en **Descargar metadatos** y guarde el archivo de metadatos localmente en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-rightanswers-tutorial/IC802930.png "Configure Single Sign-On")

5.  Envíe el archivo de metadatos descargado al equipo de soporte técnico de RightAnswers.

    >[AZURE.NOTE] El equipo de soporte técnico de RightAnswers es el que tiene que realizar la configuración real de SSO.
Cuando SSO se haya habilitado en su suscripción recibirá una notificación.

6.  En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-rightanswers-tutorial/IC802931.png "Configure Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en RightAnswers, deben aprovisionarse en RightAnswers.  
En el caso de RightAnswers, el aprovisionamiento es una tarea automatizada.  
No hay ningún elemento de acción para usted.
  
Los usuarios se crean automáticamente si es necesario durante el primer intento de inicio de sesión único.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de RightAnswers ofrecida por RightAnswers para aprovisionar cuentas de usuario de AAD.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###<a name="to-assign-users-to-rightanswers,-perform-the-following-steps:"></a>Para asignar usuarios a RightAnswers, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **RightAnswers**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-rightanswers-tutorial/IC802932.png "Assign Users")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.

    ![yes](./media/active-directory-saas-rightanswers-tutorial/IC767830.png "Yes")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


<!--HONumber=Oct16_HO2-->


