---
title: "Tutorial: Integración de Azure Active Directory con FM:Systems | Microsoft Docs"
description: "Aprenda a usar FM: Systems con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f78c58c5-6e98-458b-8991-78624a245665
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/23/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 789ec6f0a230f49ecd935bc85ea4ad984e936da9
ms.openlocfilehash: bd2bfa1696215ded3da40d349111efa13ddbed37


---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>Tutorial: Integración de Azure Active Directory con FM:Systems
El objetivo de este tutorial es mostrar la integración de Azure y FM:Systems.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en FM:Systems

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a FM:Systems podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de FM:Systems (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para FM:Systems
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Scenario")

## <a name="enabling-the-application-integration-for-fmsystems"></a>Habilitación de la integración de aplicaciones para FM:Systems
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para FM:Systems.

### <a name="to-enable-the-application-integration-for-fmsystems-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para FM:Systems:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications](./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Applications")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicación](./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Add application")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Add an application from gallerry")

6. En el **cuadro de búsqueda**, escriba **FM:Systems**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Application Gallery")

7. En el panel de resultados, seleccione **FM:Systems** y, a continuación, haga clic en **Completar** para agregar la aplicación.
   
    ![FM:Systems](./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM:Systems")
   
## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en FM:Systems con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **FM:Systems**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Configure Single Sign-On")

2. En la página **¿Cómo desea que los usuarios inicien sesión en FM:Systems?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Configure Single Sign-On")

3. En la página **Configurar dirección URL de la aplicación** , realice los pasos siguientes:
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Configure App URL")
   
    a. En el cuadro de texto **Dirección URL de inicio de sesión de FM: Systems** , escriba su **dirección URL de respuesta** de FM: Systems (p. ej.: *https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*).  
      
    > [!NOTE]
    > Este valor se lo puede proporcionar el equipo de soporte técnico de FM:Systems.
    > 
    > 
   
    b. Haga clic en **Siguiente**

4. En la página **Configurar inicio de sesión único en FM:Systems**, para descargar los metadatos, haga clic en **Descargar metadatos** y luego guarde los metadatos en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Configure Single Sign-On")

5. Envíe el archivo de metadatos descargado al equipo de soporte técnico de FM: Systems.
   
    > [!NOTE]
    > El equipo de soporte técnico de FM: Systems es el que tiene que realizar la configuración real de SSO.
    > Cuando SSO se haya habilitado en su suscripción recibirá una notificación.
    > 
    > 
6. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Configure Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en FM:Systems, deben aprovisionarse en FM:Systems.  
En el caso de FM:Systems, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. En una ventana del explorador web, inicie sesión en el sitio de la compañía de FM:Systems como administrador.

2. Vaya a **System Administration \> Manage Security \> Users \> User list** (Administración del sistema > Administrar seguridad > Usuarios > Lista de usuarios).
   
    ![Administración del sistema](./media/active-directory-saas-fm-systems-tutorial/IC795905.png "System Administration")

3. Haga clic en **Crear nuevo usuario**.
   
    ![Crear nuevo usuario](./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Create New User")

4. En la sección **Crear usuario** , lleve a cabo estos pasos:
   
    ![Crear usuario](./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Create User")
   
    a. Escriba el nombre de usuario, la contraseña y su confirmación, la dirección de correo electrónico y el Id. de empleado de la cuenta de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.
   
    b. Haga clic en **Siguiente**.
 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-fmsystems-perform-the-following-steps"></a>Para asignar usuarios a FM:Systems, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **FM:Systems**, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Assign Users")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO4-->


