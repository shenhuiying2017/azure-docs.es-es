---
title: "Tutorial: integración de Azure Active Directory con Qualtrics | Microsoft Docs"
description: "Aprenda cómo usar Qualtrics con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3fb6227aaa50816d49372e9b43fdf5faadf1bded


---
# <a name="tutorial-azure-active-directory-integration-with-qualtrics"></a>Tutorial: Integración de Azure Active Directory con Qualtrics
El objetivo de este tutorial es mostrar la integración de Azure y Qualtrics.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en Qualtrics

Después de completar este tutorial, los usuarios de Azure AD asignados a Qualtrics podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía Qualtrics (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Qualtrics
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-qualtrics-tutorial/IC789542.png "Scenario")

## <a name="enabling-the-application-integration-for-qualtrics"></a>Habilitación de la integración de aplicaciones para Qualtrics
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Qualtrics.

### <a name="to-enable-the-application-integration-for-qualtrics-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Qualtrics:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-qualtrics-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-qualtrics-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-qualtrics-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-qualtrics-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Qualtrics**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-qualtrics-tutorial/IC789543.png "Application Gallery")
7. En el panel de resultados, seleccione **Qualtrics** y haga clic en **Completar** para agregar la aplicación.
   
   ![Qualtrics](./media/active-directory-saas-qualtrics-tutorial/IC789544.png "Qualtrics")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Qualtrics con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Qualtrics**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-qualtrics-tutorial/IC789545.png "Configure Single Sign-On")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Qualtrics?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-qualtrics-tutorial/IC789546.png "Configure Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de Qualtrics**, escriba su dirección URL (por ejemplo, “*https://ssotest2ut1.qualtrics.com*") y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-qualtrics-tutorial/IC789547.png "Configure App URL")
4. En la página **Configuración de inicio de sesión único en Qualtrics**, haga clic en **Descargar metadatos** y guarde el archivo de metadatos en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-qualtrics-tutorial/IC789548.png "Configure Single Sign-On")
5. Envíe el archivo de metadatos al equipo de soporte técnico de Qualtrics.
   
   > [!NOTE]
   > La configuración del inicio de sesión único la debe realizar el equipo de soporte técnico de Qualtrics. Tan pronto como se complete la configuración, recibirá una notificación.
   > 
   > 
6. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-qualtrics-tutorial/IC789549.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

No hay elemento de acción para que configure el aprovisionamiento de usuarios en Qualtrics.  
Cuando un usuario asignado intenta iniciar sesión en Qualtrics desde el panel de acceso, Qualtrics comprueba si el usuario existe.  
Si no hay cuentas de usuario disponibles, Qualtrics crea una automáticamente.

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-qualtrics-perform-the-following-steps"></a>Para asignar usuarios a Qualtrics, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Qualtrics**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-qualtrics-tutorial/IC789550.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-qualtrics-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


