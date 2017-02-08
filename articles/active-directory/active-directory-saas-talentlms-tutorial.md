---
title: "Tutorial: integración de Azure Active Directory con TalentLMS | Microsoft Docs"
description: "Aprenda cómo usar TalentLMS con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 49352a5e8255468bbc54c02e0cd9242d49002dbd
ms.openlocfilehash: 7e1f1ccd109a2777f6d5df96b5f5d02eefc4b841


---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Tutorial: integración de Azure Active Directory con TalentLMS
El objetivo de este tutorial es mostrar la integración de Azure y TalentLMS.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de TalentLMS

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a TalentLMS podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de TalentLMS (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para TalentLMS
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-talentlms-tutorial/IC777289.png "Escenario")

## <a name="enabling-the-application-integration-for-talentlms"></a>Habilitación de la integración de aplicaciones para TalentLMS
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en TalentLMS.

### <a name="to-enable-the-application-integration-for-talentlms-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones en TalentLMS:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-talentlms-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Aplicaciones](./media/active-directory-saas-talentlms-tutorial/IC700994.png "Aplicaciones")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicaciones](./media/active-directory-saas-talentlms-tutorial/IC749321.png "Agregar aplicaciones")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-talentlms-tutorial/IC749322.png "Agregar una aplicación de la galería")

6. En el **cuadro de búsqueda**, escriba **TalentLMS**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-talentlms-tutorial/IC777290.png "Galería de aplicaciones")

7. En el panel de resultados, seleccione **TalentLMS** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![TalentLMS](./media/active-directory-saas-talentlms-tutorial/IC777291.png "TalentLMS")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en TalentLMS con su cuenta de Azure AD mediante el protocolo SAML basado en la federación. .  
La configuración de un inicio de sesión único para TalentLMS requiere la recuperación de un valor de huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **TalentLMS**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-talentlms-tutorial/IC777292.png "Configurar inicio de sesión único")

2. En la página **¿Cómo desea que los usuarios inicien sesión en TalentLMS?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-talentlms-tutorial/IC777293.png "Configurar inicio de sesión único")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **Dirección URL de inicio de sesión de TalentLMS**, escriba su dirección URL con el siguiente patrón "*https://\<nombreDeInquilino>\>.TalentLMSapp.com*" y haga clic en **Siguiente**.
   
    ![URL de inicio de sesión](./media/active-directory-saas-talentlms-tutorial/IC777294.png "URL de inicio de sesión")

4. En la página **Configurar inicio de sesión único en TalentLMS**, para descargar el certificado, haga clic en **Descargar certificado** y guarde el archivo de certificado localmente como **c:\\TalentLMS.cer**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-talentlms-tutorial/IC777295.png "Configurar inicio de sesión único")

5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de TalentLMS como administrador.

6. En la sección **Account & Settings** (Cuenta y configuración), haga clic en la pestaña **Users** (Usuarios).
   
    ![Cuenta y configuración](./media/active-directory-saas-talentlms-tutorial/IC777296.png "Cuenta y configuración")

7. Haga clic en **Inicio de sesión único (SSO)**.

8. En la sección Inicio de sesión único, siga estos pasos:
   
    ![Inicio de sesión único](./media/active-directory-saas-talentlms-tutorial/IC777297.png "Inicio de sesión único")
   
    a. En la lista **SSO integration type** (Tipo de integración de SSO), seleccione **SAML 2.0**.
   
    b. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en TalentLMS**, copie el valor de **Id. de proveedor de identidad** y péguelo en el cuadro de texto **Identity provider (IdP)** (Proveedor de identidades [IdP]).
   
    c. Copie el valor de **Huella digital** del certificado exportado y péguelo en el cuadro de texto **Certificate Fingerprint** (Huella digital del certificado).
      
    > [!TIP]
    > Para más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI)
    > 
    > 
   
    d. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en TalentLMS**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Remote sign-in URL** (URL de inicio de sesión remoto).
   
    e. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en TalentLMS**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Remote sign-out URL** (URL de cierre de sesión remoto).
   
    f. En el cuadro de texto **TargetedID** (ID dirigido), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**
   
    g. En el cuadro de texto **Name** (Nombre), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
   
    h. En el cuadro de texto **Last Name** (Apellidos), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
   
    i. En el cuadro de texto **Email** (Correo electrónico), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**
   
    j. Haga clic en **Guardar**.

9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-talentlms-tutorial/IC777298.png "Configurar inicio de sesión único")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en TalentLMS, deben aprovisionarse en TalentLMS.  
En el caso de TalentLMS, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en su inquilino de **TalentLMS** .

2. Haga clic en **Users** (Usuarios) y, luego, en **Add User** (Agregar usuario).

3. En la página del cuadro de diálogo **Agregar usuario** , realice los siguientes pasos:
   
    ![Agregar usuario](./media/active-directory-saas-talentlms-tutorial/IC777299.png "Agregar usuario")
   
    a. Escriba los valores de atributo relacionados de la cuenta de usuario de Azure AD en los siguientes cuadros de texto: **First Name** (Nombre), **Last Name** (Apellidos), **Email address** (Dirección de correo electrónico).
   
    b. Haga clic en **Agregar usuario**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de TalentLMS ofrecida por TalentLMS para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-talentlms-perform-the-following-steps"></a>Para asignar usuarios a TalentLMS, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.

2. En la página de integración de la aplicación **TalentLMS**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-talentlms-tutorial/IC777300.png "Asignar usuarios")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-talentlms-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Dec16_HO2-->


