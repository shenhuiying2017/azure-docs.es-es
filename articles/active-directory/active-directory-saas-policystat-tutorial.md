<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con PolicyStat | Microsoft Azure" 
    description="Aprenda cómo usar PolicyStat con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." 
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


#<a name="tutorial:-azure-active-directory-integration-with-policystat"></a>Tutorial: Integración de Azure Active Directory con PolicyStat
  
El objetivo de este tutorial es mostrar la integración de Azure y PolicyStat.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de PolicyStat
  
Después de completar este tutorial, los usuarios de Azure AD asignados a PolicyStat podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de PolicyStat (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para PolicyStat
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-policystat-tutorial/IC808662.png "Scenario")
##<a name="enabling-the-application-integration-for-policystat"></a>Habilitación de la integración de aplicaciones para PolicyStat
  
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para PolicyStat.

###<a name="to-enable-the-application-integration-for-policystat,-perform-the-following-steps:"></a>Siga estos pasos para habilitar la integración de aplicaciones para PolicyStat:

1.  En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.

    ![Applications](./media/active-directory-saas-policystat-tutorial/IC700994.png "Applications")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-policystat-tutorial/IC749321.png "Add application")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-policystat-tutorial/IC749322.png "Add an application from gallerry")

6.  En el **cuadro de búsqueda**, escriba **PolicyStat**.

    ![Galería de aplicaciones](./media/active-directory-saas-policystat-tutorial/IC808627.png "Application Gallery")

7.  En el panel de resultados, seleccione **PolicyStat** y haga clic en **Completar** para agregar la aplicación.

    ![PolicyStat](./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##<a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en PolicyStat con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
La aplicación PolicyStat espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML** .  
La siguiente captura de pantalla le muestra un ejemplo de esto.

![Atributos](./media/active-directory-saas-policystat-tutorial/IC808628.png "Attributes")

###<a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure clásico, en la página de integración de aplicaciones de **PolicyStat**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-policystat-tutorial/IC808629.png "Configure Single Sign-On")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en PolicyStat?**, seleccione **Inicio de sesión único de Microsoft Azure** AD y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-policystat-tutorial/IC808630.png "Configure Single Sign-On")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL utilizada por los usuarios para iniciar sesión en su aplicación de PolicyStat de URL (por ejemplo, *“https://demo-azure.policystat.com”*) y haga clic en **Siguiente**.

    ![Configurar las opciones de la aplicación](./media/active-directory-saas-policystat-tutorial/IC808631.png "Configure App Settings")

4.  En la página **Configuración de inicio de sesión único en PolicyStat**, haga clic en **Descargar metadatos** y guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-policystat-tutorial/IC808632.png "Configure Single Sign-On")

5.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía PolicyStat como administrador.

6.  Haga clic en la pestaña **Administración** y en **Configuración de inicio de sesión único** en el panel de navegación izquierdo.

    ![Menú Administrador](./media/active-directory-saas-policystat-tutorial/IC808633.png "Administrator Menu")

7.  En la sección **Configuración**, seleccione **Habilitar la integración de inicio de sesión único**.

    ![Configuración de inicio de sesión único](./media/active-directory-saas-policystat-tutorial/IC808634.png "Single Sign-On Configuration")

8.  Haga clic en **Configurar atributos** y, en la sección **Configurar atributos**, realice los pasos siguientes:

    ![Configuración de inicio de sesión único](./media/active-directory-saas-policystat-tutorial/IC808635.png "Single Sign-On Configuration")

    1.  En el cuadro de texto **Atributo de nombre de usuario**, escriba **uid**.
    2.  En el cuadro de texto **Atributo de nombre**, escriba **firstname**.
    3.  En el cuadro de texto **Atributo de apellido**, escriba **lastname**.
    4.  En el cuadro de texto **Atributo de correo electrónico**, escriba **emailaddress**.
    5.  Haga clic en **Guardar cambios**.

9.  Haga clic en **Sus metadatos de IDP** y en la sección **Sus metadatos de IDP**, realice los pasos siguientes:

    ![Configuración de inicio de sesión único](./media/active-directory-saas-policystat-tutorial/IC808635.png "Single Sign-On Configuration")

    1.  Abra el archivo de metadatos descargado, copie el contenido y luego péguelo en el cuadro de texto **Metadatos del proveedor de identidades** .
    2.  Haga clic en **Guardar cambios**.

10. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-policystat-tutorial/IC771723.png "Configure Single Sign-On")

11. 12. En el menú de la parte superior, haga clic en **Atributos** to open the **SAML Token Atributos** .

    ![Atributos](./media/active-directory-saas-policystat-tutorial/IC795920.png "Attributes")

13. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:

    ![Atributos](./media/active-directory-saas-policystat-tutorial/IC804823.png "Attributes")

    1.  Haga clic en **agregar atributo de usuario**.
    2.  En el cuadro de texto **Nombre de atributo**, escriba **uid**.
    3.  En el cuadro de texto **Valor del atributo**, seleccione **ExtractMailPrefix()**.
    4.  En la lista **Correo**, seleccione **User.mail**.
    5.  Haga clic en **Completo**.
##<a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
  
Para permitir que los usuarios de Azure AD inicien sesión en PolicyStat, deben aprovisionarse en PolicyStat.  
PolicyStat admite aprovisionamiento de usuarios justo a tiempo. Esto significa que no es necesario agregar usuarios manualmente a PolicyStat.  
Los usuarios se agregarán automáticamente en su primer inicio de sesión a través del inicio de sesión único.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de PolicyStat ofrecida por PolicyStat para aprovisionar cuentas de usuario de AAD.

##<a name="assigning-users"></a>Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###<a name="to-assign-users-to-policystat,-perform-the-following-steps:"></a>Para asignar usuarios a PolicyStat, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **PolicyStat**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-policystat-tutorial/IC808636.png "Assign Users")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-policystat-tutorial/IC767830.png "Yes")
  
Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


<!--HONumber=Oct16_HO2-->


