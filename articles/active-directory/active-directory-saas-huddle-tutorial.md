---
title: "Tutorial: Integración de Azure Active Directory con Huddle | Microsoft Docs"
description: "Aprenda a usar Huddle con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85d7789599cfb39ce0de5d52e0fe057482a49039


---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Tutorial: integración de Azure Active Directory con Huddle
El objetivo de este tutorial es mostrar la integración de Azure y Huddle.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en Huddle

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Huddle podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Huddle (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Huddle
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Configurar inicio de sesión único](./media/active-directory-saas-huddle-tutorial/IC787830.png "Configure Single Sign-On")

## <a name="enabling-the-application-integration-for-huddle"></a>Habilitación de la integración de aplicaciones para Huddle
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Huddle.

### <a name="to-enable-the-application-integration-for-huddle-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Huddle:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-huddle-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-huddle-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-huddle-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Huddle**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-huddle-tutorial/IC787831.png "Application Gallery")
7. En el panel de resultados, seleccione **Huddle** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![Huddle](./media/active-directory-saas-huddle-tutorial/IC787832.png "Huddle")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en Huddle con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Huddle**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-huddle-tutorial/IC787833.png "Configure Single Sign-On")
2. En la página **How would you like users to sign on to Huddle** (¿Cómo desea que los usuarios inicien sesión en Huddle?), seleccione Inicio de sesión único de **Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-huddle-tutorial/IC787834.png "Configure Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Huddle**, escriba la dirección URL de su inquilino de Huddle con el siguiente patrón "*http://company.huddle.com* y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-huddle-tutorial/IC787835.png "Configure App URL")
4. En la página **Configurar inicio de sesión único en Huddle** , siga estos pasos:
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-huddle-tutorial/IC787836.png "Configure Single Sign-On")
   
   1. Haga clic en **Descargar certificado**y luego guarde el archivo de certificado en el equipo.
   2. Copie el valor de **URL del emisor**, el valor de **Dirección URL de SSO de SAML** y el certificado descargado, y luego envíelos al equipo de soporte técnico de Huddle.
   
   > [!NOTE]
   > El inicio de sesión único debe habilitarlo el equipo de soporte técnico de Huddle.
   > Cuando se haya completado la configuración, recibirá una notificación.
   > 
   > 
5. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-huddle-tutorial/IC787837.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Huddle, deben aprovisionarse en Huddle.  
En el caso de Huddle, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en el sitio de compañía de **Huddle** como administrador.
2. Haga clic en **Área de trabajo**.
3. Haga clic en **Contactos\>Invitar a contactos**.
   
   ![Personas](./media/active-directory-saas-huddle-tutorial/IC787838.png "People")
4. En la sección **Crear nueva invitación** , lleve a cabo estos pasos:
   
   ![Nueva invitación](./media/active-directory-saas-huddle-tutorial/IC787839.png "New Invitation")
   
   1. En la lista **Choose a team to invite people to join** (Elegir un equipo al que invitar a unirse a los contactos), seleccione **equipo**.
   2. En el cuadro de texto relacionado, escriba la **Dirección de correo electrónico** de una cuenta de AAD válida que quiera aprovisionar.
   3. Haga clic en **Invitar**.
   
   > [!NOTE]
   > El titular de la cuenta de Azure AD recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.
   > 
   > 

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Huddle que proporcione Huddle para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-huddle-perform-the-following-steps"></a>Para asignar usuarios a Huddle, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **Huddle**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-huddle-tutorial/IC787840.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-huddle-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


