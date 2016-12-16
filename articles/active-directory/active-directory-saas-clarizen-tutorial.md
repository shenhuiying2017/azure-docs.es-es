---
title: "Tutorial: integración de Azure Active Directory con Clarizen | Microsoft Docs"
description: "Aprenda a usar Clarizen con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9f8d2418fe86163a4022960f87803c82487c86fd


---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Tutorial: integración de Azure Active Directory con Clarizen
El objetivo de este tutorial es mostrar la integración de Azure y Clarizen.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en Clarizen

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Clarizen podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Clarizen (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Clarizen
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-clarizen-tutorial/IC784679.png "Scenario")

## <a name="enabling-the-application-integration-for-clarizen"></a>Habilitación de la integración de aplicaciones para Clarizen
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Clarizen.

### <a name="to-enable-the-application-integration-for-clarizen-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Clarizen:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-clarizen-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-clarizen-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-clarizen-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Clarizen**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-clarizen-tutorial/IC784680.png "Application Gallery")
7. En el panel de resultados, seleccione **Clarizen** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![Clarizen](./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en Clarizen con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de la aplicación **Clarizen**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configure Single Sign-On")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Clarizen?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configure Single Sign-On")
3. En la página **Configurar inicio de sesión único en Clarizen**, para descargar el certificado, haga clic en **Descargar certificado** y guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configure Single Sign-On")
4. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de **Clarizen** como administrador (p.ej.: *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).
5. Haga clic en el nombre de usuario y luego haga clic en **Settings** (Configuración).
   
   ![Settings (Configuración)](./media/active-directory-saas-clarizen-tutorial/IC784685.png "Settings")
6. Haga clic en la pestaña **Global Settings** (Configuración global) y, luego, al lado de **Federated Authentication** (Autenticación federada), haga clic en **edit** (editar).
   
   ![Global Settings (Configuración global)](./media/active-directory-saas-clarizen-tutorial/IC786906.png "Global Settings")
7. En el cuadro de diálogo **Federated Authentication** (Autenticación federada), realice los pasos siguientes:
   
   ![Federated Authentication](./media/active-directory-saas-clarizen-tutorial/IC785892.png "Federated Authentication")
   
   1. Para cargar el certificado descargado, haga clic en **Cargar** .
   2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Clarizen**, copie el valor de **Dirección URL del servicio de inicio de sesión único** y péguelo en el cuadro de texto **Sign-in URL** (Dirección URL de inicio de sesión).
   3. En el Portal de Azure clásico, en la página de diálogo **Configurar cierre de sesión único en Clarizen**, copie el valor de **Dirección URL del servicio de cierre de sesión único** y péguelo en el cuadro de texto **Sign-out URL** (Dirección URL de cierre de sesión).
   4. Seleccione **Usar POST**.
   5. Haga clic en **Guardar**.
8. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Clarizen, deben aprovisionarse en Clarizen.  
En el caso de Clarizen, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en el sitio de la compañía de **Clarizen** como administrador.
2. Haga clic en **Contactos**.
   
   ![Contactos](./media/active-directory-saas-clarizen-tutorial/IC784689.png "People")
3. Haga clic en **Invitar a usuario**.
   
   ![Invitar a usuarios](./media/active-directory-saas-clarizen-tutorial/IC784690.png "Invite Users")
4. En la página de diálogo Invite People (Invitar a contactos), realice los siguientes pasos:
   
   ![Invitar a contactos](./media/active-directory-saas-clarizen-tutorial/IC784691.png "Invite People")
   
   1. En el cuadro de texto **Correo electrónico** , escriba la dirección de correo electrónico de la cuenta válida de Azure Active Directory que quiera aprovisionar.
   2. Haga clic en **Invite**(Invitar).
   
   > [!NOTE]
   > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta antes de que se active.
   > 
   > 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-clarizen-perform-the-following-steps"></a>Para asignar usuarios a Clarizen, siga estos pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **Clarizen**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-clarizen-tutorial/IC784692.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-clarizen-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


