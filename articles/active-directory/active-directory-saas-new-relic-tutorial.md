---
title: "Tutorial: Integración de Azure Active Directory con New Relic | Microsoft Docs"
description: "Aprenda cómo usar New Relic con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c7c7542454bab5e5c2fae88aeefa1a981ee165ff


---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Tutorial: Integración de Azure Active Directory con New Relic
El objetivo de este tutorial es mostrar cómo configurar el inicio de sesión único entre Azure Active Directory y New Relic.

En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en New Relic

Después de completar este tutorial, los usuarios de Active Directory de Azure que ha asignado a New Relic podrán realizar el inicio de sesión único mediante el Panel de acceso de AAD.

1. Habilitación de la integración de aplicaciones para New Relic
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-new-relic-tutorial/IC797030.png "Scenario")

## <a name="enabling-the-application-integration-for-new-relic"></a>Habilitación de la integración de aplicaciones para New Relic
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para New Relic.

### <a name="to-enable-the-application-integration-for-new-relic-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones en New Relic:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-new-relic-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-new-relic-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-new-relic-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-new-relic-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **New Relic**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-new-relic-tutorial/IC797031.png "Application Gallery")
7. En el panel de resultados, seleccione **New Relic** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![New Relic](./media/active-directory-saas-new-relic-tutorial/IC797032.png "New Relic")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en New Relic con su cuenta de Azure AD a través de la federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **New Relic**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-new-relic-tutorial/IC769534.png "Configure single sign-on")
2. En la página **¿Cómo desea que los usuarios inicien sesión en New Relic?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-new-relic-tutorial/IC797033.png "Configure Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de New Relic**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación New Relic y, luego, haga clic en **Siguiente**. 
   
   La dirección URL de la aplicación es la dirección URL de inquilino de New Relic (por ejemplo, *https://rpm.newrelic.com*):
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-new-relic-tutorial/IC797034.png "Configure App URL")
4. En la página **Configurar inicio de sesión único en New Relic**, para descargar el certificado, haga clic en **Descargar certificado** y, luego, guarde el archivo de certificado localmente en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-new-relic-tutorial/IC797035.png "Configure Single Sign-On")
5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de **New Relic** como administrador.
6. En el menú de la parte superior, haga clic en **Configuración de cuenta**.
   
   ![Configuración de cuenta](./media/active-directory-saas-new-relic-tutorial/IC797036.png "Account Settings")
7. Haga clic en la pestaña **Seguridad y autenticación** y, luego, haga clic en la pestaña **Inicio de sesión único**.
   
   ![Inicio de sesión único](./media/active-directory-saas-new-relic-tutorial/IC797037.png "Single Sign-On")
8. En la página de diálogo SAML, realice los pasos siguientes:
   
   ![SAML](./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")
   
   1. Haga clic en **Elegir archivo** para cargar el certificado de Azure Active Directory descargado.
   2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en New Relic**, copie el valor de **URL del inicio de sesión remoto** y péguelo en el cuadro de texto **URL de inicio de sesión remoto**.
   3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en New Relic**, copie el valor de **URL del inicio de sesión remoto** y péguelo en el cuadro de texto **Logout landing URL** (Dirección URL de aterrizaje de cierre de sesión).
   4. Haga clic en **Guardar los cambios**.
9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-new-relic-tutorial/IC797039.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure Active Directory inicien sesión en New Relic, deben aprovisionarse en New Relic.  
En el caso de New Relic, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-account-to-new-relic-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario a New Relic, realice los siguientes pasos:
1. Inicie sesión como administrador en el sitio de la compañía **New Relic** .
2. En el menú de la parte superior, haga clic en **Configuración de cuenta**.
   
   ![Configuración de cuenta](./media/active-directory-saas-new-relic-tutorial/IC797040.png "Account Settings")
3. En el panel **Cuenta** que se encuentra en el lado izquierdo, haga clic en **Resumen** y, luego, haga clic en **Agregar usuario**.
   
   ![Configuración de cuenta](./media/active-directory-saas-new-relic-tutorial/IC797041.png "Account Settings")
4. En el cuadro de diálogo **Usuarios activos** , realice los pasos siguientes:
   
   ![Usuarios activos](./media/active-directory-saas-new-relic-tutorial/IC797042.png "Active Users")
   
   1. En el cuadro de texto **Correo electrónico** , escriba la dirección de correo electrónico de un usuario válido de Azure Active Directory que quiera aprovisionar.
   2. Como **Rol**, seleccione **Usuario**.
   3. Haga clic en **Agregar este usuario**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de New Relic ofrecida por New Relic para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-new-relic-perform-the-following-steps"></a>Para asignar usuarios a New Relic, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **New Relic**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-new-relic-tutorial/IC797043.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-new-relic-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


