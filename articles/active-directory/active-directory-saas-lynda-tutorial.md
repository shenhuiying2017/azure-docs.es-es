---
title: "Tutorial: Integración de Azure Active Directory con Lynda.com | Microsoft Docs"
description: "Aprenda a usar Lynda.com con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f6c92789-8b64-4049-bac9-8cb928398433
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 68ac46371849282e4d68b581373b3510a2980744


---
# <a name="tutorial-azure-active-directory-integration-with-lyndacom"></a>Tutorial: Integración de Azure Active Directory con Lynda.com
El objetivo de este tutorial es mostrar la integración de Azure y Lynda.com.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de Lynda.com

Después de completar este tutorial, los usuarios de Azure AD asignados a Lynda.com podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Lynda.com (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Lynda.com
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-lynda-tutorial/IC781046.png "Scenario")

## <a name="enabling-the-application-integration-for-lyndacom"></a>Habilitación de la integración de aplicaciones para Lynda.com
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Lynda.com.

### <a name="to-enable-the-application-integration-for-lyndacom-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Lynda.com:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-lynda-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-lynda-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-lynda-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-lynda-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Lynda.com**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-lynda-tutorial/IC777524.png "Application Gallery")
7. En el panel de resultados, seleccione **Lynda.com** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![Lynda.com](./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Lynda.com con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

> [!IMPORTANT]
> Para poder configurar el inicio de sesión único en el inquilino de Lynda.com, deberá ponerse en contacto primero con el soporte técnico de Lynda.com para habilitar esta característica.
> 
> 

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Lynda.com**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-lynda-tutorial/IC777526.png "Configure single sign-on")
2. En la página **How would you like users to sign on to Lynda.com** (¿Cómo desea que los usuarios inicien sesión en Lynda.com?), seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-lynda-tutorial/IC777527.png "Configure single sign-on")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Lynda.com**, escriba la dirección URL de su inquilino de Lynda.com (por ejemplo, *https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target=https://shib.lynda.com/InCommon*) y luego haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-lynda-tutorial/IC781047.png "Configure app URL")
4. En la página **Configuración de inicio de sesión único en Lynda.com**, para descargar sus metadatos, haga clic en **Descargar metadatos** y luego guarde el archivo localmente en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-lynda-tutorial/IC777529.png "Configure single sign-on")
5. Envíe el archivo de metadatos descargado al equipo de soporte técnico de Lynda.com. El equipo de soporte técnico de Lynda.com realiza la configuración del inicio de sesión único.
6. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-lynda-tutorial/IC777530.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

No hay ningún elemento de acción para que configure el aprovisionamiento de usuarios para Lynda.com.  
Cuando un usuario asignado intenta iniciar sesión en Lynda.com desde el Panel de acceso, Lynda.com comprueba si el usuario existe.  
Si no hay cuentas de usuario disponibles, Lynda.com crea una automáticamente.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Lynda.com ofrecida por Lynda.com para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

### <a name="to-assign-users-to-lyndacom-perform-the-following-steps"></a>Para asignar usuarios a Lynda.com, siga estos pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **Lynda.com**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-lynda-tutorial/IC777531.png "Assign users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-lynda-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


