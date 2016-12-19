---
title: "Tutorial: Integración de Azure Active Directory con SmarterU | Microsoft Docs"
description: "Aprenda cómo usar SmarterU con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 95fe3212-d052-4ac8-87eb-ac5305227e85
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0f8658df8871ce3bdca4724448c2232ea326fce6


---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Tutorial: integración de Azure Active Directory con SmarterU
El objetivo de este tutorial es mostrar la integración de Azure y SmarterU.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de SmarterU

Después de completar este tutorial, los usuarios de Azure AD que asignó a SmarterU podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de SmarterU (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para SmarterU
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-smarteru-tutorial/IC777320.png "Scenario")

## <a name="enabling-the-application-integration-for-smarteru"></a>Habilitación de la integración de aplicaciones para SmarterU
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones en SmarterU.

### <a name="to-enable-the-application-integration-for-smarteru-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para SmarterU:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-smarteru-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-smarteru-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-smarteru-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-smarteru-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **SmarterU**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-smarteru-tutorial/IC777321.png "Application fallery")
7. En el panel de resultados, seleccione **SmarterU** y, después, haga clic en **Completar** para agregar la aplicación.
   
   ![SmarterU](./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en SmarterU con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **SmarterU**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-smarteru-tutorial/IC777323.png "Configure Single Sign-On")
2. En la página **¿Cómo desea que los usuarios inicien sesión en SmarterU?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-smarteru-tutorial/IC777324.png "Configure Single Sign-On")
3. En la página **Configurar inicio de sesión único en SmarterU**, para descargar los metadatos, haga clic en **Descargar metadatos** y, después, guarde el archivo de datos de forma local como **c:\\SmarterUMetaData.cer**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-smarteru-tutorial/IC777325.png "Configure Single Sign-On")
4. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de SmarterU como administrador.
5. En la barra de herramientas de la parte superior, haga clic en **Configuración de cuenta**.
   
   ![Configuración de cuenta](./media/active-directory-saas-smarteru-tutorial/IC777326.png "Account Settings")
6. En la página de configuración de la cuenta, realice los siguientes pasos:
   
   ![Autorización externa](./media/active-directory-saas-smarteru-tutorial/IC777327.png "External Authorization")
   
   1. Seleccione **Habilitar autorización externa**.
   2. En la sección **Control de inicio de sesión maestro**, seleccione la pestaña **SmarterU**.
   3. En la sección **Inicio de sesión predeterminado de usuario**, seleccione la pestaña **SmarterU**.
   4. Seleccione **Habilitar Okta**.
   5. Copie el contenido del archivo de metadatos descargado y luego péguelo en el cuadro de texto **Metadatos de Okta** .
   6. Haga clic en **Guardar**.
7. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-smarteru-tutorial/IC777328.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en SmarterU, deben aprovisionarse en SmarterU.  
En el caso de SmarterU, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en su inquilino de **SmarterU** .
2. Vaya a **Usuarios**.
3. En la sección del usuario, lleve a cabo estos pasos:
   
   ![Nuevo usuario](./media/active-directory-saas-smarteru-tutorial/IC777329.png "New User")
   
   1. Haga clic en **+Usuario**.
   2. Escriba los valores de atributos relacionados de la cuenta de usuario de Azure AD en los siguientes cuadros de texto: **Correo electrónico principal**, **Id. de empleado**, **Contraseña**, **Comprobar contraseña**, **Nombre de pila**, **Apellidos**.
   3. Haga clic en **Activo**.
   4. Haga clic en **Save**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de SmarterU ofrecida por SmarterU para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-smarteru-perform-the-following-steps"></a>Para asignar usuarios a SmarterU, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **SmarterU**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-smarteru-tutorial/IC777330.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-smarteru-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


