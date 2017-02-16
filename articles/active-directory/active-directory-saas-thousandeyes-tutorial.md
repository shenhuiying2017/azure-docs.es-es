---
title: "Tutorial: integración de Azure Active Directory con ThousandEyes | Microsoft Docs"
description: "Aprenda cómo usar ThousandEyes con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 28cd9db7e49328298f2be5788b6d94baaafb6651


---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Tutorial: integración de Azure Active Directory con ThousandEyes
El objetivo de este tutorial es mostrar cómo configurar el inicio de sesión único entre Azure Active Directory (Azure AD) y ThousandEyes.

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para inicio de sesión único en ThousandEyes

Después de completar este tutorial, los usuarios de AAD a los que ha asignado acceso a ThousandEyes podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de ThousandEyes (inicio de sesión iniciado por el proveedor del servicio) o con el Panel de acceso de AAD.

1. Habilitación de la integración de aplicaciones para ThousandEyes
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-thousandeyes-tutorial/IC790059.png "Scenario")

## <a name="enabling-the-application-integration-for-thousandeyes"></a>Habilitación de la integración de aplicaciones para ThousandEyes
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para ThousandEyes.

### <a name="to-enable-the-application-integration-for-thousandeyes-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para ThousandEyes:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-thousandeyes-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-thousandeyes-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-thousandeyes-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-thousandeyes-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **ThousandEyes**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-thousandeyes-tutorial/IC790060.png "Application Gallery")
7. En el panel de resultados, seleccione **ThousandEyes** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![ThousandEyes](./media/active-directory-saas-thousandeyes-tutorial/IC790061.png "ThousandEyes")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
En esta sección se describe cómo se habilita la autenticación de los usuarios en ThousandEyes con su cuenta de Azure Active Directory usando el protocolo SAML basado en la federación.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **ThousandEyes**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-thousandeyes-tutorial/IC790062.png "Configure Single SignOn")
2. En la página **¿Cómo desea que los usuarios inicien sesión en ThousandEyes?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-thousandeyes-tutorial/IC790063.png "Configure Single SignOn")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de ThousandEyes**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación ThousandEyes (por ejemplo: "*https://app.thousandeyes.com/login/sso*") y, después, haga clic en **Siguiente**. 
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-thousandeyes-tutorial/IC790064.png "Configure App URL")
4. En la página **Configurar inicio de sesión único en ThousandEyes**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-thousandeyes-tutorial/IC790065.png "Configure Single SignOn")
5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de **ThousandEyes** como administrador.
6. En el menú de la parte superior, haga clic en **Configuración**.
   
   ![Configuración](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Settings")
7. Haga clic en **Cuenta**
   
   ![Cuenta](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Account")
8. Haga clic en la pestaña **Security & Authentication** (Seguridad y autenticación).
   
   ![Seguridad y autenticación](./media/active-directory-saas-thousandeyes-tutorial/IC790068.png "Security & Authentication")
9. En la sección **Configurar inicio de sesión único** siga los pasos siguientes:
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-thousandeyes-tutorial/IC790069.png "Setup Single Sign-On")
   
   1. Seleccione **Enable Single Sign-On**(Habilitar inicio de sesión único).
   2. En el Portal de Microsoft Azure clásico, en la página de diálogo **Configurar inicio de sesión único en ThousandEyes**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Login Page URL** (URL de la página de inicio de sesión).
   3. En el Portal de Microsoft Azure clásico, en la página de diálogo **Configurar inicio de sesión único en ThousandEyes**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Login Page URL** (URL de página de cierre de sesión).
   4. En el Portal de Microsoft Azure clásico, en la página **Configurar inicio de sesión único en ThousandEyes**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Identity Provider Issuer** (Emisor de proveedor de identidades).
   5. En **Identity Provider Certificate** (Certificado del proveedor de identidades), haga clic en **Choose file** (Elegir archivo) y después cargue el certificado que ha descargado del Portal de Microsoft Azure clásico.
   6. Haga clic en **Guardar**.
10. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-thousandeyes-tutorial/IC790070.png "Configure Single SignOn")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en ThousandEyes, deben aprovisionarse en ThousandEyes.  
En el caso de ThousandEyes, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-account-to-thousandeyes-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario a ThousandEyes, realice los siguientes pasos:
1. Inicie sesión en su sitio de la compañía de ThousandEyes como administrador.
2. Haga clic en **Configuración**.
   
   ![Configuración](./media/active-directory-saas-thousandeyes-tutorial/IC790066.png "Settings")
3. Haga clic en **Cuenta**.
   
   ![Cuenta](./media/active-directory-saas-thousandeyes-tutorial/IC790067.png "Account")
4. Haga clic en la pestaña **Accounts & Users** (Cuentas y usuarios).
   
   ![Cuentas y usuarios](./media/active-directory-saas-thousandeyes-tutorial/IC790073.png "Accounts & Users")
5. En la sección **Add Users & Accounts** (Agregar usuarios y cuentas), realice los siguientes pasos:
   
   ![Agregar cuentas de usuario](./media/active-directory-saas-thousandeyes-tutorial/IC790074.png "Add User Accounts")
   
   1. Escriba el **nombre**, **correo electrónico** y otros detalles de una cuenta de Azure Active Directory válida que desee aprovisionar en los cuadros de texto pertinentes.
   2. Haga clic en **Agregar nuevo usuario a la cuenta**.
      
      > [!NOTE]
      > El titular de la cuenta de AAD recibirá un mensaje de correo electrónico junto con un vínculo para confirmar y activar la cuenta.
      > 
      > 

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de ThousandEyes ofrecida por ThousandEyes para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-thousandeyes-perform-the-following-steps"></a>Para asignar usuarios a ThousandEyes, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **ThousandEyes**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-thousandeyes-tutorial/IC790075.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-thousandeyes-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


