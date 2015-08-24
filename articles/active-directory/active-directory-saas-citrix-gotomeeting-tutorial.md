<properties pageTitle="Tutorial: Integración de Azure Active Directory con Citrix GoToMeeting | Microsoft Azure" description="Aprenda cómo usar Citrix GoToMeeting con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integración de Azure Active Directory con Citrix GoToMeeting  
Se aplica a: Azure

>[AZURE.TIP]Para enviar comentarios, haga clic [aquí](http://go.microsoft.com/fwlink/?LinkId=522412).

El objetivo de este tutorial es mostrar la integración de Azure y Citrix GoToMeeting. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino en Citrix GoToMeeting

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Citrix GoToMeeting
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Configuración](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuración")
##Habilitación de la integración de aplicaciones para Citrix GoToMeeting

El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Citrix GoToMeeting.

###Siga estos pasos para habilitar la integración de aplicaciones para Citrix GoToMeeting:

1.  En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda** escriba **Citrix GoToMeeting**.

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  En el panel de resultados, seleccione **Citrix GoToMeeting** y, a continuación, haga clic en **Completar** para agregar la aplicación.

    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Citrix GoToMeeting con su cuenta de Azure AD mediante federación basada en el protocolo SAML. Como parte de este procedimiento, es necesario cargar un certificado codificado en base 64 en su inquilino de Citrix GoToMeeting. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En la página de integración de aplicaciones de **Citrix GoToMeeting**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **CONFIGURAR INICIO DE SESIÓN ÚNICO**.

    ![Habilitar inicio de sesión único](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Habilitar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Citrix GoToMeeting?**, seleccione **Inicio de sesión único de Microsoft Azure AD**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configurar inicio de sesión único")

3.  En la página **Configuración de inicio de sesión único en Citrix GoToMeeting**, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configurar inicio de sesión único")

4.  En otra ventana del explorador, inicie sesión como administrador en su inquilino de Citrix GoToMeeting.

5.  Abra la página [Set up SAML 2.0 single sign-on configuration (SSO)](https://login.citrixonline.com/saml/settings.html) (establecer configuración de sesión único SAML 2.0, SSO) y a continuación, realice los pasos siguientes:

    ![Configuración de SAML](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC689232.png "Configuración de SAML")

    1.  Seleccione **Configure manually** (configurar manualmente).
    2.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Citrix GoToMeeting**, copie el valor de **Dirección URL de la página de cierre de sesión** y péguelo en el cuadro de texto **Sign-out page URL** (dirección URL de página de cierre de sesión).
    3.  En el Portal de Azure, en la página de diálogo **Configurar inicio de sesión único en Citrix GoToMeeting**, copie el valor de **Dirección URL de la página de inicio de sesión** y péguelo en el cuadro de texto **Sign-in page URL** (dirección URL de página de inicio de sesión).
    4.  Cree un archivo **codificado en base 64** a partir del certificado descargado.  

        >[AZURE.TIP]Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    5.  Haga clic en**Reemplazar certificado**y, a continuación, cargar el**archivo de certificado codificado en base 64**.
    6.  Haga clic en **Guardar**.

6.  En el Portal de Azure AD, seleccione la confirmación de configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario

El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de cuentas de usuario de Citrix GoToMeeting.

###Siga estos pasos para configurar el aprovisionamiento de usuario:

1.  En el Portal de administración de Azure, en la página de integración de aplicaciones de **Citrix GoToMeeting**, haga clic en **Configurar aprovisionamiento de usuarios** para abrir el cuadro de diálogo de **Configurar aprovisionamiento de usuarios**.

    ![Configuración de aprovisionamiento de usuario](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configuración de aprovisionamiento de usuario")

2.  En la página de **nombre de usuario de administrador de Citrix GotoMeeting**, escriba el nombre de una cuenta de Citrix GoToMeeting que tenga derechos de administrador en su inquilino Citrix GoToMeeting, y luego haga clic en **Siguiente**.

    ![Configuración de aprovisionamiento de usuario](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configuración de aprovisionamiento de usuario")

3.  En la página **Confirmación**, haga clic en la marca de verificación para guardar la configuración.

4.  Haga clic en el botón **validar** para comprobar la configuración.
##Asignación de usuarios

Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Para asignar usuarios a Citrix GoToMeeting, lleve a cabo los siguientes pasos:

1.  En el Portal de Azure AD, cree una cuenta de prueba.

2.  En la página de integración de aplicaciones de **Citrix GoToMeeting**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y, a continuación, en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Sí")

Ahora debería esperar 10 minutos y comprobar si la cuenta se ha sincronizado en Dropbox for Business.

Como primer paso de verificación, puede comprobar el estado del aprovisionamiento, haciendo clic en el panel de información en la D en la página de integración de aplicaciones de **Citrix GoToMeeting** en el Portal de administración de Azure.

![Panel](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Panel")

Un ciclo de aprovisionamiento de usuarios completado correctamente se indica con un estado relacionado:

![Estado de integración](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Estado de integración")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso.

Para obtener más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->