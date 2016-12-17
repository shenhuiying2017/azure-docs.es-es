---
title: "Tutorial: Integración de Azure Active Directory con Panorama9 | Microsoft Docs"
description: "Aprenda cómo usar Panorama9 con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: bf29d754d365320c35cdd44e2116a8b5beb3a27e


---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Tutorial: Integración de Azure Active Directory con Panorama9
El objetivo de este tutorial es mostrar la integración de Azure y Panorama9.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en Panorama9

Después de completar este tutorial, los usuarios de Azure AD asignados a Panorama9 podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía Panorama9 (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones en Panorama9
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-panorama9-tutorial/IC790016.png "Scenario")

## <a name="enabling-the-application-integration-for-panorama9"></a>Habilitación de la integración de aplicaciones en Panorama9
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Panorama9.

### <a name="to-enable-the-application-integration-for-panorama9-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Panorama9:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-panorama9-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-panorama9-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-panorama9-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-panorama9-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Panorama9**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-panorama9-tutorial/IC790017.png "Application Gallery")
7. En el panel de resultados, seleccione **Panorama9** y, después, haga clic en **Completar** para agregar la aplicación.
   
   ![Panorama9](./media/active-directory-saas-panorama9-tutorial/IC790018.png "Panorama9")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Panorama9 con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
La configuración de un inicio de sesión único para Panorama9 requiere la recuperación de un valor de huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Panorama9**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-panorama9-tutorial/IC790019.png "Configure Single Sign-On")
2. En la página **¿Cómo quiere que los usuarios inicien sesión en Panorama9?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-panorama9-tutorial/IC790020.png "Configure Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Panorama9**, escriba la dirección URL que usan los usuarios para iniciar sesión en Panorama9 (por ejemplo: “*https://dashboard.panorama9.com/saml/access/3262*”) y, luego, haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-panorama9-tutorial/IC790021.png "Configure App URL")
4. En la página **Configurar inicio de sesión único en Panorama9**, para descargar el certificado, haga clic en **Descargar certificado** y, después, guárdelo en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-panorama9-tutorial/IC790022.png "Configure Single Sign-On")
5. En otra ventana del explorador web, inicie sesión en el sitio de la compañía Panorama9 como administrador.
6. En la barra de herramientas de la parte superior, haga clic en **Administrar** y luego en **Extensiones**.
   
   ![Extensiones](./media/active-directory-saas-panorama9-tutorial/IC790023.png "Extensions")
7. En el diálogo **Extensiones**, haga clic en **Inicio de sesión único**.
   
   ![Inicio de sesión único](./media/active-directory-saas-panorama9-tutorial/IC790024.png "Single Sign-On")
8. En la sección **Configuración** , lleve a cabo estos pasos:
   
   ![Configuración](./media/active-directory-saas-panorama9-tutorial/IC790025.png "Settings")
   
   1. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Panorama9**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Dirección URL del proveedor de identidades**.
   2. Copie el valor de **Huella digital** del certificado exportado y péguelo en el cuadro de texto **Certificate Fingerprint (Huella digital del certificado)**.  
      
      > [!TIP]
      > Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI)
      > 
      > 
   3. Haga clic en **Guardar**.
9. En el Portal de Azure AD clásico, seleccione la confirmación de configuración de inicio de sesión único y, luego, haga clic en **Completar** para cerrar el diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-panorama9-tutorial/IC790026.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Panorama9, deben aprovisionarse en Panorama9.  
En el caso de Panorama9, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en el sitio de la compañía **Panorama9** como administrador.
2. En el menú en la parte superior, haga clic en **Administrar** y luego haga clic en **Usuarios**.
   
   ![Usuarios](./media/active-directory-saas-panorama9-tutorial/IC790027.png "Users")
3. Haga clic en **+**.
4. En la sección de datos del usuario, lleve a cabo estos pasos:
   
   ![Usuarios](./media/active-directory-saas-panorama9-tutorial/IC790028.png "Users")
   
   1. En el cuadro de texto **Correo electrónico** , escriba la dirección de correo electrónico de un usuario válido de Azure Active Directory que quiera aprovisionar.
   2. Haga clic en **Guardar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Panorama9 ofrecida por Panorama9 para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-panorama9-perform-the-following-steps"></a>Para asignar usuarios a Panorama9, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Panorama9**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-panorama9-tutorial/IC790029.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-panorama9-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


