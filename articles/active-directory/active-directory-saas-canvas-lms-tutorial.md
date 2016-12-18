---
title: "Tutorial: Integración de Azure Active Directory con Canvas LMS | Microsoft Docs"
description: "Aprenda cómo usar Canvas LMS con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0031446f8b5202f6627ed02e90c4789f6d434234


---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Tutorial: integración de Azure Active Directory con Canvas LMS
El objetivo de este tutorial es mostrar la integración de Azure y Canvas.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Canvas

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Canvas podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Canvas (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Canvas
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Scenario")

## <a name="enabling-the-application-integration-for-canvas"></a>Habilitación de la integración de aplicaciones para Canvas
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Canvas.

### <a name="to-enable-the-application-integration-for-canvas-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Canvas:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Canvas**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Application Gallery")
7. En el panel de resultados, seleccione **Canvas** y, después, haga clic en **Completar** para agregar la aplicación.
   
   ![Lienzo](./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Canvas")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Canvas con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
La configuración de un inicio de sesión único para Canvas requiere la recuperación de un valor de huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Canvas**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "Configure single sign-on")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Canvas?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Configure Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Canvas**, escriba su dirección URL con el patrón siguiente `https://<tenant-name>.instructure.com` y, después, haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "Configure App URL")
4. En la página **Configurar inicio de sesión único en Canvas**, para descargar el certificado, haga clic en **Descargar certificado** y, después, guarde el archivo de certificado de forma local en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Configure Single Sign-On")
5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Canvas.
6. Vaya a **Cursos \> Cuentas administradas \> Microsoft**.
   
   ![Lienzo](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")
7. En el panel de navegación izquierdo, seleccione **Authentication** (Autenticación) y, después, haga clic en **Add New SAML Config** (Agregar nueva configuración de SAML).
   
   ![Autenticación](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Authentication")
8. En la página Curent Integration (integración actual) realice los siguientes pasos:
   
   ![Integración actual](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Current Integration")
   
   1. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Canvas**, copie el valor de **Id. de entidad** y péguelo en el cuadro de texto **Id. de entidad de IdP**.
   2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Canvas**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.
   3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Canvas**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.
   4. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Canvas**, copie el valor de **Cambiar dirección URL de contraseña** y péguelo en el cuadro de texto **Cambiar vínculo de contraseña**.
   5. Copie el valor de **Huella digital** del certificado exportado y péguelo en el cuadro de texto **Certificate Fingerprint** (Huella digital del certificado).  
      
      > [!TIP]
      > Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI)
      > 
      > 
   6. En la lista **Atributo de inicio de sesión**, seleccione **NameID**.
   7. En la lista **Formato de identificador**, seleccione **emailAddress**.
   8. Haga clic en **Guardar configuración de autenticación**.
9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Canvas, tienen que aprovisionarse en Canvas.  
En el caso de Canvas, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en su inquilino de **Canvas** .
2. Vaya a **Cursos \> Cuentas administradas \> Microsoft**.
   
   ![Lienzo](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")
3. Haga clic en **Usuarios**.
   
   ![Usuarios](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Users")
4. Haga clic en **Add New User**(Agregar nuevo usuario).
   
   ![Usuarios](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Users")
5. En la página de diálogo Agregar nuevo usuario, realice los pasos siguientes:
   
   ![Add User](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Add User")
   
   1. En el cuadro de texto **Full name** (Nombre completo), escriba el nombre del usuario.
   2. En el cuadro de texto **Email** (Correo electrónico), escriba la dirección de correo electrónico del usuario.
   3. En el cuadro de texto **Login** (Inicio de sesión), escriba la dirección de correo electrónico de Azure AD del usuario.
   4. Seleccione **Email the user about this account creation**(Enviar correo electrónico al usuario sobre la creación de esta cuenta).
   5. Haga clic en **Add User**(Agregar usuario).

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Canvas ofrecida por Canvas para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

### <a name="to-assign-users-to-canvas-perform-the-following-steps"></a>Para asignar usuarios a Canvas, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Canvas**, haga clic en **Asignar usuarios**.
   
   ![Asignación de usuarios](./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Assigning users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


