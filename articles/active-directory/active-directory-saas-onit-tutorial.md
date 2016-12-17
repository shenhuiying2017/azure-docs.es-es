---
title: "Tutorial: Integración de Azure Active Directory con Onit | Microsoft Docs"
description: "Aprenda a usar Onit con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 80c727ba687e11f7407339b57bc1f6b0c091dd0f


---
# <a name="tutorial-azure-active-directory-integration-with-onit"></a>Tutorial: Integración de Azure Active Directory con Onit
El objetivo de este tutorial es mostrar la integración de Azure y Onit.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en Onit

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Onit podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía Onit (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Onit
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-onit-tutorial/IC791166.png "Scenario")

## <a name="enabling-the-application-integration-for-onit"></a>Habilitación de la integración de aplicaciones para Onit
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Onit.

### <a name="to-enable-the-application-integration-for-onit-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Onit:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-onit-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-onit-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-onit-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-onit-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Onit**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-onit-tutorial/IC791167.png "Application Gallery")
7. En el panel de resultados, seleccione **Onit** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![Onit](./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta El objetivo de esta sección es describir cómo se habilita la autenticación de usuarios en Onit con su cuenta de Azure AD mediante la federación basada en el protocolo SAML.  
La configuración de un inicio de sesión único para Onit requiere la recuperación de un valor de huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

La aplicación Onit espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML** .  
La siguiente captura de pantalla le muestra un ejemplo de esto.

![Inicio de sesión único](./media/active-directory-saas-onit-tutorial/IC791168.png "Single Sign-On")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Onit**, en el menú de la parte superior, haga clic en **Atributos** para abrir el cuadro de diálogo **Atributos de token de SAML**.
   
   ![Atributos](./media/active-directory-saas-onit-tutorial/IC791169.png "Attributes")
2. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:

    |Nombre del atributo|Valor de atributo|
    |---|---|
    |name|User.userprincipalname|
    |email|User.mail|

    1.  En cada fila de datos de la tabla anterior, haga clic en **agregar atributo de usuario**.
    2.  En el cuadro de texto **Nombre de atributo** , escriba el nombre de atributo que se muestra para la fila.
    3.  En la lista **Valor de atributo** , seleccione el valor de atributo que se muestra para esa fila.
    4.  Haga clic en **Completo**.

1. Haga clic en **Aplicar cambios**.
2. En el explorador, haga clic en **Atrás** para volver a abrir el cuadro de diálogo **Inicio rápido**.
3. Para abrir el cuadro de diálogo **Configurar inicio de sesión único**, haga clic en **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-onit-tutorial/IC791170.png "Configure Single Sign-On")
4. En la página **¿Cómo desea que los usuarios inicien sesión en Onit?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-onit-tutorial/IC791171.png "Configure Single Sign-On")
5. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Onit**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación Onit (por ejemplo: "*https://ms-sso-test-onit.com*") y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-onit-tutorial/IC791172.png "Configure App URL")
6. En la página **Configurar inicio de sesión único en Onit**, para descargar el certificado, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-onit-tutorial/IC791173.png "Configure Single Sign-On")
7. En otra ventana del explorador web, inicie sesión en como administrador en el sitio de Onit de la compañía.
8. En el menú de la parte superior, haga clic en **Administration**(Administración).
   
   ![Administration](./media/active-directory-saas-onit-tutorial/IC791174.png "Administration")
9. Haga clic en **Edit Corporation**(Editar corporación).
   
   ![Edit Corporation](./media/active-directory-saas-onit-tutorial/IC791175.png "Edit Corporation")
10. Haga clic en la pestaña **Security** (Seguridad).
    
    ![Editar información de la compañía](./media/active-directory-saas-onit-tutorial/IC791176.png "Edit Company Information")
11. En la pestaña **Seguridad** , lleve a cabo estos pasos:
    
    ![Inicio de sesión único](./media/active-directory-saas-onit-tutorial/IC791177.png "Single Sign-On")
    
    1. Como **Estrategia de autenticación**, seleccione **Inicio de sesión único y contraseña**.
    2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Onit**, copie el valor de **URL de inicio de sesión remoto** y péguelo en el cuadro de texto **URL de destino de IdP**.
    3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Onit**, copie el valor de **URL de cierre de sesión remoto** y péguelo en el cuadro de texto **URL de cierre de sesión de IdP**.
    4. Copie el valor de **Huella digital** del certificado exportado y luego péguelo en el cuadro de texto **Huella digital del certificado de Idp (SHA1)**.  
       
       > [!TIP]
       > Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI)
       > 
       > 
    5. Como **Tipo de inicio de sesión único**, seleccione **SAML**.
    6. En el cuadro **Texto de botón de inicio de sesión SSO** , escriba el texto que desee para el botón.
    7. Seleccione **Inicio de sesión con SSO: necesario para los siguientes dominios o usuarios**. Escriba la dirección de correo electrónico de un usuario de prueba en el cuadro de texto relacionado y luego haga clic en **Actualizar**.
       ![Edit Corporation](./media/active-directory-saas-onit-tutorial/IC791178.png "Edit Corporation")
12. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-onit-tutorial/IC791179.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Onit, tienen que aprovisionarse en Onit.  
En el caso de Onit, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en su sitio de la compañía de **Onit** como administrador.
2. Haga clic en **Add User**(Agregar usuario).
   
   ![Administration](./media/active-directory-saas-onit-tutorial/IC791180.png "Administration")
3. En la página del cuadro de diálogo **Add User** (Agregar usuario), realice los siguientes pasos:
   
   ![Agregar usuario](./media/active-directory-saas-onit-tutorial/IC791181.png "Add User")
   
   1. Escriba el **Nombre** y la **Dirección de correo electrónico** de una cuenta de AAD válida que quiera aprovisionar en los cuadros de texto relacionados.
   2. Haga clic en **Crear**.  
      
      > [!NOTE]
      > El propietario de la cuenta recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.
      > 
      > 

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Onit que proporcione Onit para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-onit-perform-the-following-steps"></a>Para asignar usuarios a Onit, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones **Onit**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-onit-tutorial/IC791182.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-onit-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


