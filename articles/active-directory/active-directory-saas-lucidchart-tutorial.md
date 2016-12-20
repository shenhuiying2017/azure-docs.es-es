---
title: "Tutorial: Integración de Azure Active Directory con Lucidchart | Microsoft Docs"
description: "Aprenda a usar Lucidchart con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8386497b457f63ba0b62f50301ce3948ce060b97


---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Tutorial: Integración de Azure Active Directory con Lucidchart
El objetivo de este tutorial es mostrar la integración de Azure y Lucidchart.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para inicio de sesión único en Lucidchart

Después de completar este tutorial, los usuarios de Azure AD asignados a Lucidchart podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Lucidchart (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para Lucidchart
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Scenario")

## <a name="enabling-the-application-integration-for-lucidchart"></a>Habilitación de la integración de aplicaciones para Lucidchart
El objetivo de esta sección es describir cómo habilitar la integración de aplicaciones para Lucidchart.

### <a name="to-enable-the-application-integration-for-lucidchart-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Lucidchart:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **Lucidchart**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Application Gallery")
7. En el panel de resultados, seleccione **Lucidchart** y, después, haga clic en **Completar** para agregar la aplicación.
   
   ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo habilitar la autenticación de usuarios en Lucidchart con su cuenta de Azure AD mediante federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **Lucidchart**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configure Single Sign-On")
2. En la página **¿Cómo desea que los usuarios inicien sesión en Lucidchart?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configure Single Sign-On")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Lucidchart**, escriba la dirección URL que usan los usuarios para iniciar sesión en la aplicación de Lucidchart (por ejemplo: "*https://chart2.office.lucidchart.com/saml/sso/azure*") y, después, haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configure App URL")
4. En la página **Configurar inicio de sesión único en Lucidchart**, para descargar sus metadatos, haga clic en **Descargar metadatos** y, después, guarde el archivo de datos de forma local en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configure Single Sign-On")
5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Lucidchart.
6. En el menú de la parte superior, haga clic en **Equipo**.
   
   ![Team](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Team")
7. Haga clic en **Aplicación \> Administrar SAML**.
   
   ![Administrar SAML](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Manage SAML")
8. En la página de diálogo **Configuración de la autenticación SAML** , realice los pasos siguientes:
   
   1. Seleccione **Habilitar autenticación SAML** y, después, haga clic en **Opcional**.
      ![Configuración de la autenticación SAML](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "SAML Authentication Settings")
   2. En el cuadro de texto **Dominio**, escriba el dominio y, después, haga clic en **Cambiar certificado**.
      ![Cambiar certificado](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Change Certificate")
   3. Abra el archivo de metadatos descargado, copie el contenido y luego péguelo en el cuadro de texto **Cargar metadatos** .
      ![Cargar metadatos](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Upload Metadata")
   4. Seleccione **Agregar nuevo usuario al equipo automáticamente** y, después, haga clic en **Guardar los cambios**.
      ![Save Changes](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Save Changes")
9. Seleccione la confirmación de la configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

No hay ningún elemento de acción para que configure el aprovisionamiento de usuarios para Lucidchart.  
Cuando un usuario asignado intenta iniciar sesión en Lucidchart desde el Panel de acceso, Lucidchart comprueba si el usuario existe.  
Si no hay cuentas de usuario disponibles, Lucidchart crea una automáticamente.

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe asignar los usuarios de Azure AD que quiera que usen su aplicación para concederles acceso a ella.

### <a name="to-assign-users-to-lucidchart-perform-the-following-steps"></a>Para asignar usuarios a Lucidchart, siga estos pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **Lucidchart**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


