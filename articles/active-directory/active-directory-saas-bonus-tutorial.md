---
title: "Tutorial: Integración de Azure Active Directory con Bonus.ly | Microsoft Docs"
description: "Aprenda cómo usar Bonus.ly con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 25a4e33ab85139bd3f871f46cef1d12a7b9cd06b


---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Tutorial: Integración de Azure Active Directory con Bonus.ly
El objetivo de este tutorial es mostrar la integración de Azure y Bonus.ly. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de prueba en Bonus.ly

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Bonus.ly
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-bonus-tutorial/IC773679.png "Scenario")

## <a name="enabling-the-application-integration-for-bonusly"></a>Habilitación de la integración de aplicaciones para Bonus.ly
El objetivo de esta sección es describir cómo se habilita la integración de las aplicaciones para Bonus.ly.

### <a name="to-enable-the-application-integration-for-bonusly-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Bonus.ly:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Habilitar el inicio de sesión único](./media/active-directory-saas-bonus-tutorial/IC773680.png "Enable single sign-on")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-bonus-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-bonus-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-bonus-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Bonus.ly**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-bonus-tutorial/IC773681.png "Application gallery")
7. En el panel de resultados, seleccione **Bonus.ly** y, después, haga clic en **Completar** para agregar la aplicación.
   
   ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Bonus.ly con su cuenta de Azure AD mediante la federación basada en el protocolo SAML.  
La configuración de un inicio de sesión único para Bonus.ly requiere la recuperación de un valor de huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Bonus.ly**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bonus-tutorial/IC749323.png "Configure single sign-on")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Bonus.ly?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bonus-tutorial/IC773683.png "Configure single sign-on")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **Dirección URL de inquilino de Bonus.ly**, escriba su dirección URL con el patrón siguiente "*https://\<nombre-de-inquilino\>.Bonus.ly*" y, después, haga clic en **Siguiente**: 
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-bonus-tutorial/IC773684.png "Configure app URL")
4. En la página **Configurar inicio de sesión único en Bonus.ly**, haga clic en **Descargar certificado** y, después, guarde el archivo de certificado de forma local en el equipo como **c:\\Bonusly.cer**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-bonus-tutorial/IC773685.png "Configure single sign-on")
5. En una ventana de explorador web diferente, inicie sesión en su inquilino de **Bonus.ly** .
6. En la barra de herramientas de la parte superior, haga clic en **Configuración** y seleccione **Integraciones y aplicaciones**.
   
   ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")
7. En **Inicio de sesión único**, seleccione **SAML**.
8. En la página de diálogo **SAML** , realice los pasos siguientes:
   
   ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")
   
   1. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Bonus.ly**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **URL de destino de SSO de IdP**.
   2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Bonus.ly**, copie el valor de **Id. del emisor** y péguelo en el cuadro de texto **Emisor de IdP**.
   3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Bonus.ly**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **URL de inicio de sesión de IdP**.
   4. Copie el valor de **Huella digital** del certificado exportado y, después, péguelo en el cuadro de texto **Huella digital del certificado**.
      
      > [!TIP]
      > Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI)
      > 
      > 
9. Haga clic en **guardar**.
10. En el Portal de Microsoft Azure clásico, seleccione la confirmación de configuración y, después, haga clic en **Completar** para cerrar el diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-bonus-tutorial/IC773689.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Bonus.ly, tienen que aprovisionarse en Bonus.ly.  
En el caso de Bonus.ly, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. En una ventana de explorador web diferente, inicie sesión en su inquilino de Bonus.ly.
2. Haga clic en **Configuración**
   
   ![Configuración](./media/active-directory-saas-bonus-tutorial/IC781041.png "Settings")
3. Haga clic en la pestaña **Usuarios y bonificaciones** .
   
   ![Usuarios y bonificaciones](./media/active-directory-saas-bonus-tutorial/IC781042.png "Users and bonuses")
4. Haga clic en **Administrar usuarios**.
   
   ![Administrar usuarios](./media/active-directory-saas-bonus-tutorial/IC781043.png "Manage Users")
5. Haga clic en **Agregar usuario**.
   
   ![Agregar usuario](./media/active-directory-saas-bonus-tutorial/IC781044.png "Add User")
6. En el cuadro de diálogo **Agregar usuario** , realice los pasos siguientes:
   
   ![Agregar usuario](./media/active-directory-saas-bonus-tutorial/IC781045.png "Add User")
   
   1. Escriba el “**Correo electrónico**, el **Nombre** y los **Apellidos**” de una cuenta de AAD válida que quiera aprovisionar en los cuadros de texto correspondientes.
   2. Haga clic en **Guardar**.
   
   > [!NOTE]
   > El titular de la cuenta de AAD recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.
   > 
   > 

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Bonus.ly que proporcione Bonus.ly para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

### <a name="to-assign-users-to-bonusly-perform-the-following-steps"></a>Para asignar usuarios a Bonus.ly, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de Bonus.ly, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-bonus-tutorial/IC773690.png "Assign users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-bonus-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


