---
title: "Tutorial: Integración de Azure Active Directory con Dropbox for Business | Microsoft Docs"
description: "Aprenda a usar DropBox para Empresas con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/17/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7a0a300f505d9012471679ac27373944f07fdba3
ms.openlocfilehash: e328f8ecfad5f481462a7b9c0c4a16e966e33217


---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Tutorial: Integración de Azure Active Directory con Dropbox para Empresas
El objetivo de este tutorial es mostrar la integración de Azure y Dropbox para Empresas.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de prueba en Dropbox para empresas

Después de completar este tutorial, los usuarios de Azure AD asignados a DropBox para Empresas podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de DropBox para Empresas (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Dropbox para Empresas
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Scenario")

## <a name="enabling-the-application-integration-for-dropbox-for-business"></a>Habilitación de la integración de aplicaciones para Dropbox para Empresas
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Dropbox para Empresas.

### <a name="to-enable-the-application-integration-for-dropbox-for-business-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Dropbox para Empresas:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicación](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Dropbox for Business**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Application gallery")
7. En el panel de resultados, seleccione **Dropbox for Business** y haga clic en **Completar** para agregar la aplicación.
   
    ![DropBox para Empresas](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox for Business")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Dropbox para Empresas con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

Como parte de este procedimiento, es necesario cargar un certificado codificado en base 64 en su inquilino de Dropbox para Empresas. Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicaciones **Dropbox for Business**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Configure single sign-on")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Dropbox for Business?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Configure single sign-on")
3. En la página **Configurar dirección URL de la aplicación** , realice los pasos siguientes:
   
    a. Inicie sesión en el inquilino de Dropbox para Empresas. 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Configure single sign-on")
   
    b. En el panel de navegación de la izquierda, haga clic en **Admin Console**(Consola de administración). 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Configure single sign-on")
   
    c. En la **Admin Console** (Consola de administración), haga clic en la opción **Authentication** (Autenticación) del panel de navegación izquierdo. 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Configure single sign-on")
   
    d. En la sección **Single sign-on** (Inicio de sesión único), seleccione **Enable single sign-on** (Habilitar inicio de sesión único) y haga clic en **More** (Más) para expandir esta sección.  
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Configure single sign-on")
   
    e. Copie la dirección URL que aparece al lado de **Users can sign in by entering their email address or they can go directly to**(Los usuarios pueden iniciar sesión especificando su dirección de correo electrónico o pueden ir directamente a). 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Configure single sign-on")
   
    f. En el Portal de Azure clásico, en el cuadro de texto **URL de inicio de sesión de Dropbox** , pegue la dirección URL. 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Configure single sign-on")  
4. En la página **Configurar inicio de sesión único en Dropbox for Business**, haga clic en **Descargar certificado** y guarde el archivo de certificado en el equipo.  
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Configure single sign-on")
5. En su inquilino de Dropbox for Business, en la sección **Single sign-on** (Inicio de sesión único) de la página **Authentication** (Autenticación), siga estos pasos: 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configure single sign-on")
   
    a. Haga clic en **Required**(Obligatorio).
   
    b. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Dropbox for Business**, copie el valor de **Dirección URL de la página de inicio de sesión** y péguelo en el cuadro de texto **Sign in URL** (Dirección URL de inicio de sesión).

    c. Cree un archivo **codificado en base 64** a partir del certificado descargado. 

    > [!TIP] 
    > Para más información, vea [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

    d. Haga clic en **Elegir certificado** y vaya a su **archivo de certificado codificado en Base 64**.

    e. Haga clic en **Guardar cambios** para completar la configuración en el inquilino de DropBox for Business.

1. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**. 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de cuentas de usuario de Active Directory para Dropbox para Empresas.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **DropBox for Business**, haga clic en **Configurar aprovisionamiento de usuarios** para abrir el cuadro de diálogo **Configurar aprovisionamiento de usuarios**.
2. En la página Habilitar aprovisionamiento de usuarios en Dropbox para Empresas, haga clic en Habilitar aprovisionamiento de usuarios para abrir el cuadro de diálogo Sign in to Dropbox to link with Azure AD (Iniciar sesión en Dropbox para vincularlo a Azure AD).  
   
    ![Aprovisionamiento de usuarios](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "User provisioning")
3. En el cuadro de diálogo **Sign in to Dropbox to link with Azure AD** (Iniciar sesión en Dropbox para vincularlo a Azure AD), inicie sesión en su inquilino de Dropbox for Business. 
   
    ![Aprovisionamiento de usuarios](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "User provisioning")
4. Haga clic en **Allow** (Permitir) para conceder a Azure AD acceso a Dropbox. 
   
    ![Aprovisionamiento de usuarios](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "User provisioning")
5. Para finalizar la configuración, haga clic en el botón **Complete** (Completar).  
   
    ![Aprovisionamiento de usuarios](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "User provisioning")

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-dropbox-for-business-perform-the-following-steps"></a>Siga estos pasos para asignar usuarios a Dropbox para Empresas:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **DropBox for Business**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Assign users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Yes")

Ahora debería esperar 10 minutos y comprobar si la cuenta se ha sincronizado en Dropbox for Business.

Como primer paso de verificación, puede comprobar el estado del aprovisionamiento, para lo que debe hacer clic en la opción **Panel** de la página de integración de la aplicación **Dropbox for Business** del Portal de Azure clásico.

![Asignar usuarios](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Assign users")

Un ciclo de aprovisionamiento de usuarios completado correctamente se indica con un estado relacionado:

![Asignar usuarios](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Assign users")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso.
Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->


