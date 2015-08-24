<properties pageTitle="Tutorial: integración de Azure Active Directory con CloudBees | Microsoft Azure" description="Aprenda a usar CloudBees con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: integración de Azure Active Directory con CloudBees
>[AZURE.TIP]Para enviar comentarios, haga clic [aquí](http://go.microsoft.com/fwlink/?LinkId=528737).

El objetivo de este tutorial es mostrar la integración de Azure y CloudBees. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de CloudBees

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a CloudBees podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de CloudBees (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](https://msdn.microsoft.com/library/azure/dn308586.aspx)

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para CloudBees
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-cloudbees-tutorial/IC790415.png "Escenario")

##Habilitación de la integración de aplicaciones para CloudBees

El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para CloudBees.

###Siga estos pasos para habilitar la integración de aplicaciones para CloudBees:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-cloudbees-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-cloudbees-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-cloudbees-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-cloudbees-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **CloudBees**.

    ![Galería de aplicaciones](./media/active-directory-saas-cloudbees-tutorial/IC790416.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **CloudBees** y, a continuación, haga clic en **Completar** para agregar la aplicación.

    ![Cloudbees](./media/active-directory-saas-cloudbees-tutorial/IC790417.png "Cloudbees")

##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse CloudBees con su cuenta de Azure AD mediante federación basada en el protocolo SAML. La configuración de un inicio de sesión único para CloudBees requiere la recuperación de un valor de huella digital de un certificado. Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure AD, en la página de integración de aplicaciones de **CloudBees**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único **.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cloudbees-tutorial/IC790418.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en CloudBees?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, a continuación, haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cloudbees-tutorial/IC790419.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de CloudBees**, escriba la dirección URL que usan los usuarios para iniciar sesión en CloudBees (por ejemplo: **https://grandcentral.cloudbees.com/login*") y, a continuación, haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-cloudbees-tutorial/IC790420.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en CloudBees**, para descargar el certificado, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cloudbees-tutorial/IC790421.png "Configurar inicio de sesión único")

5.  En otra ventana del explorador web, inicie sesión en el sitio de la compañía de CloudBees como administrador.

6.  En el menú de la parte superior, haga clic en **Settings** (Configuración).

    ![Settings](./media/active-directory-saas-cloudbees-tutorial/IC790422.png "Settings")

7.  Haga clic en la pestaña **SSO integration** (Integración de SSO).

8.  En la sección **Set Up SAML 2.0 Single Sign On** (Configurar inicio de sesión único de SAML 2.0), siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-cloudbees-tutorial/IC790423.png "Configurar inicio de sesión único")

    1.  En el Portal de Azure AD, en la página **Configurar inicio de sesión único en CloudBees**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Remote Login URL** (Dirección URL de inicio de sesión remoto).
    2.  Copie el valor de **Huella digital** del certificado exportado y, a continuación, péguelo en el cuadro de texto **Existing certificate fingerprint** (Huella digital de certificado existente).
    
        >[AZURE.TIP]Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI)

    3.  En el cuadro de texto **Authentication domains** (Dominios de autenticación), escriba el dominio de su empresa.
    4.  Haga clic en **Guardar**.

9.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar ** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cloudbees-tutorial/IC790424.png "Configurar inicio de sesión único")

##Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en **CloudBees**, deben aprovisionarse en **CloudBees**. En el caso de **CloudBees**, el aprovisionamiento es una tarea manual.

###Para aprovisionar una cuenta de usuario en CloudBees, realice los siguientes pasos:

1.  Inicie sesión en el sitio de la compañía de **CloudBees** como administrador.

2.  Vaya a **Edit Users** (Editar usuarios).

    ![Usuarios](./media/active-directory-saas-cloudbees-tutorial/IC790429.png "Usuarios")

3.  Haga clic en **Agregar**.

    ![Agregar](./media/active-directory-saas-cloudbees-tutorial/IC790430.png "Agregar")

4.  En la sección **Add User** (Agregar usuario), lleve a cabo estos pasos:

    ![Agregar usuario](./media/active-directory-saas-cloudbees-tutorial/IC790431.png "Agregar usuario")

    1.  En los cuadros de texto correspondientes, escriba los valores de **Correo electrónico**, **Nombre**, **Apellidos** y otros atributos de la cuenta de Azure Active Directory válida que desea aprovisionar.
    2.  Haga clic en **Add User** (Agregar usuario).

        >[AZURE.NOTE]Se enviará un correo electrónico con instrucciones de inicio de sesión al propietario de la cuenta.

>[AZURE.NOTE]Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de CloudBees ofrecida por CloudBees para aprovisionar cuentas de usuario de AAD.

##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a CloudBees, siga estos pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **CloudBees**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-cloudbees-tutorial/IC790432.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y, a continuación, en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-cloudbees-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://msdn.microsoft.com/library/azure/dn308586.aspx).

<!---HONumber=August15_HO7-->