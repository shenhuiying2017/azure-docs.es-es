---
title: "Tutorial: Integración de Azure Active Directory con Cornerstone OnDemand | Microsoft Docs"
description: "Aprenda cómo usar Cornerstone OnDemand con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f57c5fef-49b0-4591-91ef-fc0de6d654ab
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7ab0ffdb9619a488915a7627ac7f50cfaad7019b


---
# <a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Tutorial: Integración de Azure Active Directory con Cornerstone OnDemand
El objetivo de este tutorial es mostrar la integración de Azure y Cornerstone OnDemand.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Cornerstone OnDemand

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Cornerstone OnDemand podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Cornerstone OnDemand (inicio de sesión iniciado por el proveedor del servicio) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Cornerstone OnDemand
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Scenario")

## <a name="enabling-the-application-integration-for-cornerstone-ondemand"></a>Habilitación de la integración de aplicaciones para Cornerstone OnDemand
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para Cornerstone OnDemand.

### <a name="to-enable-the-application-integration-for-cornerstone-ondemand-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Cornerstone OnDemand:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **cornerstone ondemand**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Application Gallery")
7. En el panel de resultados, seleccione **Cornerstone OnDemand** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![cornerstone ondemand](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Cornerstone OnDemand")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Cornerstone OnDemand con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Cornerstone OnDemand**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Habilitar el inicio de sesión único](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Enable Single Sign-On")
2. En la página **How would you like users to sign on ** (¿Cómo desea que los usuarios inicien sesión en Cornerstone OnDemand?), seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego, haga clic en **Siguiente**.
   
   ![Inicio de sesión único de Microsoft Azure AD](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure AD Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Cornerstone OnDemand**, escriba su dirección URL con el siguiente patrón "*http://company.csod.com*" y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configure App URL")
4. En la página **Configurar inicio de sesión único en Cornerstone OnDemand**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configure Single Sign-On")
5. Envíe los elementos siguientes al equipo de soporte técnico de Cornerstone OnDemand:
   
   1. El certificado descargado
   2. El valor **Dirección URL de inicio de sesión remoto** .
   3. El valor **Dirección URL de cierre de sesión remoto** .
   
   > [!NOTE]
   > El inicio de sesión único debe configurarlo el equipo de soporte técnico Cornerstone OnDemand.
   > Cuando se haya completado la configuración, recibirá una notificación del equipo de soporte técnico.
   > 
   > 
6. Seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Cornerstone OnDemand, deben aprovisionarse a Cornerstone OnDemand.  
En el caso de Cornerstone OnDemand, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Envíe la información (p. ej.: nombre, correo electrónico) sobre el usuario de Azure AD que desee aprovisionar al equipo de soporte técnico de Cornerstone OnDemand.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Cornerstone OnDemand ofrecida por Cornerstone OnDemand para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-cornerstone-ondemand-perform-the-following-steps"></a>Para asignar usuarios a Cornerstone OnDemand, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **Cornerstone OnDemand**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Assign users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Asignar usuarios](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Assign Users")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


