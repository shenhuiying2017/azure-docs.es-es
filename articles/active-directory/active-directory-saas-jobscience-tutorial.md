---
title: "Tutorial: Integración de Azure Active Directory con Jobscience | Microsoft Docs"
description: "Aprenda a usar Jobscience con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e4e665ced0fa4e2749c052c2cd087ffb0ee698cb


---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Tutorial: Integración de Azure Active Directory con Jobscience
El objetivo de este tutorial es mostrar la integración de Azure y Jobscience.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para inicio de sesión único en Jobscience

Después de completar este tutorial, los usuarios de Azure AD asignados a Jobscience podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Jobscience (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Jobscience
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-jobscience-tutorial/IC784341.png "Scenario")

## <a name="enabling-the-application-integration-for-jobscience"></a>Habilitación de la integración de aplicaciones para Jobscience
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Jobscience.

### <a name="to-enable-the-application-integration-for-jobscience-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Jobscience:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-jobscience-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-jobscience-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-jobscience-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **jobscience**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-jobscience-tutorial/IC784342.png "Application Gallery")
7. En el panel de resultados, seleccione **Jobscience** y, después, haga clic en **Completar** para agregar la aplicación.
   
   ![jobscience](./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Jobscience con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
La configuración de un inicio de sesión único para Jobscience requiere la recuperación de un valor de huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. Inicie sesión como administrador en el sitio de la compañía de Jobscience.
2. Acceda a **Setup**(Configuración).
   
   ![Configuración](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Setup")
3. En el panel de navegación izquierdo, en la sección **Administer** (Administrar), haga clic en **Domain Management** (Administración de dominios) para expandir la sección relacionada y, luego, haga clic en **My Domain** (Mi dominio) para abrir la página **My Domain** (Mi dominio). 
   
   ![Mi dominio](./media/active-directory-saas-jobscience-tutorial/IC767825.png "My Domain")
4. Para comprobar que el dominio se ha configurado correctamente, asegúrese de que está en "**Step 4 Deployed to Users**” (Paso 4 Dominio implementado para usuarios) y revise la sección "**My Domain Settings**" (Mi configuración de dominio).
   
   ![Dominio implementado al usuario](./media/active-directory-saas-jobscience-tutorial/IC784377.png "Doman Deployed to User")
5. En otra ventana del explorador web, inicie sesión en el Portal de Azure clásico.
6. En la página de integración de aplicaciones de **Jobscience**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configure Single Sign-On")
7. En la página **¿Cómo desea que los usuarios inicien sesión en Jobscience?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configure Single Sign-On")
8. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de Jobscience**, escriba su dirección URL con el siguiente patrón *http://company.my.salesforce.com* y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-jobscience-tutorial/IC784362.png "Configure App URL")
9. En la página **Configurar inicio de sesión único en Jobscience**, para descargar el certificado, haga clic en **Descargar certificado** y, luego, guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configure Single Sign-On")
10. En el sitio de la compañía de Jobscience, haga clic en **Security Controls** (Controles de seguridad) y, a continuación, haga clic en **Single Sign-On Settings** (Configuración de inicio de sesión único).
    
    ![Controles de seguridad](./media/active-directory-saas-jobscience-tutorial/IC784364.png "Security Controls")
11. En la sección **Configuración del inicio de sesión único** , siga estos pasos:
    
    ![Single Sign-On Settings](./media/active-directory-saas-jobscience-tutorial/IC781026.png "Single Sign-On Settings")
    
    1. Seleccione **SAML habilitado**.
    2. Haga clic en **Nuevo**.
12. En el cuadro de diálogo **SAML Single Sign-On Setting Edit** (Edición de la configuración de inicio de sesión único de SAML), realice los pasos siguientes:
    
    ![Configuración de inicio de sesión único SAML](./media/active-directory-saas-jobscience-tutorial/IC784365.png "SAML Single Sign-On Setting")
    
    1. En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración.
    2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Jobscience**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Emisor**.
    3. En el cuadro de texto **Id. de entidad**, escriba **https://salesforce-jobscience.com**.
    4. Haga clic en **Examinar** para cargar el certificado de Azure AD.
    5. Como **///SAML Identity Type** (Tipo de identidad SAML), seleccione **///Assertion contains the Federation ID from the User object** (La aserción contiene el id. de federación del objeto Usuario).
    6. Para **///SAML Identity Location** (Ubicación de identidad SAML), seleccione **///Identity is in the NameIdentfier element of the Subject statement** (La identidad está en el elemento NameIdentifier de la instrucción Subject).
    7. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Jobscience**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión del proveedor de identidades**.
    8. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Jobscience**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión del proveedor de identidades**.
    9. Haga clic en **Guardar**.
13. En el panel de navegación izquierdo, en la sección **Administer** (Administrar), haga clic en **Domain Management** (Administración de dominios) para expandir la sección relacionada y, luego, haga clic en **My Domain** (Mi dominio) para abrir la página **My Domain** (Mi dominio). 
    
    ![Mi dominio](./media/active-directory-saas-jobscience-tutorial/IC767825.png "My Domain")
14. En la página **My domain** (Mi dominio), en la sección **Login Page Branding** (Personalización de marca de la página de inicio de sesión), haga clic en **Edit** (Editar).
    
    ![Personalización de marca de la página de inicio de sesión](./media/active-directory-saas-jobscience-tutorial/IC767826.png "Login Page Branding")
15. En la página **Login Page Branding** (Personalización de marca de la página de inicio de sesión), en la sección **Authentication Service** (Servicio de autenticación), se muestra el nombre de su **SAML SSO Settings** (Configuración de SSO de SAML). Selecciónelo y luego haga clic en **Save**(Guardar).
    
    ![Personalización de marca de la página de inicio de sesión](./media/active-directory-saas-jobscience-tutorial/IC784366.png "Login Page Branding")
16. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configure Single Sign-On")

Para obtener la dirección URL de inicio de sesión único iniciado por el proveedor de servicios, haga clic en **Configuración de inicio de sesión único** en la sección del menú **Controles de seguridad**.

![Controles de seguridad](./media/active-directory-saas-jobscience-tutorial/IC784368.png "Security Controls")

Haga clic en el perfil SSO creado en el paso anterior.  
En esta página se muestra la URL de inicio de sesión único de la empresa (por ejemplo, *https://nombreDeLaEmpresa.my.salesforce.com?so=idEmpresa*).

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en Jobscience, deben aprovisionarse en Jobscience.  
En el caso de Jobscience, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión como administrador en el sitio de la compañía de **Jobscience** .
2. Ir a Configuración
   
   ![Configuración](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Setup")
3. Vaya a **Manage Users \> (Administrar usuarios) Users** (Usuarios).
   
   ![Usuarios](./media/active-directory-saas-jobscience-tutorial/IC784369.png "Users")
4. Haga clic en **Nuevo usuario**.
   
   ![Todos los usuarios](./media/active-directory-saas-jobscience-tutorial/IC784370.png "All Users")
5. En el cuadro de diálogo **Editar usuario** , realice los siguientes pasos:
   
   ![Edición de usuarios](./media/active-directory-saas-jobscience-tutorial/IC784371.png "User Edit")
   
   1. En los cuadros de texto relacionados, escriba el nombre, los apellidos, el alias, el correo electrónico, las propiedades de nombre y el alias del usuario de Azure AD que desee aprovisionar.
   2. Haga clic en **Guardar**.
   
   > [!NOTE]
   > El titular de la cuenta de Azure AD recibirá un mensaje de correo electrónico que incluye un vínculo para confirmar la cuenta antes de que se active.
   > 
   > 

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Jobscience ofrecida por Jobscience para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

### <a name="to-assign-users-to-jobscience-perform-the-following-steps"></a>Para asignar usuarios a Jobscience, siga estos pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Jobscience**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-jobscience-tutorial/IC784372.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-jobscience-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


