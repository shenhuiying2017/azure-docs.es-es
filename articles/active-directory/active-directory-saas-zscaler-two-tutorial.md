---
title: "Tutorial: Integración de Azure Active Directory con Zscaler Two | Microsoft Docs"
description: "Aprenda cómo usar Zscaler Two con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1fd8a940-7320-47e0-a176-2dd4eeca6db2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a8925d01def7b4e9b00584602fdd4ecbaecef57c


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Tutorial: Integración de Azure Active Directory con Zscaler Two
El objetivo de este tutorial es mostrar la integración de Azure y ZScaler Two.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en ZScaler Two

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a ZScaler Two podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de ZScaler Two (inicio de sesión iniciado por el proveedor del servicio) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para ZScaler Two
2. Configuración del inicio de sesión único
3. Configuración de los valores de proxy
4. Configuración del aprovisionamiento de usuario
5. Asignación de usuarios

![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-two-tutorial/IC800199.png "Configure Single Sign-On")

## <a name="enabling-the-application-integration-for-zscaler-two"></a>Habilitación de la integración de aplicaciones para ZScaler Two
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para ZScaler Two.

### <a name="to-enable-the-application-integration-for-zscaler-two-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para ZScaler Two:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-zscaler-two-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-zscaler-two-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-zscaler-two-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-zscaler-two-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **ZScaler Two**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-zscaler-two-tutorial/IC800200.png "Application Gallery")
7. En el panel de resultados, seleccione **ZScaler Two** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![ZScaler Two](./media/active-directory-saas-zscaler-two-tutorial/IC800201.png "ZScaler Two")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en ZScaler Two con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
Como parte de este procedimiento, es necesario cargar un certificado codificado en base 64 en su inquilino de ZScaler Two.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **ZScaler Two**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-two-tutorial/IC800202.png "Configure Single Sign-On")
2. En la página **How would you like users to sign on to ZScaler Two** (¿Cómo desea que los usuarios inicien sesión en ZScaler Two?), seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-two-tutorial/IC800203.png "Configure Single Sign-On")
3. En la página **Configurar dirección URL de aplicación**, en el cuadro de texto **URL de inicio de sesión de ZScaler Two**, escriba la dirección URL usada por los usuarios para iniciar sesión en su aplicación ZScaler Two y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-zscaler-two-tutorial/IC800204.png "Configure App URL")
   
   > [!NOTE]
   > Para obtener el valor real para su entorno de su equipo de soporte de ZScaler Two si lo necesita.
   > 
   > 
4. En la página **Configuración de inicio de sesión único en ZScaler Two**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-two-tutorial/IC800205.png "Configure Single Sign-On")
5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de ZScaler Two como administrador.
6. En el menú de la parte superior, haga clic en **Administration**(Administración).
   
   ![Administración](./media/active-directory-saas-zscaler-two-tutorial/IC800206.png "Administration")
7. En **Manage Administrators & Roles** (Administrar administradores y roles) haga clic en **Manage Users & Authentication** (Administrar usuarios y autenticación).
   
   ![Manage Users & Authentication](./media/active-directory-saas-zscaler-two-tutorial/IC800207.png "Manage Users & Authentication")
8. En la sección **Choose Authentication Options for your Organization** (Elegir opciones de autenticación para su organización), lleve a cabo los pasos siguientes:
   
   ![Autenticación](./media/active-directory-saas-zscaler-two-tutorial/IC800208.png "Authentication")
   
   1. Seleccione **Authenticate using SAML Single Sign-On**(Autenticarse mediante el inicio de sesión único SAML).
   2. Haga clic en **Configure SAML Single Sign-On Parameters**(Configurar parámetros de inicio de sesión único SAML).
