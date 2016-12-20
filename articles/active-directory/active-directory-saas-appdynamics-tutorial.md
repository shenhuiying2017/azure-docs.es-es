---
title: "Tutorial: integración de Azure Active Directory con AppDynamics | Microsoft Docs"
description: "Aprenda a usar AppDynamics con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 13f4e780afc26dc0f1f59408a77f68a7e46308c7


---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Tutorial: Integración de Azure Active Directory con AppDynamics
El objetivo de este tutorial es mostrar la integración de Azure y AppDynamics. En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en AppDynamics

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a AppDynamics podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de AppDynamics (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para AppDynamics
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Scenario")

## <a name="enabling-the-application-integration-for-appdynamics"></a>Habilitación de la integración de aplicaciones para AppDynamics
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para AppDynamics.

### <a name="to-enable-the-application-integration-for-appdynamics-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para AppDynamics:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **AppDynamics**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Application Gallery")
7. En el panel de resultados, seleccione **AppDynamics** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![AppDynamics](./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en AppDynamics con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, se requiere crear un archivo de certificado codificado en base 64.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **AppDynamics**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configure Single SignOn")
2. En la página **¿Cómo desea que los usuarios inicien sesión en AppDynamics?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configure Single SignOn")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de AppDynamics**, escriba la dirección URL que los usuarios usan para iniciar sesión en AppDynamics (*https://companyname.saas.appdynamics.com*) y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configure App URL")
4. En la página **Configuración de inicio de sesión único en AppDynamics**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configure Single SignOn")
5. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de AppDynamics como administrador.
6. En la barra de herramientas de la parte superior, haga clic en **Configuración** y luego en **Administración**.
   
   ![Administración](./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administration")
7. Haga clic en la pestaña **Authentication Provider** (Proveedor de autenticación).
   
   ![Authentication Provider](./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Authentication Provider")
8. En la sección **Proveedor de autenticación** , realice estos pasos:
   
   ![Configuración de SAML](./media/active-directory-saas-appdynamics-tutorial/IC790225.png "SAML Configuration")
   
   1. En **Authentication Provider** (Proveedor de autenticación), seleccione **SAML**.
   2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en AppDynamics**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de inicio de sesión**.
   3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en AppDynamics**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.
   4. Cree un archivo **codificado en base 64** a partir del certificado descargado.  
      
      > [!TIP]
      > Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   5. Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado** .
   6. Haga clic en **Guardar**.
      ![Guardar](./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Save")
9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en AppDynamics, tienen que aprovisionarse en AppDynamics.  
En el caso de AppDynamics, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en su sitio de la compañía de AppDynamics como administrador.
2. Vaya a **Users** (Usuarios) y, a continuación, haga clic en **+** para abrir el cuadro de diálogo **Create User** (Crear usuario).
   
   ![Usuarios](./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Users")
3. En la sección **Crear usuario** , lleve a cabo estos pasos:
   
   ![Crear usuario](./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Create User")
   
   1. Escriba **Nombre de usuario**, **Nombre**, **Correo electrónico**, **Nueva contraseña**, **Repetir nueva contraseña** de una cuenta válida de AAD que desee aprovisionar en los cuadros de texto relacionados.
   2. Haga clic en **Guardar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de AppDynamics suministrada por AppDynamics para aprovisionar cuentas de usuario de Azure AD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-appdynamics-perform-the-following-steps"></a>Para asignar usuarios a AppDynamics, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **AppDynamics**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


