---
title: "Tutorial: Integración de Azure Active Directory con UserVoice | Microsoft Docs"
description: "Aprenda cómo usar UserVoice con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: 592fc838645dbc05b710b1bd83fd7c0135688844
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Tutorial: integración de Azure Active Directory con UserVoice
El objetivo de este tutorial es mostrar la integración de Azure y UserVoice.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de UserVoice

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a UserVoice podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de UserVoice (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones en UserVoice
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-uservoice-tutorial/IC777514.png "Escenario")

## <a name="enabling-the-application-integration-for-uservoice"></a>Habilitación de la integración de aplicaciones en UserVoice
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para UserVoice.

### <a name="to-enable-the-application-integration-for-uservoice-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para UserVoice:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Aplicaciones](./media/active-directory-saas-uservoice-tutorial/IC700994.png "Aplicaciones")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicaciones](./media/active-directory-saas-uservoice-tutorial/IC749321.png "Agregar aplicaciones")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-uservoice-tutorial/IC749322.png "Agregar una aplicación de la galería")

6. En el **cuadro de búsqueda**, escriba **UserVoice**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-uservoice-tutorial/IC777513.png "Galería de aplicaciones")

7. En el panel de resultados, seleccione **UserVoice** y luego haga clic en **Completar** para agregar la aplicación.
   
    ![UserVoice](./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en UserVoice con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
La configuración de un inicio de sesión único para UserVoice requiere la recuperación de un valor de huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **UserVoice**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-uservoice-tutorial/IC777515.png "Configurar inicio de sesión único")

2. En la página **How would you like users to sign on to UserVoice** (¿Cómo desea que los usuarios inicien sesión en UserVoice), seleccione **Inicio de sesión único de Microsoft Azure AD** y, a continuación, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-uservoice-tutorial/IC777516.png "Configurar inicio de sesión único")

3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **Dirección URL de inicio de sesión de UserVoice**, escriba la URL con el siguiente patrón: *https://\<nombre de inquilino\>UserVoice.com*" y, a continuación, haga clic en **Siguiente**.
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-uservoice-tutorial/IC777517.png "Configurar dirección URL de la aplicación")

4. En la página **Configurar inicio de sesión único en UserVoice**, para descargar su certificado, haga clic en**Descargar certificado** y guarde el archivo de certificado localmente como **c:\\UserVoice.cer**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-uservoice-tutorial/IC777518.png "Configurar inicio de sesión único")

5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de UserVoice como administrador.

6. En la barra de herramientas de la parte superior, haga clic en Configuración y luego el portal web en el menú.
   
    ![Configuración](./media/active-directory-saas-uservoice-tutorial/IC777519.png "Configuración")

7. En la pestaña **Portal web**, en la sección **Autenticación del usuario**, haga clic en **Editar** para abrir la página del cuadro de diálogo **Editar autenticación de usuario**
   
    ![Portal web](./media/active-directory-saas-uservoice-tutorial/IC777520.png "Portal web")

8. En la página de diálogo **Editar autenticación del usuario** , realice los pasos siguientes:
   
    ![Editar autenticación de usuario](./media/active-directory-saas-uservoice-tutorial/IC777521.png "Editar autenticación de usuario")
   
    a. Haga clic en **Inicio de sesión único (SSO)**.
 
    b. En el Portal de Azure clásico, en la página del cuadro de diálogo **Configurar inicio de sesión único en UserVoice**, copie el valor de **Dirección URL de inicio de sesión remoto** y, a continuación, péguelo en el cuadro de texto **SSO Remote Sign-In** (Inicio de sesión remoto de SSO).

    c. En el Portal de Azure clásico, en la página de cuadro de diálogo **Configurar inicio de sesión único en UserVoice**, copie el valor de **Dirección URL de cierre de sesión remoto** y, a continuación, péguelo en el cuadro de texto **SSO Remote Sign-In ** (Cierre de sesión remoto de SSO).
 
    d. Copie el valor de **Huella digital** del certificado exportado y, a continuación, péguelo en el cuadro de texto **Current certificate SHA1 fingerprint ** (Huella digital de SHA1 del certificado actual).  
      
    > [!TIP]
    > Para más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI)
    > 
    > 
  
    e. Haga clic en **Guardar configuración de autenticación**.

9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-uservoice-tutorial/IC777522.png "Configurar inicio de sesión único")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en UserVoice, deben aprovisionarse en UserVoice.  
En el caso de UserVoice, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en su inquilino de **UserVoice** .

2. Vaya a **Configuración**.
   
    ![Configuración](./media/active-directory-saas-uservoice-tutorial/IC777811.png "Configuración")

3. Haga clic en **General**.

4. Haga clic en **Agentes y permisos**.
   
    ![Agentes y permisos](./media/active-directory-saas-uservoice-tutorial/IC777812.png "Agentes y permisos")

5. Haga clic en **Agregar administradores**.
   
    ![Agregar administradores](./media/active-directory-saas-uservoice-tutorial/IC777813.png "Agregar administradores")

6. En el cuadro de diálogo **Invitar a administradores** , realice los pasos siguientes:
   
    ![Invitar a administradores](./media/active-directory-saas-uservoice-tutorial/IC777814.png "Invitar a administradores")
   
    a. En el cuadro de texto de correos electrónicos, escriba la dirección de correo electrónico de la cuenta que quiere aprovisionar y luego haga clic en **Agregar**.
   
    b. Haga clic en **Invitar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de UserVoice ofrecida por UserVoice para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-uservoice-perform-the-following-steps"></a>Para asignar usuarios a UserVoice, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **UserVoice**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-uservoice-tutorial/IC777523.png "Asignar usuarios")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-uservoice-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


