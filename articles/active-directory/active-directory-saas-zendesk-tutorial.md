---
title: "Tutorial: Integración de Azure Active Directory con Zendesk | Microsoft Docs"
description: "Aprenda cómo usar Zendesk con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: 4ef08fb592ff8558fa779d628945d14144dc09b7
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Tutorial: Integración de Azure Active Directory con Zendesk
El objetivo de este tutorial es mostrar la integración de Azure y Zendesk.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Zendesk

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Zendesk podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Zendesk (inicio de sesión iniciado por el proveedor del servicio) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Zendesk
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-zendesk-tutorial/IC773083.png "Escenario")

## <a name="enabling-the-application-integration-for-zendesk"></a>Habilitación de la integración de aplicaciones para Zendesk
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Zendesk.

### <a name="to-enable-the-application-integration-for-zendesk-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Zendesk:
1. En el panel de navegación izquierdo del Portal de administración de Azure, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Aplicaciones](./media/active-directory-saas-zendesk-tutorial/IC700994.png "Aplicaciones")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicaciones](./media/active-directory-saas-zendesk-tutorial/IC749321.png "Agregar aplicaciones")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-zendesk-tutorial/IC749322.png "Agregar una aplicación de la galería")

6. En el **cuadro de búsqueda**, escriba **Zendesk**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-zendesk-tutorial/IC773084.png "Galería de aplicaciones")

7. En el panel de resultados, seleccione **Zendesk** y, luego, haga clic en **Completar** para agregar la aplicación.
   
    ![Zendesk](./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Zendesk con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
La configuración del inicio de sesión único para Zendesk requiere que recupere un valor de huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure AD, en la página de integración de aplicaciones de **Zendesk**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/IC773086.png "Inicio de sesión único")

2. En la página **¿Cómo desea que los usuarios inicien sesión en Zendesk?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego , haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configurar inicio de sesión único")

3. En la página **Configurar dirección URL de la aplicación** , realice los pasos siguientes:
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configurar dirección URL de la aplicación")
   
    a. En el cuadro de texto **URL de inicio de sesión de Zendesk**, escriba la dirección URL con el siguiente patrón: `https://<tenant-name>.zendesk.com`.
   
    b. Haga clic en **Siguiente**.

4. En la página **Configurar inicio de sesión único en Zendesk**, haga clic en **Descargar certificado** y, luego, guarde el archivo de certificado en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configurar inicio de sesión único")

5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Zendesk como administrador.

6. Haga clic en **Administrador**.

7. En el panel de navegación izquierdo, haga clic en **Settings** (Configuración) y luego en **Security** (Seguridad).
   
    ![Seguridad](./media/active-directory-saas-zendesk-tutorial/IC773089.png "Seguridad")

8. En la página **Security** (Seguridad), haga clic en la pestaña **Admin & Agents** (Administrador y agentes).

9. Seleccione **Single sign-on (SSO) and SAML** (Inicio de sesión único (SSO) y SAML) y, luego, seleccione **SAML**.

10. En el portal de Azure AD, en la página **Configurar inicio de sesión único en Zendesk**, copie el valor de **Dirección URL de inicio de sesión único de SAML** y péguelo en el cuadro de texto **SAML SSO URL** (Dirección URL de inicio de sesión único de SAML).

11. En el portal de Azure AD, en la página **Configurar inicio de sesión único en Zendesk**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Remote Logout URL** (Dirección URL de cierre de sesión remoto).
    
    ![Inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/IC773090.png "Inicio de sesión único")

12. Copie el valor de **Huella digital** del certificado exportado y péguelo en el cuadro de texto **Certificate Fingerprint** (Huella digital del certificado).
    
    > [!TIP]
    > Para más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI)
    > 
    > 

13. Haga clic en **Guardar**.

14. En el Portal de Azure AD, seleccione la confirmación de la configuración de inicio de sesión único y, luego, haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configurar inicio de sesión único")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para que los usuarios de Azure AD puedan iniciar sesión en **Zendesk**, deben aprovisionarse a **Zendesk**.  
En el caso de **Zendesk**, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-account-to-zendesk-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario a Zendesk, realice los siguientes pasos:
1. Inicie sesión en su inquilino de **Zendesk** .

2. Seleccione la pestaña **Customer List** (Lista de clientes).

3. Seleccione la pestaña **User** (Usuario) y haga clic en **Add** (Agregar).
   
    ![Agregar usuario](./media/active-directory-saas-zendesk-tutorial/IC773632.png "Agregar usuario")
4. Escriba la dirección de correo electrónico de una cuenta de Azure AD existente que quiera aprovisionar y luego haga clic en **Guardar**.
   
    ![Nuevo usuario](./media/active-directory-saas-zendesk-tutorial/IC773633.png "Nuevo usuario")

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Zendesk ofrecida por Zendesk para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-zendesk-perform-the-following-steps"></a>Para asignar usuarios a Zendesk, lleve a cabo los siguientes pasos:
1. En el Portal de Azure AD, cree una cuenta de prueba.

2. En la página de integración de la aplicación **Zendesk**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-zendesk-tutorial/IC773094.png "Asignar usuarios")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-zendesk-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


