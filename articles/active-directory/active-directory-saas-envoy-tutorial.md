---
title: "Tutorial: Integración de Azure Active Directory con Envoy | Microsoft Docs"
description: "Aprenda a usar Envoy con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 71f7afcc-1033-4098-9b7e-4f9f2b26f734
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4cb3062d8b3ce6640ae07da8887f0e0f14a89d05


---
# <a name="tutorial-azure-active-directory-integration-with-envoy"></a>Tutorial: integración de Azure Active Directory con Envoy
El objetivo de este tutorial es mostrar la integración de Azure y Envoy.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Envoy

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Envoy podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Envoy (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Envoy
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-envoy-tutorial/IC776759.png "Scenario")

## <a name="enabling-the-application-integration-for-envoy"></a>Habilitación de la integración de aplicaciones para Envoy
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Envoy.

### <a name="to-enable-the-application-integration-for-envoy-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Envoy:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-envoy-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-envoy-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-envoy-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Envoy**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-envoy-tutorial/IC776760.png "Application gallery")
7. En el panel de resultados, seleccione **Envoy** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoy")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en Envoy con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
La configuración de un inicio de sesión único para Envoy requiere la recuperación de un valor de huella digital de un certificado.  
Si no está familiarizado con este procedimiento, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Envoy**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Habilitar el inicio de sesión único](./media/active-directory-saas-envoy-tutorial/IC776778.png "Enable single sign-on")
2. En la página **How would you like users to sign on to Envoy** (¿Cómo desea que los usuarios inicien sesión en Envoy?), seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-envoy-tutorial/IC776779.png "Configure single sign-on")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Envoy**, escriba su dirección URL con el siguiente patrón *https://\<nombre del inquilino\>.Envoy.com*" y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-envoy-tutorial/IC776780.png "Configure app URL")
4. En la página **Configurar inicio de sesión único en Envoy**, para descargar el certificado, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado localmente como **c:\\Envoy.cer**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-envoy-tutorial/IC776781.png "Configure single sign-on")
5. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Envoy como administrador.
6. En la barra de herramientas de la parte superior, haga clic en el icono de **Configuración**.
   
   ![Envoy](./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoy")
7. Haga clic en **Compañía**.
   
   ![Compañía](./media/active-directory-saas-envoy-tutorial/IC776783.png "Company")
8. Haga clic en **SAML**.
   
   ![SAML](./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")
9. En la sección de configuración de **SAML Authentication** (Autenticación de SAML), realice los pasos siguientes:
   
   ![SAML Authentication](./media/active-directory-saas-envoy-tutorial/IC776785.png "SAML authentication")
   
   > [!NOTE]
   > La aplicación genera automáticamente el valor del identificador de ubicación de la sede central.
   > 
   > 
   
   1. Copie el valor de **Huella digital** del certificado exportado y luego péguelo en el cuadro de texto **Huella digital**.  
      
      > [!TIP]
      > Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI)
      > 
      > 
   2. En el Portal de Azure clásico, en la página del cuadro de diálogo **Configurar inicio de sesión único en Envoy**, copie el valor de **Dirección URL de SSO de SAML** y péguelo en el cuadro de texto **Identity Provider HTTP SAML URL** (Dirección URL de SAML para HTTP del proveedor de identidades).
   3. Haga clic en **Guardar cambios**.
10. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-envoy-tutorial/IC776786.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

No hay elemento de acción para que configure el aprovisionamiento de usuarios en Envoy.  
Cuando un usuario asignado intenta iniciar sesión en Envoy desde el panel de acceso, Envoy comprueba si el usuario existe.  
Si no hay cuentas de usuario disponibles, Envoy crea una automáticamente.

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-envoy-perform-the-following-steps"></a>Para asignar usuarios a Envoy, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **Envoy**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-envoy-tutorial/IC776787.png "Assign users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-envoy-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


