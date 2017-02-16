---
title: "Tutorial: integración de Azure Active Directory con TOPdesk - Public | Microsoft Docs"
description: "Aprenda cómo usar TOPdesk - Public con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 03df35cff59f9de4642de7b5e851199d593129a9


---
# <a name="tutorial-azure-directory-integration-with-topdesk---public"></a>Tutorial: integración de Azure Directory con TOPdesk - Public
El objetivo de este tutorial es mostrar la integración de Azure y TOPdesk - Public.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en TOPdesk - Public

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a TOPdesk - Public podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de TOPdesk - Public (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para TOPdesk - Public
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-topdesk-public-tutorial/IC790613.png "Scenario")

## <a name="enabling-the-application-integration-for-topdesk---public"></a>Habilitación de la integración de aplicaciones para TOPdesk - Public
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para TOPdesk - Public.

### <a name="to-enable-the-application-integration-for-topdesk---public-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para TOPdesk - Public:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-topdesk-public-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Applications](./media/active-directory-saas-topdesk-public-tutorial/IC700994.png "Applications")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicación](./media/active-directory-saas-topdesk-public-tutorial/IC749321.png "Add application")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-topdesk-public-tutorial/IC749322.png "Add an application from gallerry")
6. En el **cuadro de búsqueda**, escriba **TOPdesk - Public**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-topdesk-public-tutorial/IC790614.png "Application Gallery")
7. En el panel de resultados, seleccione **TOPdesk - Public** y, luego, haga clic en **Completar** para agregar la aplicación.
   
   ![TOPdesk - Public](./media/active-directory-saas-topdesk-public-tutorial/IC791317.png "TOPdesk Public")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en TOPdesk - Public con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
La configuración del inicio de sesión único para TOPdesk - Public requiere cargar un archivo de icono de logotipo. Para obtener el archivo de icono, póngase en contacto con el equipo de soporte técnico de TOPdesk.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. Inicie sesión en el sitio de la compañía de **TOPdesk - Public** como administrador.
2. En el menú **TOPdesk**, haga clic en **Configuración**.
   
   ![Configuración](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Settings")
3. Haga clic en **Login Settings**(Configuración de inicio de sesión).
   
   ![Login Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Login Settings")
4. Expanda el menú **Login Settings** (Configuración de inicio de sesión) y luego haga clic en **General**.
   
   ![General](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")
5. En la sección **Público** de la sección de configuración **Inicio de sesión SAML**, siga estos pasos:
   
   ![Configuración técnica](./media/active-directory-saas-topdesk-public-tutorial/IC790601.png "Technical Settings")
   
   1. Haga clic en **Download** (Descargar) para descargar el archivo de metadatos público y luego guárdelo localmente en el equipo.
   2. Abra el archivo de metadatos y luego busque el nodo **AssertionConsumerService** .
      ![AssertionConsumerService](./media/active-directory-saas-topdesk-public-tutorial/IC790619.png "AssertionConsumerService")
   3. Copie el valor **AssertionConsumerService** .  
      
      > [!NOTE]
      > Necesitará el valor de la sección **Configurar dirección URL de la aplicación** más adelante en este tutorial.
      > 
      > 
6. En otra ventana del explorador web, inicie sesión en su **Portal de Azure clásico** como administrador.
7. En la página de integración de la aplicación **TOPdesk - Public**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-topdesk-public-tutorial/IC790620.png "Configure Single Sign-On")
8. En la página **¿Cómo desea que los usuarios inicien sesión en TOPdesk - Public?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y luego haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-topdesk-public-tutorial/IC790621.png "Configure Single Sign-On")
9. En la página **Configurar dirección URL de la aplicación** , realice los pasos siguientes:
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-topdesk-public-tutorial/IC790622.png "Configure App URL")
   
   1. En el cuadro de texto **URL de inicio de sesión de TOPdesk - Public**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación TOPdesk - Public (por ejemplo, "*https://qssolutions.topdesk.net*").
   2. En el cuadro de texto **URL de respuesta de TOPdesk – Public**, pegue la **dirección URL de AssertionConsumerService de TOPdesk - Public** (por ejemplo, "*https://qssolutions.topdesk.net/tas/public/login/saml*")
   3. Haga clic en **Next**.
