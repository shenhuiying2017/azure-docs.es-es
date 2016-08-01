<properties 
    pageTitle="Tutorial: Integración de Azure Active Directory con Box | Microsoft Azure" 
    description="Aprenda a usar Box con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc." 
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
    ms.date="07/13/2016" 
    ms.author="jeedes" />




#Tutorial: Integración de Azure Active Directory con Box


  
El objetivo de este tutorial es mostrar la integración de Azure y Box. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

-   Una suscripción de Azure válida
-   Un inquilino de prueba en Box
  
Después de completar este tutorial, los usuarios de Azure AD asignados a Box podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Box (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).
  
La situación descrita en este tutorial consta de los siguientes bloques de creación:

1.  Habilitación de la integración de aplicaciones para Box
2.  Configuración del inicio de sesión único
3.  Configuración del aprovisionamiento de usuarios y grupos
4.  Asignación de usuarios

![Escenario](./media/active-directory-saas-box-tutorial/IC769537.png "Escenario")



##Habilitación de la integración de aplicaciones para Box
  
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Box.

###Siga estos pasos para habilitar la integración de aplicaciones para Box:

1.  En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.

    ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3.  Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.

    ![Aplicaciones](./media/active-directory-saas-box-tutorial/IC700994.png "Aplicaciones")

4.  Haga clic en **Agregar** en la parte inferior de la página.

    ![Agregar aplicación](./media/active-directory-saas-box-tutorial/IC749321.png "Agregar aplicación")

5.  En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.

    ![Agregar una aplicación de la galería](./media/active-directory-saas-box-tutorial/IC749322.png "Agregar una aplicación de la galería")

6.  En el **cuadro de búsqueda**, escriba **Box**.

    ![Galería de aplicaciones](./media/active-directory-saas-box-tutorial/IC701023.png "Galería de aplicaciones")

7.  En el panel de resultados, seleccione **Box** y haga clic en **Completar** para agregar la aplicación.

    ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")



##Configuración del inicio de sesión único
  
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Box con su cuenta de Azure AD mediante federación basada en el protocolo SAML. Como parte de este procedimiento, es necesario cargar metadatos en Box.com.

###Siga estos pasos para configurar el inicio de sesión único:

1.  En el Portal de Azure clásico, en la página de integración de la aplicación **Box**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-box-tutorial/IC769538.png "Configurar inicio de sesión único")

2.  En la página **¿Cómo desea que los usuarios inicien sesión en Box?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-box-tutorial/IC769539.png "Configurar inicio de sesión único")

3.  En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **Dirección URL de inquilino de Box**, escriba la dirección URL del inquilino de Box (por ejemplo: https://<nombreDeMiDominio>.box.com) y haga clic en **Siguiente**.

    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-box-tutorial/IC669826.png "Configurar dirección URL de la aplicación")

4.  En la página **Configurar inicio de sesión único en Box**, para descargar sus metadatos, haga clic en **Descargar metadatos** y guarde el archivo de datos localmente en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-box-tutorial/IC669824.png "Configurar inicio de sesión único")

5.  Reenvíe el archivo de metadatos al equipo de soporte técnico de Box. El equipo de soporte técnico necesita configurar un inicio de sesión único para usted.

6.  Seleccione la confirmación de la configuración de inicio de sesión único y, a continuación, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-box-tutorial/IC769540.png "Configurar inicio de sesión único")
##Configuración del aprovisionamiento de usuario
  
El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de las cuentas de usuario de Active Directory en Box.

###Siga estos pasos para configurar el inicio de sesión único:

1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Box**, haga clic en **Configurar aprovisionamiento de usuarios** para abrir el cuadro de diálogo **Configurar aprovisionamiento de usuarios**.

	![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-box-tutorial/IC769541.png "Habilitar el aprovisionamiento automático de usuarios")

2. En la página de diálogo **Habilitar aprovisionamiento de usuarios en Box**, haga clic en **Habilitar aprovisionamiento de usuarios**.

	![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-box-tutorial/IC769544.png "Habilitar el aprovisionamiento automático de usuarios")

3. En la página **Log in to grant access to Box** (Iniciar sesión para otorgar acceso a Box), proporcione las credenciales necesarias y haga clic en **Authorize** (Autorizar).

	![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-box-tutorial/IC769546.png "Habilitar el aprovisionamiento automático de usuarios")


