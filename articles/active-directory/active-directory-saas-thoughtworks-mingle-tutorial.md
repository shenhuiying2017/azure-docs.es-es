---
title: "Tutorial: integración de Azure Active Directory con Thoughtworks Mingle | Microsoft Docs"
description: "Aprenda cómo usar Thoughtworks Mingle con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6318f2fad781118b0f2c364d1cbc2b686f46a95e


---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Tutorial: Integración de Azure Active Directory con Thoughtworks Mingle
El objetivo de este tutorial es mostrar la integración de Azure y Thoughtworks Mingle.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Thoughtworks Mingle

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Thoughtworks Mingle
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Scenario")

## <a name="enabling-the-application-integration-for-thoughtworks-mingle"></a>Habilitación de la integración de aplicaciones para Thoughtworks Mingle
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Thoughtworks Mingle.

### <a name="to-enable-the-application-integration-for-thoughtworks-mingle-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Thoughtworks Mingle:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **thoughtworks mingle**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Application Gallery")
7. En el panel de resultados, seleccione **Thoughtworks Mingle** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![Thoughtworks Mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Thoughtworks Mingle")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Thoughtworks Mingle con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
Como parte de este procedimiento, es necesario cargar un certificado en Thoughtworks Mingle.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **Thoughtworks Mingle**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "Configure single sign-on")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Thoughtworks Mingle?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "Configure single sign-on")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inquilino de Thoughtworks Mingle**, escriba su dirección URL con el siguiente patrón "*http://company.mingle.thoughtworks.com*" y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Configure App URL")
4. En la página **Configuración de inicio de sesión único en Thoughtworks Mingle** , haga clic en Descargar metadatos y luego guarde el archivo de metadatos en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "Configure single sign-on")
5. Inicie sesión en su sitio de compañía de **Thoughtworks Mingle** como administrador.
6. Haga clic en la pestaña **Admin** (Administrador) y, luego, en **SSO Config** (Configuración de SSO).
   
   ![Configuración de SSO](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "SSO Config")
7. En la sección **Configuración de SSO** , lleve a cabo estos pasos:
   
   ![Configuración de SSO](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "SSO Config")
   
   1. Para cargar el archivo de metadatos, haga clic en **Elegir archivo**.
   2. Haga clic en **Guardar cambios**.
8. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para que los usuarios de AAD puedan inician sesión, deben aprovisionarse para la aplicación Thoughtworks Mingle con sus nombres de usuario de Azure Active Directory.  
En el caso de Thoughtworks Mingle, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en su sitio de compañía de Thoughtworks Mingle como administrador.
2. Haga clic en **Perfil**.
   
   ![Su primer proyecto](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "Your First Project")
3. Haga clic en la pestaña **Admin** (Administrador) y, luego, en **Users** (Usuarios).
   
   ![Usuarios](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Users")
4. Haga clic en **Nuevo usuario**.
   
   ![Nuevo usuario](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "New User")
5. En la página del cuadro de diálogo **Nuevo usuario** , realice los pasos siguientes:
   
   ![Nuevo usuario](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "New User")
   
   1. En los cuadros de texto **Sign-in name** (Nombre de inicio de sesión), **Display name** (Nombre para mostrar), **Choose password** (Elegir contraseña) y **Confirm password** (Confirmar contraseña), escriba la información pertinente de una cuenta de AAD válida que desee aprovisionar.
   2. En **User type** (Tipo de usuario), seleccione **Full user** (Usuario completo).
   3. Haga clic en **Crear este perfil**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Thoughtworks Mingle ofrecida por Thoughtworks Mingle para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-thoughtworks-mingle-perform-the-following-steps"></a>Para asignar usuarios a Thoughtworks Mingle, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **Thoughtworks Mingle**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


