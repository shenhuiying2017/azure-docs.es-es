---
title: "Tutorial: Integración de Azure Active Directory con Greenhouse | Microsoft Docs"
description: "Aprenda a usar Greenhouse con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automático, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 78ec1766-4f79-4f16-9a66-d5584c4b6151
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 55a9a01414d930b40f60119318f5c023172141e1


---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Tutorial: integración de Azure Active Directory con Greenhouse
El objetivo de este tutorial es mostrar la integración de Azure y Greenhouse.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción de inicio de sesión único de Greenhouse

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a Greenhouse podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Greenhouse (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Greenhouse
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Scenario")

## <a name="enabling-the-application-integration-for-greenhouse"></a>Habilitación de la integración de aplicaciones para Greenhouse
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Greenhouse.

### <a name="to-enable-the-application-integration-for-greenhouse-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Greenhouse:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **greenhouse**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Application Gallery")
7. En el panel de resultados, seleccione **Greenhouse** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![greenhouse](./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Greenhouse")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en Greenhouse con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Greenhouse**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-greenhouse-tutorial/IC790786.png "Configure single sign-on")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Greenhouse?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-greenhouse-tutorial/IC790787.png "Configure single sign-on")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de inicio de sesión de BlueJeans**, escriba su dirección URL con el siguiente patrón *https://company.greenhouse.io* y, después, haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Configure App URL")
4. En la página **Configurar inicio de sesión único en Greenhouse**, haga clic en **Descargar metadatos** y luego guarde el archivo de metadatos localmente en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-greenhouse-tutorial/IC790789.png "Configure single sign-on")
5. Reenvíe el archivo de metadatos al equipo de soporte técnico de Greenhouse.
   
   > [!NOTE]
   > El equipo de soporte técnico de Greenhouse es el que debe habilitar el inicio de sesión único.
   > 
   > 
6. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-greenhouse-tutorial/IC790790.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Greenhouse, deben aprovisionarse en Greenhouse.  
En el caso de Greenhouse, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice estos pasos:
1. Inicie sesión en el sitio de la compañía de **Greenhouse** como administrador.
2. En el menú en la parte superior, haga clic en **Configurar** y luego haga clic en **Usuarios**.
   
   ![Usuarios](./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Users")
3. Haga clic en **Nuevos usuarios**.
   
   ![Nuevo usuario](./media/active-directory-saas-greenhouse-tutorial/IC790792.png "New User")
4. En la sección **Agregar nuevo usuario** , lleve a cabo estos pasos:
   
   ![Agregar nuevo usuario](./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Add New User")
   
   1. En el cuadro de texto **Especificar correos electrónicos de usuarios** , escriba la dirección de correo electrónico de la cuenta válida de Azure Active Directory que quiera aprovisionar.
   2. Haga clic en **Guardar**.
      
      > [!NOTE]
      > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.
      > 
      > 

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Greenhouse que proporcione Greenhouse para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-greenhouse-perform-the-following-steps"></a>Para asignar usuarios a Greenhouse, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Greenhouse**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Assign users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


