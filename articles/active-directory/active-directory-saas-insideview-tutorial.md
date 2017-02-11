---
title: "Tutorial: integración de Azure Active Directory con InsideView | Microsoft Docs"
description: "Aprenda cómo usar InsideView con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0da7c8f20fb6bbe78294aab2fb69c24a2dddd877


---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Tutorial: integración de Azure Active Directory con InsideView
El objetivo de este tutorial es mostrar la integración de Azure e InsideView.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino de InsideView

Después de completar este tutorial, los usuarios de Azure AD que haya asignado a InsideView podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de InsideView (inicio de sesión iniciado por el proveedor de servicios) o desde [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para InsideView
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-insideview-tutorial/IC794128.png "Scenario")

## <a name="enabling-the-application-integration-for-insideview"></a>Habilitación de la integración de aplicaciones para InsideView
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para InsideView.

### <a name="to-enable-the-application-integration-for-insideview-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para InsideView:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-insideview-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-insideview-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-insideview-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **InsideView**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-insideview-tutorial/IC794129.png "Application Gallery")
7. En el panel de resultados, seleccione **InsideView** y luego haga clic en **Completar** para agregar la aplicación.
   
   ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
   
   ## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único

El objetivo de esta sección es describir cómo permitir a los usuarios autenticarse en InsideView con su cuenta de Azure AD mediante federación basada en el protocolo SAML.  
Como parte de este procedimiento, se requiere crear un archivo de certificado codificado en base 64.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **InsideView**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-insideview-tutorial/IC794131.png "Configure Single SignOn")
2. En la página **¿Cómo desea que los usuarios inicien sesión en InsideView?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-insideview-tutorial/IC794132.png "Configure Single SignOn")
3. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto de **URL de respuesta de InsideView,** escriba la dirección URL de inicio de sesión único de InsideView (por ejemplo, `https://my.insideview.com/iv/<STS Name>/login.iv`) y haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-insideview-tutorial/IC794133.png "Configure App URL")
4. En la página **Configuración de inicio de sesión único en InsideView**, para descargar el certificado, haga clic en **Descargar certificado** y luego guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-insideview-tutorial/IC794134.png "Configure Single SignOn")
5. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de InsideView.
6. En la barra de herramientas en la parte superior, haga clic en**Admin**, **Configuración de inicio de sesión único** y luego haga clic en**Agregar SAML**.
   
   ![Configurar inicio de sesión único de SAML](./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML Single Sign On Settings")
7. En la sección **Agregar un nuevo SAML** , realice estos pasos:
   
   ![Agregar un nuevo SAML](./media/active-directory-saas-insideview-tutorial/IC794136.png "Add a New SAML")
   
   1. En el cuadro de texto **Nombre STS** , escriba el nombre de la configuración.
   2. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en InsideView**, copie el valor de **Punto de conexión iniciado por el proveedor de servicios** y luego péguelo en el cuadro de texto **Punto de conexión no solicitado SamlP/WS-Fed**.
   3. Cree un archivo **codificado en base 64** a partir del certificado descargado.
      
      > [!TIP]
      > Para obtener más información, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Abra el certificado codificado en base 64 en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado STS** .
   5. En el cuadro de texto **Crm User Id Mapping** (Asignación de identificador de usuario de CRM), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
   6. En el cuadro de texto **Crm Email Mapping** (Asignación de correo electrónico de CRM), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
   7. En el cuadro de texto **Crm First Name Mapping** (Asignación de nombre de CRM), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
   8. En el cuadro de texto **Crm lastName Mapping** (Asignación de apellidos de CRM), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
   9. Haga clic en **Guardar**.
8. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-insideview-tutorial/IC794137.png "Configure Single SignOn")
   
   ## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario

Para permitir que los usuarios de Azure AD inicien sesión en InsideView, tienen que aprovisionarse en InsideView.  
En el caso de InsideView, el aprovisionamiento es una tarea manual.

Para obtener los usuarios o contactos creados en InsideView, póngase en contacto con su administrador de éxito de clientes o envíe un correo electrónico a **support@insideview.com**

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de InsideView suministrada por InsideView para aprovisionar cuentas de usuario de Azure AD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, tiene que conceder acceso, mediante su asignación, a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-insideview-perform-the-following-steps"></a>Para asignar usuarios a InsideView, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **InsideView**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-insideview-tutorial/IC794138.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-insideview-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


