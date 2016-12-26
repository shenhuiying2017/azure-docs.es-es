---
title: "Tutorial: integración de Azure Active Directory con Zscaler | Microsoft Docs"
description: "Aprenda cómo usar Zscaler con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7a0a300f505d9012471679ac27373944f07fdba3
ms.openlocfilehash: 22f83af7fc19410df01350d40619e6520878bec7


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Tutorial: Integración de Azure Active Directory con Zscaler
El objetivo de este tutorial es mostrar la integración de Azure y Zscaler. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino en Zscaler

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Zscaler
2. Configuración del inicio de sesión único
3. Configuración de los valores de proxy
4. Configuración del aprovisionamiento de usuario
5. Asignación de usuarios

![Escenario](./media/active-directory-saas-zscaler-tutorial/IC769226.png "Scenario")

## <a name="enabling-the-application-integration-for-zscaler"></a>Habilitación de la integración de aplicaciones para Zscaler
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Zscaler.

### <a name="to-enable-the-application-integration-for-zscaler-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Zscaler:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-zscaler-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications](./media/active-directory-saas-zscaler-tutorial/IC700994.png "Applications")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicación](./media/active-directory-saas-zscaler-tutorial/IC749321.png "Add application")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-zscaler-tutorial/IC749322.png "Add an application from gallerry")

6. En el **cuadro de búsqueda**, escriba **Zscaler**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-zscaler-tutorial/IC769227.png "Application gallery")

7. En el panel de resultados, seleccione **Zscaler** y, después, haga clic en **Completar** para agregar la aplicación.
   
    ![Zscaler](./media/active-directory-saas-zscaler-tutorial/IC769228.png "Zscaler")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Zscaler con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
Como parte de este procedimiento, es necesario cargar un certificado en Zscaler.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **Zscaler**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
    ![Habilitar el inicio de sesión único](./media/active-directory-saas-zscaler-tutorial/IC769229.png "Enable single sign-on")

2. En la página **¿Cómo desea que los usuarios inicien sesión en Zscaler?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y después haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-tutorial/IC769230.png "Configure single sign on")

3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **Dirección URL de inicio de sesión de Zscaler**, escriba su dirección URL de inicio de sesión que obtuvo de Zscaler y después haga clic en **Siguiente**: 
   
    > [!NOTE]
    > Póngase en contacto con el equipo de soporte técnico de Zscaler si no sabe qué dirección URL de inicio de sesión es.
    > 
    > 
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-zscaler-tutorial/IC769231.png "Configure app URL")

4. En la página **Configurar inicio de sesión único en Zscaler** , siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-tutorial/IC769232.png "Configure single sign-on")
   
    1. Haga clic en **Descargar certificado** y después guarde el archivo del certificado como **c:\\\Zscaler.cer**.
    2. Copia la **Dirección URL de solicitud de autenticación** en el portapapeles.

5. Inicie sesión en su inquilino de Zscaler.

6. En el menú de la parte superior, haga clic en **Administration**(Administración).
   
    ![Administración](./media/active-directory-saas-zscaler-tutorial/IC769486.png "Administration")

7. En **Administrar administradores y roles** haga clic en **Administrar usuarios y autenticación**.
   
    ![Administrar administradores y roles](./media/active-directory-saas-zscaler-tutorial/IC769487.png "Manage Administrators & Roles")

8. En la sección **Elegir opción de autenticación para su organización** , lleve a cabo los pasos siguientes:
   
    ![Elegir opciones de autenticación](./media/active-directory-saas-zscaler-tutorial/IC769488.png "Choose Authentication Options")
   
    1. Seleccione **Autenticarse mediante el inicio de sesión único SAML**.
    2. Haga clic en **Configure SAML Single Sign-On Parameters**(Configurar parámetros de inicio de sesión único SAML).

