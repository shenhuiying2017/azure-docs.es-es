---
title: "Tutorial: integración de Azure Active Directory con FreshService | Microsoft Docs"
description: "Aprenda a usar FreshService con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e6fa6a1817bc01e6d9f6b10f7e6479cdc4f60ca2


---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Tutorial: integración de Azure Active Directory con FreshService
El objetivo de este tutorial es mostrar la integración de Azure y FreshService.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en FreshService

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a FreshService podrán realizar un inicio de sesión único en la aplicación desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para FreshService
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-freshservice-tutorial/IC790807.png "Scenario")

## <a name="enabling-the-application-integration-for-freshservice"></a>Habilitación de la integración de aplicaciones para FreshService
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para FreshService.

### <a name="to-enable-the-application-integration-for-freshservice-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para FreshService:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-freshservice-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-freshservice-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-freshservice-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **FreshService**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-freshservice-tutorial/IC790808.png "Application Gallery")
7. En el panel de resultados, seleccione **FreshService** y, después, haga clic en **Completar** para agregar la aplicación.
   
   ![FreshService](./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en FreshService con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
La configuración de un inicio de sesión único para FreshService requiere la recuperación de un valor de huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **FreshService**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configure Single Sign-On")
2. En la página **¿Cómo desea que los usuarios inicien sesión en FreshService?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configure Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de FreshService**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación Freshdesk (por ejemplo: "*http://democompany.freshservice.com/*") y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-freshservice-tutorial/IC790812.png "Configure App URL")
4. En la página **Configurar inicio de sesión único en FreshService**, para descargar el certificado, haga clic en **Descargar certificado** y guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configure Single Sign-On")
5. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de FreshService como administrador.
6. En el menú de la parte superior, haga clic en **Administrador**.
   
   ![Administrador](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")
7. En el **Portal del cliente**, haga clic en **Seguridad**.
   
   ![Seguridad](./media/active-directory-saas-freshservice-tutorial/IC790815.png "Security")
8. En la sección **Seguridad** , realice estos pasos:
   
   ![Inicio de sesión único](./media/active-directory-saas-freshservice-tutorial/IC790816.png "Single Sign On")
   
   1. Conmute **Inicio de sesión único**a Activado.
   2. Seleccione **Inicio de sesión único de SAML**.
   3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en FreshService**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **SAML Login URL** (Dirección URL de inicio de sesión de SAML).
   4. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en FreshService**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Logout URL** (Dirección URL de cierre de sesión).
   5. Copie el valor de **Huella digital** del certificado exportado y luego péguelo en el cuadro de texto **Security Certificate Fingerprint** (Huella digital de certificado de seguridad).
      
      > [!TIP]
      > Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI)
      > 
      > 
9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en FreshService, deben aprovisionarse en FreshService.  
En el caso de FreshService, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en el sitio de la compañía de **FreshService** como administrador.
2. En el menú de la parte superior, haga clic en **Administrador**.
   
   ![Administrador](./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")
3. En la sección **User Management** (Administración de usuarios), haga clic en **Requesters** (Solicitantes).
   
   ![Solicitantes](./media/active-directory-saas-freshservice-tutorial/IC790818.png "Requesters")
4. Haga clic en **Nuevo solicitante**.
   
   ![Nuevos solicitantes](./media/active-directory-saas-freshservice-tutorial/IC790819.png "New Requesters")
5. En la sección **New Requester** (Nuevo solicitante), lleve a cabo estos pasos:
   
   ![Nuevo solicitante](./media/active-directory-saas-freshservice-tutorial/IC790820.png "New Requester")
   
   1. Escriba los atributos **First Name** (Nombre) y **Email** (Correo electrónico) de una cuenta de Azure Active Directory válida que desee aprovisionar en los cuadros de texto relacionados.
   2. Haga clic en **Save**.
   
   > [!NOTE]
   > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.
   > 
   > 

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de FreshService que proporcione FreshService para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-freshservice-perform-the-following-steps"></a>Para asignar usuarios a FreshService, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **FreshService**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-freshservice-tutorial/IC790821.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-freshservice-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


