<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Dropbox para Empresas | Microsoft Azure" 
    description="Aprenda a usar DropBox para Empresas con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#Tutorial: Integración de Azure Active Directory con Dropbox para Empresas
  
El objetivo de este tutorial es mostrar la integración de Azure y Dropbox para Empresas. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de prueba en Dropbox para empresas
  
Después de completar este tutorial, los usuarios de Azure AD asignados a DropBox para Empresas podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de DropBox para Empresas (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Dropbox para Empresas
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuario
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Escenario")



##Habilitación de la integración de aplicaciones para Dropbox para Empresas
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Dropbox para Empresas.

###Siga estos pasos para habilitar la integración de aplicaciones para Dropbox para Empresas:

1.  En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **DropBox para Empresas**.

    ![Galería de aplicaciones](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **DropBox para Empresas** y haga clic en **Completar** para agregar la aplicación.

    ![Dropbox para Empresas](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox para Empresas")

##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Dropbox para Empresas con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

Como parte de este procedimiento, es necesario cargar un certificado codificado en base 64 en su inquilino de Dropbox para Empresas. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure clásico, en la página de integración de la aplicaciones **Dropbox for Business**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en DropBox para Empresas?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, realice los pasos siguientes:

	a. Inicie sesión en el inquilino de Dropbox para Empresas.

	![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Configurar inicio de sesión único")

	b. En el panel de navegación de la izquierda, haga clic en **Admin Console** (Consola de administración).

	![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Configurar inicio de sesión único")

	c. En la **consola de administración**, haga clic en la opción **Authentication** (Autenticación) del panel de navegación izquierdo.

	![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Configurar inicio de sesión único")

	d. En la sección **Single sign-on** (Inicio de sesión único), seleccione **Enable single sign-on** (Habilitar inicio de sesión único) y haga clic en **More** (Más) para expandir esta sección.

	![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Configurar inicio de sesión único")

	e. Copie la dirección URL que aparece al lado de **Users can sign in by entering their email address or they can go directly to** (Los usuarios pueden iniciar sesión especificando su dirección de correo electrónico o pueden ir directamente a).

	![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Configurar inicio de sesión único")

	f. En el Portal de Azure clásico, en el cuadro de texto **URL de inicio de sesión de Dropbox**, pegue la dirección URL.

	![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Configurar inicio de sesión único")



4. En la página **Configurar inicio de sesión único en Dropbox for Business**, haga clic en **Descargar certificado** y guarde el archivo de certificado en el equipo.

	![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Configurar inicio de sesión único")


5. En su inquilino de Dropbox for Business, en la sección **Inicio de sesión único** de la página **Autenticación**, siga estos pasos:

	![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configurar inicio de sesión único")

	a. Haga clic en **Required** (Obligatorio).

	b. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Dropbox for Business**, copie el valor de **Dirección URL de la página de inicio de sesión** y péguelo en el cuadro de texto **Sign in URL** (Dirección URL de inicio de sesión).


	c. Cree un archivo **codificado en base 64** a partir del certificado descargado.

	> [AZURE.TIP] Para más información, vea [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).


	d. Haga clic en el botón **"Choose certificate"** (Elegir certificado) y busque el **archivo de certificado codificado en base 64**.


	e. Haga clic en el botón **"Save changes"** (Guardar cambios) para completar la configuración del inquilino de DropBox for Business.


6. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y, luego, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

	![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Configurar inicio de sesión único")



##Configuración del aprovisionamiento de usuario
  
El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de cuentas de usuario de Active Directory para Dropbox para Empresas.


### Siga estos pasos para configurar el aprovisionamiento de usuario:

1. En el Portal de Azure clásico, en la página de integración de la aplicación **DropBox for Business**, haga clic en **Configurar aprovisionamiento de usuarios** para abrir el cuadro de diálogo **Configurar aprovisionamiento de usuarios**.

2. En la página Habilitar aprovisionamiento de usuarios en Dropbox para Empresas, haga clic en Habilitar aprovisionamiento de usuarios para abrir el cuadro de diálogo Sign in to Dropbox to link with Azure AD (Iniciar sesión en Dropbox para vincularlo a Azure AD).

	![Aprovisionamiento de usuarios](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "Aprovisionamiento de usuarios")

3. En el cuadro de diálogo **Sign in to Dropbox to link with Azure AD** (Iniciar sesión en Dropbox para vincularlo a Azure AD), inicie sesión en su inquilino de Dropbox for Business.

	![Aprovisionamiento de usuarios](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "Aprovisionamiento de usuarios")



4. Haga clic en **Allow** (Permitir) para conceder a Azure AD acceso a Dropbox.

	![Aprovisionamiento de usuarios](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "Aprovisionamiento de usuarios")



5. Para finalizar la configuración, haga clic en el botón **Complete** (Completar).

	![Aprovisionamiento de usuarios](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "Aprovisionamiento de usuarios")




##Asignación de usuarios
  
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Siga estos pasos para asignar usuarios a Dropbox para Empresas:

1.  En el Portal de Azure clásico, cree una cuenta de prueba.

2.  En la página de integración de la aplicación **DropBox for Business**, haga clic en **Asignar usuarios**.

    ![Asignar usuarios](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

    ![Sí](./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Sí")
  


Ahora debería esperar 10 minutos y comprobar si la cuenta se ha sincronizado en Dropbox for Business.

Como primer paso de verificación, puede comprobar el estado del aprovisionamiento, para lo que debe hacer clic en la opción **Panel** de la página de integración de la aplicación **Dropbox for Business** del Portal de Azure clásico.

![Asignar usuarios](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Asignar usuarios")


Un ciclo de aprovisionamiento de usuarios completado correctamente se indica con un estado relacionado:

![Asignar usuarios](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Asignar usuarios")


Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




## Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0817_2016-->