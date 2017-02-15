---
title: "Tutorial: Integración de Azure Active Directory con Chromeriver | Microsoft Docs"
description: "Aprenda cómo usar Chromeriver con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 445c5600-e340-4724-a9cb-3cfaf5770b70
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 88b450c61d382c3c3509f85a8015edc58e8d5139


---
# <a name="tutorial-azure-active-directory-integration-with-chromeriver"></a>Tutorial: integración de Azure Active Directory con Chromeriver
El objetivo de este tutorial es mostrar la integración de Azure y Chromeriver.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para inicio de sesión único en Chromeriver

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Chromeriver podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Chromeriver (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Chromeriver
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-chromeriver-tutorial/IC802755.png "Scenario")

## <a name="enabling-the-application-integration-for-chromeriver"></a>Habilitación de la integración de aplicaciones para Chromeriver
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Chromeriver.

### <a name="to-enable-the-application-integration-for-chromeriver-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Chromeriver:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-chromeriver-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-chromeriver-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-chromeriver-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-chromeriver-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Chromeriver**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-chromeriver-tutorial/IC802756.png "Application Gallery")
7. En el panel de resultados, seleccione **Chromeriver** y luego haga clic en **Completar** para agregar la aplicación.
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Chromeriver con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Chromeriver**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-chromeriver-tutorial/IC802757.png "Configure Single Sign-On")
2. En la página **How would you like users to sign on to Chromeriver** (¿Cómo desea que los usuarios inicien sesión en Chromeriver?), seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-chromeriver-tutorial/IC802758.png "Configure Single Sign-On")
3. En la página **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
   ![Configurar las opciones de la aplicación](./media/active-directory-saas-chromeriver-tutorial/IC802759.png "Configure App Settings")
   
   1. En el cuadro de texto **URL de repuesta**, escriba su **URL de AssertionConsumerService URL** de Chromeriver (por ejemplo: *https://qa-app.chromeriver.com/login/sso/saml/consume?customerId=911*).  
      
      > [!NOTE]
      > Este valor lo puede proporcionar el equipo de soporte técnico de Chromeriver.
      > 
      > 
   2. Haga clic en **Siguiente**
4. En la página **Configurar inicio de sesión único en Chromeriver**, para descargar los metadatos, haga clic en **Descargar metadatos** y luego guarde el archivo de metadatos en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-chromeriver-tutorial/IC802760.png "Configure Single Sign-On")
5. Envíe el archivo de metadatos descargado al equipo de soporte técnico de Chromeriver.
   
   > [!NOTE]
   > El equipo de soporte técnico de Chromeriver es el que tiene que realizar la configuración real de SSO.  
   > Cuando SSO se haya habilitado en su suscripción recibirá una notificación.
   > 
   > 
6. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-chromeriver-tutorial/IC802761.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Chromeriver, deben aprovisionarse en Chromeriver.  
En el caso de Chromeriver, las cuentas de usuario debe crearlas el equipo de soporte técnico de Chromeriver.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Chromeriver que proporcione Chromeriver para aprovisionar cuentas de usuario de Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-chromeriver-perform-the-following-steps"></a>Para asignar usuarios a Chromeriver, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Chromeriver**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-chromeriver-tutorial/IC802762.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-chromeriver-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


