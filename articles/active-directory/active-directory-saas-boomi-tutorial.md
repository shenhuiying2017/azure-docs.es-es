---
title: "Tutorial: Integración de Azure Active Directory con Boomi | Microsoft Docs"
description: "Aprenda a usar Boomi con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: a315916e-8bfd-4390-bad2-ec9029314ab6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 51ea1ededc7b850e3dff24c9f735789d34391434


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Tutorial: Integración de Azure Active Directory con Boomi
El objetivo de este tutorial es mostrar la integración de Azure y Boomi.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para inicio de sesión único en Boomi

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a Boomi podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Boomi (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Boomi
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-boomi-tutorial/IC791134.png "Scenario")

## <a name="enabling-the-application-integration-for-boomi"></a>Habilitación de la integración de aplicaciones para Boomi
El objetivo de esta sección es describir cómo se habilita la integración de las aplicaciones para Boomi.

### <a name="to-enable-the-application-integration-for-boomi-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Boomi:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-boomi-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-boomi-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-boomi-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Boomi**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-boomi-tutorial/IC790822.png "Application Gallery")
7. En el panel de resultados, seleccione **Boomi** y, después, haga clic en **Completar** para agregar la aplicación.
   
   ![Boomi](./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo se habilita la autenticación de usuarios en Boomi con su cuenta de Azure AD mediante la federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Boomi**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/IC790824.png "Configure Single Sign-On")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Boomi?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/IC790825.png "Configure Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de respuesta de Boomi**, escriba la **Dirección URL de inicio de sesión de Boomi AtomSphere** (por ejemplo, “*https://platform.boomi.com/sso/AccountName/saml*”) y, después, haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-boomi-tutorial/IC790826.png "Configure App URL")
4. En la página **Configurar inicio de sesión único en Boomi**, para descargar el certificado, haga clic en **Descargar certificado** y, después, guarde el archivo de certificado de forma local en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/IC790827.png "Configure Single Sign-On")
5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Boomi.
6. En la barra de herramientas de la parte superior, haga clic en el nombre de su empresa y en **Configuración**.
   
   ![Configuración](./media/active-directory-saas-boomi-tutorial/IC790828.png "Setup")
7. Haga clic en **Opciones de SSO**.
   
   ![Opciones de SSO](./media/active-directory-saas-boomi-tutorial/IC790829.png "SSO Options")
8. En la sección **Opciones de inicio de sesión único** , siga estos pasos:
   
   ![Opciones de inicio de sesión único](./media/active-directory-saas-boomi-tutorial/IC790830.png "Single Sign-On Options")
   
   1. Seleccione **Habilitar inicio de sesión único de SAML**.
   2. Haga clic en **Importar**para cargar el certificado descargado.
   3. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en Boomi**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **URL de inicio de sesión del proveedor de identidades**.
   4. En **Ubicación del id. de federación**, seleccione **El id. de federación está en el elemento NameID de Subject**.
   5. Haga clic en **Guardar**.
9. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-boomi-tutorial/IC775560.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en Boomi, tienen que aprovisionarse en Boomi.  
En el caso de Boomi, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión como administrador en el sitio de la compañía de **Boomi** .
2. Vaya a **Administración de usuarios \> Usuarios**.
   
   ![Usuarios](./media/active-directory-saas-boomi-tutorial/IC790831.png "Users")
3. Haga clic en **Agregar usuario**.
   
   ![Agregar usuario](./media/active-directory-saas-boomi-tutorial/IC790832.png "Add User")
4. En la página del cuadro de diálogo **Agregar roles de usuario** , realice los siguientes pasos:
   
   ![Agregar usuario](./media/active-directory-saas-boomi-tutorial/IC790833.png "Add User")
   
   1. Escriba los datos de una cuenta de AAD válida que desee aprovisionar en los cuadros de texto correspondientes: **Nombre**, **Apellidos** y **Correo electrónico**.
   2. Haga clic en Aceptar.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Boomi que proporcione Boomi para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

### <a name="to-assign-users-to-boomi-perform-the-following-steps"></a>Para asignar usuarios a Boomi, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Boomi**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-boomi-tutorial/IC790834.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-boomi-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


