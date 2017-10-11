---
title: "Tutorial: integración de Azure Active Directory con TOPdesk - Secure | Microsoft Docs"
description: "Aprenda cómo usar TOPdesk - Secure con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 8e149d2d-7849-48ec-9993-31f4ade5fdb4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: 28f0542dbe87bb34c83a7852db7c3a9fef055ce9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Tutorial: integración de Azure Active Directory con TOPdesk - Secure
El objetivo de este tutorial es mostrar la integración de Azure y TOPdesk - Secure.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en TOPdesk - Secure

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a TOPdesk - Secure podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de TOPdesk - Secure (inicio de sesión iniciado por el proveedor de servicios) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para TOPdesk - Secure
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "Escenario")

## <a name="enabling-the-application-integration-for-topdesk---secure"></a>Habilitación de la integración de aplicaciones para TOPdesk - Secure
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para TOPdesk - Secure.

### <a name="to-enable-the-application-integration-for-topdesk---secure-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para TOPdesk - Secure:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory**, seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications**, en el menú superior de la vista de directorios.
   
    ![Aplicaciones](./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "Aplicaciones")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicaciones](./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "Agregar aplicaciones")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "Agregar una aplicación de la galería")

6. En el **cuadro de búsqueda**, escriba **TOPdesk - Secure**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Galería de aplicaciones")

