---
title: "Tutorial: Integración de Azure Active Directory con Rally Software | Microsoft Docs"
description: "Aprenda cómo usar Rally Software con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c71cf608f162b0af04a436892dde9df3f80b8c02


---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Tutorial: Integración de Azure Active Directory con Rally Software
El objetivo de este tutorial es mostrar la integración de Azure y Rally Software.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Rally Software

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Rally Software
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-rally-software-tutorial/IC769525.png "Scenario")

## <a name="enabling-the-application-integration-for-rally-software"></a>Habilitación de la integración de aplicaciones para Rally Software
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Rally Software.

### <a name="to-enable-the-application-integration-for-rally-software-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Rally Software:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-rally-software-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-rally-software-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-rally-software-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **rally**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-rally-software-tutorial/IC769526.png "Application gallery")
7. En el panel de resultados, seleccione **Rally Software** y haga clic en **Completar** para agregar la aplicación.
   
   ![Rally Software](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Rally Software con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
Como parte de este procedimiento, es necesario cargar un certificado en Rally Software.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Rally Software**, haga clic en** Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configure single sign-on")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Rally?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
   ![Inicio de sesión único de Microsoft Azure AD](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure AD Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **Dirección URL de inquilino de Rally Software**, escriba su dirección URL con el siguiente patrón "*https://\<nombre-inquilino\>.rally.com*" y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configure App URL")
4. En la página **Configuración de inicio de sesión único en Rally** , haga clic en Descargar metadatos y luego guarde el archivo de metadatos en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configure single sign-on")
5. Inicie sesión en su inquilino de **Rally Software** .
6. En la barra de herramientas de la parte superior, haga clic en **Configuración** y seleccione **Suscripción**.
   
   ![suscripción](./media/active-directory-saas-rally-software-tutorial/IC769531.png "Subscription")
7. Haga clic en el botón **Acción** de la barra de herramientas de la parte superior del lado derecho y seleccione **Editar suscripción**.
8. En la página de diálogo **Suscripción**, realice los pasos siguientes y haga clic en **Guardar y cerrar**:
   
   ![Autenticación](./media/active-directory-saas-rally-software-tutorial/IC769542.png "Authentication")
   
   1. Seleccione **Rally o autenticación de SSO** en la lista desplegable de autenticación.
   2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Rally Software**, copie el valor del **Id. de proveedor de identidad** y péguelo en el cuadro de texto **Identity Provider URL** (URL del proveedor de identidades).
   3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Rally Software**, copie el valor de **Dirección URL de cierre de sesión remoto**.
9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para que los usuarios de AAD puedan inician sesión, deben aprovisionarse para la aplicación Rally Software con sus nombres de usuario de Azure Active Directory.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en su inquilino de Rally Software.
2. Vaya a **Configuración \> USUARIOS** y haga clic en **+ Agregar nuevo**.
   
   ![Usuarios](./media/active-directory-saas-rally-software-tutorial/IC781039.png "Users")
3. Escriba el nombre en el cuadro de texto Nuevo usuario y luego haga clic en **Agregar con detalles**.
4. En la sección **Crear usuario** , lleve a cabo estos pasos:
   
   ![Crear usuario](./media/active-directory-saas-rally-software-tutorial/IC781040.png "Create User")
   
   1. En el cuadro de texto **Nombre de usuario** , escriba el nombre del usuario de Azure AD que quiera aprovisionar.
   2. En el cuadro de texto **Dirección de correo electrónico** , escriba la dirección de correo electrónico del usuario de Azure AD que quiera aprovisionar.
   3. Haga clic en **Guardar y cerrar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Rally Software ofrecida por Rally Software para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-rally-software-perform-the-following-steps"></a>Para asignar usuarios a Rally Software, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Rally Software**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-rally-software-tutorial/IC769548.png "Assign users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-rally-software-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