9. En la página del cuadro de diálogo **Configure SAML Single Sign-On Parameters** (Configurar parámetros de inicio de sesión único SAML), lleve a cabo estos pasos y luego haga clic en el botón **Done** (Hecho).
   
   ![Inicio de sesión único](./media/active-directory-saas-zscaler-two-tutorial/IC800209.png "Single Sign-On")
   
   1. En el Portal de Azure clásico, en la página del cuadro de diálogo **Configurar inicio de sesión único en ZScaler Two**, copie el valor de **Dirección URL de solicitud de autenticación** y péguelo en el cuadro de texto **URL of the SAML Portal to which users are sent for authentication** (URL del portal de SAML al que se envían los usuarios para autenticación).
   2. En el cuadro de texto **Attribute containing Login Name** (Atributo que contiene el nombre de inicio de sesión), escriba **NameID**.
   3. Para cargar el certificado descargado, haga clic en **pem de Zscaler**.
   4. Seleccione **Habilitar aprovisionamiento automático de SAML**.
10. En la página del cuadro de diálogo **Configurar autenticación de usuario** , realice los pasos siguientes:
    
    ![Administración](./media/active-directory-saas-zscaler-two-tutorial/IC800210.png "Administration")
    
    1. Haga clic en **Guardar**.
    2. Haga clic en **Activar ahora**.
11. En el Portal de Azure clásico, en la página del cuadro de diálogo **Configurar inicio de sesión único en ZScaler Two**, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-two-tutorial/IC800211.png "Configure Single Sign-On")

## <a name="configuring-proxy-settings"></a>Configuración de los valores de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir la configuración de proxy en Internet Explorer
1. Inicie **Internet Explorer**.
2. Seleccione **Opciones de Internet** en el menú **Herramientas** para abrir el cuadro de diálogo **Opciones de Internet**.
   
   ![Opciones de Internet](./media/active-directory-saas-zscaler-two-tutorial/IC769492.png "Internet Options")
3. Haga clic en la pestaña **Conexiones** .
   
   ![Conexiones](./media/active-directory-saas-zscaler-two-tutorial/IC769493.png "Connections")
4. Haga clic en **Configuración de LAN** para abrir el cuadro de diálogo **Configuración de LAN**.
5. En la sección del servidor proxy, lleve a cabo estos pasos:
   
   ![Servidor proxy](./media/active-directory-saas-zscaler-two-tutorial/IC769494.png "Proxy server")
   
   1. Seleccione Usar un servidor proxy para la LAN.
   2. En el cuadro de texto Dirección, escriba **gateway.zscalerone.net**.
   3. En el cuadro de texto Puerto, escriba **80**.
   4. Seleccione **No usar servidor proxy para direcciones locales**.
   5. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Configuración de red de área local (LAN)**.
6. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Opciones de Internet**.

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en ZScaler Two, deben aprovisionarse a ZScaler Two.  
En el caso de ZScaler Two, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en su inquilino de **Zscaler** .
2. Haga clic en **Administración**.
   
   ![Administración](./media/active-directory-saas-zscaler-two-tutorial/IC781035.png "Administration")
3. Haga clic en **User Management**(Administración de usuarios).
   
   ![Sumar](./media/active-directory-saas-zscaler-two-tutorial/IC781037.png "Add")
4. En la pestaña **Users** (Usuarios) haga clic en **Add** (Agregar).
   
   ![Sumar](./media/active-directory-saas-zscaler-two-tutorial/IC781037.png "Add")
5. En la sección Agregar usuario, lleve a cabo estos pasos:
   
   ![Agregar usuario](./media/active-directory-saas-zscaler-two-tutorial/IC781038.png "Add User")
   
   1. Escriba los valores para **UserID**, **User Display Name** (Nombre de usuario para mostrar), **Password** (Contraseña), **Confirm Password** (Confirmar contraseña) y luego seleccione **Groups** (Grupos) y el **departamento** de una cuenta válida de AAD que quiera aprovisionar.
   2. Haga clic en **Guardar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de ZScaler Two ofrecida por ZScaler Two para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-zscaler-two-perform-the-following-steps"></a>Para asignar usuarios a ZScaler Two, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **ZScaler Two**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-zscaler-two-tutorial/IC800212.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-zscaler-two-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