10. En la página **Configurar inicio de sesión único en TOPdesk - Public**, para descargar su archivo de metadatos, haga clic en **Descargar metadatos** y luego guarde el archivo localmente en el equipo.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-topdesk-public-tutorial/IC790623.png "Configure Single Sign-On")
11. Lleve a cabo los siguientes pasos para crear un archivo de certificado:
    
    ![Certificate](./media/active-directory-saas-topdesk-public-tutorial/IC790606.png "Certificate")
    
    1. Abra el archivo de metadatos descargado.
    2. Expanda el nodo **RoleDescriptor** cuyo **xsi:type** es **fed:ApplicationServiceType**.
    3. Copie el valor del nodo **X509Certificate** .
    4. Guarde el valor de **X509Certificate** copiado localmente en el equipo en un archivo.
12. En el sitio de la compañía de TOPdesk - Public, en el menú **TOPdesk**, haga clic en **Settings** (Configuración).
    
    ![Configuración](./media/active-directory-saas-topdesk-public-tutorial/IC790598.png "Settings")
13. Haga clic en **Login Settings**(Configuración de inicio de sesión).
    
    ![Login Settings](./media/active-directory-saas-topdesk-public-tutorial/IC790599.png "Login Settings")
14. Expanda el menú **Login Settings** (Configuración de inicio de sesión) y luego haga clic en **General**.
    
    ![General](./media/active-directory-saas-topdesk-public-tutorial/IC790600.png "General")
15. En la sección **Public** (Público), haga clic en **Add** (Agregar).
    
    ![Inicio de sesión SAML](./media/active-directory-saas-topdesk-public-tutorial/IC790625.png "SAML Login")
16. En la página de diálogo del **SAML configuration assistant** (Asistente de configuración de SAML), realice los siguientes pasos:
    
    ![SAML configuration assistant](./media/active-directory-saas-topdesk-public-tutorial/IC790608.png "SAML Configuration Assistant")
    
    1. Para cargar el archivo de metadatos que ha descargado en **Federation Metadata** (Metadatos de federación), haga clic en **Browse** (Examinar).
    2. Para cargar el archivo del certificado, en **Certificate (RSA)** (Certificado [RSA]), haga clic en **Browse** (Examinar).
    3. Para cargar el archivo de logotipo que obtuvo del equipo de soporte técnico de TOPdesk, en **Logo icon** (Icono de logotipo), haga clic en **Browse** (Examinar).
    4. En el cuadro de texto **User name attribute** (Atributo de nombre de usuario), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    5. En el cuadro de texto **Display name** (Nombre para mostrar), escriba un nombre para su configuración.
    6. Haga clic en **Save**.
17. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-topdesk-public-tutorial/IC790627.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en TOPdesk - Public, deben aprovisionarse en TOPdesk - Public.  
En el caso de TOPdesk - Public, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en su sitio de la compañía de **TOPdesk - Public** como administrador.
2. En el menú de la parte superior, haga clic en **TOPdesk \> New \> Support Files \> Person** (TOPdesk > Nuevo > Archivos de soporte > Persona).
   
   ![Persona](./media/active-directory-saas-topdesk-public-tutorial/IC790628.png "Person")
3. En el cuadro de diálogo Nueva persona, realice los pasos siguientes:
   
   ![Nueva persona](./media/active-directory-saas-topdesk-public-tutorial/IC790629.png "New Person")
   
   1. Haga clic en la pestaña General.
   2. En el cuadro de texto Apellido, escriba el apellido de una cuenta válida de Azure Active Directory que quiera aprovisionar.
   3. Seleccione un **sitio** para la cuenta.
   4. Haga clic en **Save**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de TOPdesk - Public ofrecida por TOPdesk - Public para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-topdesk---public-perform-the-following-steps"></a>Para asignar usuarios a TOPdesk - Public, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de la aplicación **TOPdesk - Public**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-topdesk-public-tutorial/IC790630.png "Assign Users")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-topdesk-public-tutorial/IC767830.png "Yes")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Nov16_HO3-->