4. Haga clic en **Grant access to Box** (Otorgar acceso a Box) para autorizar esta operación y volver al Portal de Azure clásico.

	![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-box-tutorial/IC769549.png "Habilitar el aprovisionamiento automático de usuarios")


5. En la página **Opciones de aprovisionamiento**, las casillas **Tipos de objeto para proveer** permiten seleccionar si se aprovisionan objetos de grupo en Box, además de objetos de usuario. Para obtener más información, vea a continuación la sección "Asignación de usuarios y grupos".


6. Para finalizar la configuración, haga clic en el botón Completar.

	![Habilitar el aprovisionamiento automático de usuarios](./media/active-directory-saas-box-tutorial/IC769551.png "Habilitar el aprovisionamiento automático de usuarios")



##Asignación de un usuario de prueba
  
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

###Siga estos pasos para asignar usuarios a Box:

1. En el Portal de Azure clásico, cree una cuenta de prueba.

2. En la página de integración de aplicaciones de **Box**, haga clic en **Asignar usuarios**.

	![Asignar usuarios](./media/active-directory-saas-box-tutorial/IC769552.png "Asignar usuarios")

3.  Seleccione su usuario de prueba, haga clic en **Asignar** y luego en **Sí** para confirmar la asignación.

	![Sí](./media/active-directory-saas-box-tutorial/IC767830.png "Sí")
  
Ahora debería esperar 10 minutos y comprobar si la cuenta se ha sincronizado en Box.

Como primer paso de verificación, puede comprobar el estado del aprovisionamiento haciendo clic en la opción Panel de la página de integración de la aplicación DocuSign en el Portal de Azure clásico.

![Panel](./media/active-directory-saas-box-tutorial/IC769553.png "Panel")

Un ciclo de aprovisionamiento de usuarios completado correctamente se indica con un estado relacionado:

![Estado de integración](./media/active-directory-saas-box-tutorial/IC769555.png "Estado de integración")


En su inquilino de Box, los usuarios sincronizados se muestran en **Usuarios administrados** en la **Consola de administración**.

![Estado de integración](./media/active-directory-saas-box-tutorial/IC769556.png "Estado de integración")


##Asignación de usuarios y grupos

La pestaña **Box > Usuarios y grupos** del Portal de Azure clásico permite especificar qué usuarios y grupos deben tener acceso a Box. La asignación de un usuario o un grupo hace que ocurra lo siguiente:

* Azure AD permite que el usuario asignado (ya sea mediante asignación directa o pertenencia al grupo) se autentique en Box. Si no se asigna un usuario, Azure AD no permitirá que inicie sesión en Box y devolverá un error en la página de inicio de sesión de Azure AD.

* Se agregará un icono de la aplicación de Box al [iniciador de aplicaciones](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) del usuario.

* Si está habilitado el aprovisionamiento automático, se agregan a la cola de aprovisionamiento los usuarios o grupos asignados para aprovisionarlos automáticamente.

    * Si solo se ha configurado el aprovisionamiento de objetos de usuario, todos los usuarios asignados directamente se colocan en la cola de aprovisionamiento, y todos los usuarios que son miembros de los grupos asignados se colocarán en la cola de aprovisionamiento.
    
    * Si se ha configurado el aprovisionamiento de objetos de grupo, todos los objetos de grupo asignados se aprovisionan en Box, así como todos los usuarios que son miembros de esos grupos. Las pertenencias a grupos y usuarios se conservan una vez que se escriben en Box.
    
Puede usar la pestaña **Atributos > Inicio de sesión único** para configurar los atributos de usuario (o notificaciones) que se presentan en Box durante la autenticación basada en SAML, y la pestaña **Atributos > Aprovisionamiento** para configurar la forma en que los atributos de usuario y grupo fluyen de Azure AD a Box durante las operaciones de aprovisionamiento. Para obtener más información, consulte los recursos que se muestran a continuación.


## Recursos adicionales

* [Personalización de notificaciones emitidas en el token SAML](active-directory-saml-claims-customization.md)
* [Aprovisionamiento: personalización de asignaciones de atributos](active-directory-saas-customizing-attribute-mappings.md)
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0720_2016-->