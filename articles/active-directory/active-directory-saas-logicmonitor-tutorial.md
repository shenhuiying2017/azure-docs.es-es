---
title: "Tutorial: integración de Azure Active Directory con LogicMonitor | Microsoft Docs"
description: "Aprenda a usar LogicMonitor con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6dbb19605f92ff0c0d58478a56e564bab71123e4


---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Tutorial: Integración de Azure Active Directory con LogicMonitor
El objetivo de este tutorial es mostrar la integración de Azure y LogicMonitor.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de LogicMonitor

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para LogicMonitor
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Scenario")

## <a name="enabling-the-application-integration-for-logicmonitor"></a>Habilitación de la integración de aplicaciones para LogicMonitor
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para LogicMonitor.

### <a name="to-enable-the-application-integration-for-logicmonitor-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para LogicMonitor:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **logicmonitor**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Application Gallery")
7. En el panel de resultados, seleccione **LogicMonitor** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![logicmonitor](./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en LogicMonitor con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **LogicMonitor**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Configure Single Sign-On")
2. En la página **¿Cómo desea que los usuarios inicien sesión en LogicMonitor?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Configure Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL que los usuarios utilizan para iniciar sesión en LogicMonitor \(por ejemplo: "*http://company.logicmonitor.com*"\) y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Configure App URL")
4. En la página **Configuración de inicio de sesión único en LogicMonitor**, haga clic en **Descargar metadatos** y guárdelos en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Configure Single Sign-On")
5. Inicie sesión como administrador en el sitio de la compañía de **LogicMonitor** .
6. En el menú de la parte superior, haga clic en **Configuración**.
   
   ![Configuración](./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Settings")
7. En la barra de navegación del lado izquierdo, haga clic en **Inicio de sesión único**
   
   ![Inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Single Sign-On")
8. En la sección **Configuración del inicio de sesión único** , siga estos pasos:
   
   ![Configuración de inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Single Sign-On Settings")
   
   1. Seleccione **Habilitar inicio de sesión único**.
   2. En **Default Role Assignment** (Asignación de rol predeterminado), seleccione **readonly**.
   3. Abra el archivo de metadatos descargado en el Bloc de notas y luego pegue el contenido del archivo en el cuadro de texto **Metadatos del proveedor de identidades** .
   4. Haga clic en **Guardar cambios**.
9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para que los usuarios de AAD puedan inician sesión, deben aprovisionarse para la aplicación LogicMonitor con sus nombres de usuario de Azure Active Directory.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión como administrador en el sitio de la compañía de LogicMonitor.
2. En el menú de la parte superior, haga clic en **Settings** (Configuración) y, luego, en **Roles and Users** (Roles y usuarios).
   
   ![Roles y usuarios](./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Roles and Users")
3. Haga clic en **Agregar**.
4. En la sección **Agregar una cuenta** , realice estos pasos:
   
   ![Agregar una cuenta](./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Add an account")
   
   1. En los cuadros de texto correspondientes, escriba los valores de **nombre de usuario**, **correo electrónico**, **contraseña** y **volver a escribir contraseña** del usuario de Azure Active Directory que desee aprovisionar.
   2. Seleccione **Roles**, **View Permissions** (Ver permisos) y **Status** (Estado).
   3. Haga clic en **Submit**(Enviar).

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de LogicMonitor que proporcione LogicMonitor para aprovisionar cuentas de usuario de Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

### <a name="to-assign-users-to-logicmonitor-perform-the-following-steps"></a>Para asignar usuarios a LogicMonitor, siga estos pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **LogicMonitor**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


