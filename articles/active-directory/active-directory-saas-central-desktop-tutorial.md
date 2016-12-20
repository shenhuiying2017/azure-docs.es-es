---
title: "Tutorial: Integración de Azure Active Directory con Central Desktop | Microsoft Docs"
description: "Aprenda cómo usar Central Desktop con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5923bf1012c862d500d3c9b0062f0d7662e9f1bc


---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Tutorial: integración de Azure Active Directory con Central Desktop
El objetivo de este tutorial es mostrar la integración de Azure y Central Desktop. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción de Central Desktop con inicio de sesión único habilitado / Inquilino de Central Desktop

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Central Desktop
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Scenario")

## <a name="enabling-the-application-integration-for-central-desktop"></a>Habilitación de la integración de aplicaciones para Central Desktop
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Central Desktop.

### <a name="to-enable-the-application-integration-for-central-desktop-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Central Desktop:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Central Desktop**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Application gallery")
7. En el panel de resultados, seleccione **Central Desktop** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central Desktop")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Central Desktop con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, se requiere cargar un certificado codificado en base 64 en el inquilino de Central Desktop.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Central Desktop**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configure single sign-on")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Central Desktop?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configure single sign-on")
3. En la página **Configurar dirección URL de la aplicación**, realice los pasos siguientes y luego haga clic en **Siguiente**. 
   
   * En el cuadro de texto **URL de inicio de sesión de Central Desktop**, escriba la dirección URL de su inquilino de Central Desktop (por ejemplo, *http://contoso.centraldesktop.com*).
   * En el cuadro de texto dirección URL de respuesta de Central Desktop, escriba la dirección URL de AssertionConsumerService de Central Desktop (por ejemplo, https://contoso.centraldesktop.com/saml2-assertion.php).
   
   > [!NOTE]
   > Puede obtener el valor de los metadatos de Central Desktop (por ejemplo, *http://contoso.centraldesktop.com*).
   > 
   > 
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configure app URL")
4. En la página **Configuración de inicio de sesión único en Central Desktop**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configure single sign-on")
5. Inicie sesión en su inquilino de **Central Desktop** .
6. Vaya a **Configuración**, haga clic en **Avanzadas** y, luego en **Inicio de sesión único**.
   
   ![Programa de instalación: avanzado](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Setup - Advanced")
7. En la página **Configuración de inicio de sesión único** , siga estos pasos:
   
   ![Configuración de inicio de sesión único](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Single Sign On Settings")
   
   1. Seleccione **Enable SAML v2 Single Sign On**(Habilitar inicio de sesión único de SAML).
   2. En el Portal de Azure clásico, en la página **Configurar inicio de sesión único en Central Desktop**, copie el valor de **Dirección URL de emisor** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión único**.
   3. En el Portal de Azure clásico, en la página **Configurar inicio de sesión único en Central Desktop**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión SSO**.
   4. En el Portal de Azure clásico, en la página **Configurar inicio de sesión único en Central Desktop**, copie el valor de **Dirección URL del servicio de cierre de sesión único** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión SSO**.
8. En la sección **Método de autenticación de firma de mensaje** , realice los pasos siguientes:
   
   ![Método de autenticación de firma de mensaje](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Message Signature Verification Method")
   
   1. Seleccione **Certificado**.
   2. En la lista **Certificado SSO**, seleccione **RSH SHA256**.
   3. Cree un archivo de texto a partir del certificado descargado, copie el contenido del archivo de texto y, a continuación, péguelo en el campo **SSO Certificate** (certificado SSO).  
      
      > [!TIP]
      > Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Seleccione **Mostrar un vínculo a la página de inicio de sesión SAMLv2**.
9. Haga clic en **Actualizar**.
10. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para que los usuarios de AAD puedan iniciar sesión, deben aprovisionarse a Central Desktop. En esta sección se describe cómo crear cuentas de usuario de AAD en Central Desktop .

### <a name="to-provision-user-accounts-to-central-desktop"></a>Para el aprovisionamiento de cuentas de usuario a Central Desktop:
1. Inicie sesión en su inquilino de Central Desktop.
2. Vaya a **Contactos \> Miembros internos**.
3. Haga clic en **Agregar miembros internos**.
   
   ![Contactos](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "People")
4. En el cuadro de texto **Dirección de correo electrónico de nuevos miembros**, escriba una cuenta AAD que quiera aprovisionar y luego haga clic en **Siguiente**.
   
   ![Direcciones de correo electrónico de los nuevos miembros](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Email Addresses of New Members")
5. Haga clic en **Agregar miembros internos**.
   
   ![Agregar miembros internos](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Add Internal Member")
   
   > [!NOTE]
   > Los usuarios que ha agregado recibirán un correo electrónico que incluye un vínculo de confirmación en el que tienen que hacer clic para activar la cuenta.
   > 
   > 

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Central Desktop ofrecida por Central Desktop para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-central-desktop-perform-the-following-steps"></a>Para asignar usuarios a Central Desktop, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Central Desktop**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Assign users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


