<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con SCC LifeCycle | Microsoft Azure" 
    description="Aprenda cómo usar SCC LifeCycle con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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


#<a name="tutorial:-azure-active-directory-integration-with-scc-lifecycle"></a>Tutorial: Integración de Azure Active Directory con SCC LifeCycle
  
El objetivo de este tutorial es mostrar la integración de Azure y SCC LifeCycle.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Una suscripción habilitada para el inicio de sesión único en SCC LifeCycle
  
Después de completar este tutorial, los usuarios de Azure AD que ha asignado a SCC LifeCycle podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de SCC LifeCycle (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para SCC LifeCycle
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "Scenario")
##<a name="enabling-the-application-integration-for-scc-lifecycle"></a>Habilitación de la integración de aplicaciones para SCC LifeCycle
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para SCC LifeCycle.

###<a name="to-enable-the-application-integration-for-scc-lifecycle,-perform-the-following-steps:"></a>Siga estos pasos para habilitar la integración de aplicaciones en SCC LifeCycle:

1.  En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.

    ![Applications](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "Applications")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "Add application")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "Add an application from gallerry")

6.  En el **cuadro de búsqueda**, escriba **SCC LifeCycle**.

    ![Galería de aplicaciones](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "Application Gallery")

7.  En el panel de resultados, seleccione **SCC LifeCycle** y haga clic en **Completar** para agregar la aplicación.

    ![SCC LifeCycle](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC LifeCycle")
##<a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en SCC LifeCycle con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

###<a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure clásico, en la página de integración de aplicaciones de **SCC LifeCycle**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "Configure Single Sign-On")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en SCC LifeCycle?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "Configure Single Sign-On")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión** de, escriba la dirección URL utilizada por los usuarios para iniciar sesión en su aplicación de SCC LifeCycle con el patrón "*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*" y haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "Configure App URL")

4.  En la página **Configuración de inicio de sesión único en SCC LifeCycle**, haga clic en **Descargar metadatos** y guarde el archivo de metadatos localmente en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "Configure Single Sign-On")

5.  Reenvíe el archivo de metadatos al equipo de soporte técnico de SCC LifeCycle.

    >[AZURE.NOTE]El equipo de soporte técnico de SCC LifeCycle es el que debe habilitar el inicio de sesión único.

6.  En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "Configure Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en SCC LifeCycle, deben aprovisionarse en SCC LifeCycle.
  
No hay elemento de acción para que configure el aprovisionamiento de usuarios en SCC LifeCycle.  
Cuando un usuario asignado intente iniciar sesión en LifeCycle, se creará automáticamente una cuenta de LifeCycle si es necesario.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de SCC LifeCycle ofrecida por SCC LifeCycle para aprovisionar cuentas de usuario de AAD.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###<a name="to-assign-users-to-scc-lifecycle,-perform-the-following-steps:"></a>Para asignar usuarios a SCC LifeCycle, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **SCC LifeCycle**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "Assign Users")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Yes")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


<!--HONumber=Oct16_HO2-->