7. En el panel de resultados, seleccione **TOPdesk - Secure** y, luego, haga clic en **Completar** para agregar la aplicación.
   
    ![TOPdesk - Secure](./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - Secure")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en TOPdesk - Secure con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
La configuración del inicio de sesión único para TOPdesk - Secure requiere cargar un archivo de icono de logotipo. Para obtener el archivo de icono, póngase en contacto con el equipo de soporte técnico de TOPdesk.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. Inicie sesión en su sitio de la compañía de **TOPdesk - Secure** como administrador.
2. En el menú **TOPdesk**, haga clic en **Configuración**.
   
    ![Configuración](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Configuración")

3. Haga clic en **Login Settings**(Configuración de inicio de sesión).
   
    ![Configuración de inicio de sesión](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Configuración de inicio de sesión")

4. Expanda el menú **Login Settings** (Configuración de inicio de sesión) y luego haga clic en **General**.
   
    ![General](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "General")

5. En la sección **Secure** (Seguro) de la sección de configuración **SAML login** (Inicio de sesión SAML), realice los pasos siguientes:
   
    ![Configuración técnica](./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "Configuración técnica")
   
    a. Haga clic en **Download** (Descargar) para descargar el archivo de metadatos público y luego guárdelo localmente en el equipo.
   
    b. Abra el archivo de metadatos y luego busque el nodo **AssertionConsumerService** .
    
    ![Servicio de consumidor de aserciones](./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "Servicio de consumidor de aserciones")
   
    c. Copie el valor **AssertionConsumerService** .  
      
    > [!NOTE]
    > Necesitará el valor de la sección **Configurar dirección URL de la aplicación** más adelante en este tutorial.
    > 
    > 

6. En otra ventana del explorador web, inicie sesión en su **Portal de Azure clásico** como administrador.

7. En el **TOPdesk - Secure** página de integración de aplicaciones, haga clic en **configurar inicio de sesión único** para abrir el ** configurar inicio de sesión único ** cuadro de diálogo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Configurar inicio de sesión único")

8. En la página **¿Cómo desea que los usuarios inicien sesión en TOPdesk - Secure?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Configurar inicio de sesión único")

9. En la página **Configurar dirección URL de la aplicación** , realice los pasos siguientes:
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "Configurar dirección URL de la aplicación")
   
    a. En el cuadro de texto **URL de inicio de sesión de TOPdesk - Secure**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación TOPdesk - Secure (por ejemplo, "*https://qssolutions.topdesk.net*").
   
    b. En el cuadro de texto **URL de respuesta de TOPdesk – Secure**, pegue la **dirección URL de AssertionConsumerService de TOPdesk - Public** (por ejemplo, "*https://qssolutions.topdesk.net/tas/public/login/saml*").
   
    c. Haga clic en **Siguiente**.

10. En la página **Configurar inicio de sesión único en TOPdesk - Secure**, para descargar su archivo de metadatos, haga clic en **Descargar metadatos** y luego guarde el archivo localmente en el equipo.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Configurar inicio de sesión único")

11. Lleve a cabo los siguientes pasos para crear un archivo de certificado:
    
    ![Certificado](./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "Certificado")
    
    a. Abra el archivo de metadatos descargado.
    b. Expanda el nodo **RoleDescriptor** cuyo **xsi:type** es **fed:ApplicationServiceType**.
    c. Copie el valor del nodo **X509Certificate** .
    d. Guarde el valor de **X509Certificate** copiado localmente en el equipo en un archivo.

12. En el sitio de la compañía de TOPdesk - Secure, en el menú **TOPdesk**, haga clic en **Settings** (Configuración).
    
    ![Configuración](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Configuración")

13. Haga clic en **Login Settings**(Configuración de inicio de sesión).
    
    ![Configuración de inicio de sesión](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Configuración de inicio de sesión")

14. Expanda el menú **Login Settings** (Configuración de inicio de sesión) y luego haga clic en **General**.
    
    ![General](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "General")

15. En la sección **Public** (Público), haga clic en **Add** (Agregar).
    
    ![Agregar](./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "Agregar")

16. En la página de diálogo del **SAML configuration assistant** (Asistente de configuración de SAML), realice los siguientes pasos:
    
    ![Asistente para configuración de SAML](./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "Asistente de configuración de SAML")
    
    a. Para cargar el archivo de metadatos que ha descargado en **Federation Metadata** (Metadatos de federación), haga clic en **Browse** (Examinar).

    b. Para cargar el archivo del certificado, en **Certificate (RSA)** (Certificado [RSA]), haga clic en **Browse** (Examinar).

    c. Para cargar el archivo de logotipo que obtuvo del equipo de soporte técnico de TOPdesk, en **Logo icon** (Icono de logotipo), haga clic en **Browse** (Examinar).

    d. En el cuadro de texto **User name attribute** (Atributo de nombre de usuario), escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    e. En el cuadro de texto **Display name** (Nombre para mostrar), escriba un nombre para su configuración.

    f. Haga clic en **Save**.

17. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Configurar inicio de sesión único")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en TOPdesk - Secure, deben aprovisionarse en TOPdesk - Secure.  
En el caso de TOPdesk - Secure, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en su sitio de la compañía de **TOPdesk - Secure** como administrador.
2. En el menú de la parte superior, haga clic en **TOPdesk \> New \> Support Files \> Operator** (TOPdesk > Nuevo > Archivos de soporte > Operador).
   
    ![Operador](./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "Operador")

3. En el cuadro de diálogo **Nuevo operador** , realice los pasos siguientes:
   
    ![New operador](./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "Nuevo operador")
   
    a. Haga clic en la pestaña General.
   
    b. En el cuadro de texto **Apellido** de la sección **General**, escriba los apellidos de una cuenta válida de Azure Active Directory que desee aprovisionar.
   
    c. Seleccione un **sitio** para la cuenta en la sección **Location** (Ubicación).
   
    d. En el cuadro de texto **Login Name** (Nombre de inicio de sesión) de la sección **TOPdesk Login** (Inicio de sesión de TOPdesk), escriba el nombre de inicio de sesión del usuario.
   
    e. Haga clic en **Save**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de TOPdesk - Secure ofrecida por TOPdesk - Secure para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-topdesk---secure-perform-the-following-steps"></a>Para asignar usuarios a TOPdesk - Secure, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En el ** TOPdesk - Secure ** página de integración de aplicaciones, haga clic en **asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "Asignar usuarios")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