9. En la página de diálogo **Configure SAML Single Sign-On Parameters** (Configurar parámetros de inicio de sesión único SAML), realice estos pasos y luego haga clic en **Listo**:
   
    ![Cargar certificado](./media/active-directory-saas-zscaler-tutorial/IC769489.png "Upload certificate")
   
    1. En el cuadro de texto **URL of the SAML Portal to which users are sent for authentication** (URL del portal de SAML al que se envían los usuarios para autenticación), pegue el valor del campo **Dirección URL de solicitud de autenticación** desde el Portal de Azure clásico.
   
    2. En el cuadro de texto **Attribute containing Login Name** (Atributo que contiene el nombre de inicio de sesión), escriba **NameID**.
   
    3. En el campo **Upload SSL Public Certificate** (Cargar certificado público de SSL), cargue el certificado que ha descargado desde el Portal de Azure clásico.
   
    4. Seleccione **Habilitar aprovisionamiento automático de SAML**.

10. En la página del cuadro de diálogo **Configurar autenticación de usuario** , realice los pasos siguientes:
    
    ![Configurar autenticación de usuario](./media/active-directory-saas-zscaler-tutorial/IC769490.png "Configure User Authentication")
    
    1. Haga clic en **Save**.
    2. Haga clic en **Activar ahora**.

11. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-tutorial/IC769491.png "Configure single sign-on")


## <a name="configuring-proxy-settings"></a>Configuración de los valores de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir la configuración de proxy en Internet Explorer

1. Inicie **Internet Explorer**.

2. Seleccione **Opciones de Internet** en el menú **Herramientas** para abrir el diálogo **Opciones de Internet**.
   
    ![Opciones de Internet](./media/active-directory-saas-zscaler-tutorial/IC769492.png "Internet Options")

3. Haga clic en la pestaña **Conexiones** .
   
    ![Conexiones](./media/active-directory-saas-zscaler-tutorial/IC769493.png "Connections")

4. Haga clic en **Configuración de LAN** para abrir el diálogo **Configuración de LAN**.

5. En la sección del servidor proxy, lleve a cabo estos pasos:
   
    ![Servidor proxy](./media/active-directory-saas-zscaler-tutorial/IC769494.png "Proxy server")
   
    1. Seleccione Usar un servidor proxy para la LAN.

    2. En el cuadro de texto Dirección, escriba **gateway.zscalertwo.net**.
   
    3. En el cuadro de texto Puerto, escriba **80**.
   
    4. Seleccione **No usar servidor proxy para direcciones locales**.
   
    5. Haga clic en **Aceptar** para cerrar el diálogo **Configuración de red de área local (LAN)**.

6. Haga clic en **Aceptar** para cerrar el diálogo **Opciones de Internet**.

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en Zscaler, deben aprovisionarse a Zscaler.  
En el caso de Zscaler, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:

1. Inicie sesión en su inquilino de **Zscaler** .

2. Haga clic en **Administración**.

    ![Administración](./media/active-directory-saas-zscaler-tutorial/IC781035.png "Administration")

3. Haga clic en **User Management**(Administración de usuarios).
   
    ![User Management](./media/active-directory-saas-zscaler-tutorial/IC781036.png "User Management")

4. En la pestaña **Usuarios**, haga clic en **Agregar**.
   
    ![Sumar](./media/active-directory-saas-zscaler-tutorial/IC781037.png "Add")

5. En la sección Agregar usuario, lleve a cabo estos pasos:
   
    ![Agregar usuario](./media/active-directory-saas-zscaler-tutorial/IC781038.png "Add User")
   
    1. Escriba el **Id. de usuario**, el **Nombre para mostrar del usuario**, la **Contraseña**, **Confirmar contraseña** y después seleccione **Grupos** y el **Departamento** de una cuenta de AAD válida que quiera aprovisionar.
   
    2. Haga clic en **Guardar**.

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-zscaler-perform-the-following-steps"></a>Para asignar usuarios a Zscaler, lleve a cabo los siguientes pasos:

1. En el Portal de Azure clásico, cree una cuenta de prueba.

2. En la página de integración de la aplicación **Zscaler**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-zscaler-tutorial/IC769495.png "Assign users")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-zscaler-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


