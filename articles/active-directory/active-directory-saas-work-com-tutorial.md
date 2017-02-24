---
title: "Tutorial: Integración de Azure Active Directory con Work.com | Microsoft Docs"
description: "Aprenda cómo usar Work.com con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: 69d94659f4eff72e1c449fd915616d81fd4712de


---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Tutorial: Integración de Azure Active Directory con Work.com
El objetivo de este tutorial es mostrar la integración de Azure y Work.com.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en Work.com

Después de completar este tutorial, los usuarios de AAD a los que ha asignado acceso a Work.com podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de Work.com (inicio de sesión iniciado por el proveedor del servicio) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones en Work.com
2. Configuración del inicio de sesión único
3. Configuración del aprovisionamiento de usuario
4. Asignación de usuarios

![Escenario](./media/active-directory-saas-work-com-tutorial/IC794105.png "Escenario")

## <a name="enabling-the-application-integration-for-workcom"></a>Habilitación de la integración de aplicaciones en Work.com
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para Work.com.

### <a name="to-enable-the-application-integration-for-workcom-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para Work.com:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.

3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Aplicaciones](./media/active-directory-saas-work-com-tutorial/IC700994.png "Aplicaciones")

4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Agregar aplicaciones](./media/active-directory-saas-work-com-tutorial/IC749321.png "Agregar aplicaciones")

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Agregar una aplicación de la galería](./media/active-directory-saas-work-com-tutorial/IC749322.png "Agregar una aplicación de la galería")

6. En el **cuadro de búsqueda**, escriba **Work.com**.
   
    ![Galería de aplicaciones](./media/active-directory-saas-work-com-tutorial/IC794106.png "Galería de aplicaciones")

7. En el panel de resultados, seleccione **Work.com** y luego haga clic en **Completar** para agregar la aplicación.
   
    ![Work.com](./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en Work.com con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
Como parte de este procedimiento, es necesario cargar un certificado en Work.com.com.

> [!NOTE]
> Para configurar el inicio de sesión único, deberá configurar todavía un nombre de dominio personalizado de Work.com. Deberá definir al menos un nombre de dominio, probar su nombre de dominio e implementarlo en toda la organización.
> 
> 

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. Inicie sesión en su inquilino de Work.com como administrador.
2. Acceda a **Setup**(Configuración).
   
    ![Instalación](./media/active-directory-saas-work-com-tutorial/IC794108.png "Instalación")

3. En el panel de navegación izquierdo, en la sección **Administer** (Administrar), haga clic en **Domain Management** (Administración de dominios) para expandir la sección relacionada y, luego, haga clic en **My Domain** (Mi dominio) para abrir la página **My Domain** (Mi dominio). 
   
    ![Mi dominio](./media/active-directory-saas-work-com-tutorial/IC767825.png "Mi dominio")

4. Para comprobar que el dominio se ha configurado correctamente, asegúrese de que está en "**Step 4 Deployed to Users**” (Paso 4 Dominio implementado para usuarios) y revise la sección "**My Domain Settings**" (Mi configuración de dominio).
   
    ![Dominio implementado al usuario](./media/active-directory-saas-work-com-tutorial/IC784377.png "Dominio implementado al usuario")

5. En otra ventana del explorador web, inicie sesión en el Portal de Azure clásico.

6. En la página de integración de aplicaciones de **Work.com**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo **Configurar inicio de sesión único**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-work-com-tutorial/IC794109.png "Configurar inicio de sesión único")

7. En la página **How would you like users to sign on to Work.com** (¿Cómo desea que los usuarios inicien sesión en Work.com?), seleccione **Inicio de sesión único de Microsoft Azure AD** y, luego, haga clic en **Siguiente**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-work-com-tutorial/IC794110.png "Configurar inicio de sesión único")

8. En la página **Configurar dirección URL de la aplicación**, en el cuadro de texto **URL de inicio de sesión de Work.com**, escriba la dirección URL que utilizan los usuarios para iniciar sesión en su aplicación Work.com (por ejemplo: "*http://company.my.salesforce.com*) y luego haga clic en **Siguiente**: 
   
    ![Configurar dirección URL de la aplicación](./media/active-directory-saas-work-com-tutorial/IC794111.png "Configurar dirección URL de la aplicación")

9. En la página **Configurar inicio de sesión único en Work.com**, para descargar el certificado, haga clic en **Descargar certificado** y, a continuación, guarde el archivo de certificado en el equipo.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-work-com-tutorial/IC794112.png "Configurar inicio de sesión único")

10. Inicie sesión en su inquilino de Work.com.

11. Acceda a **Setup**(Configuración).
    
    ![Instalación](./media/active-directory-saas-work-com-tutorial/IC794108.png "Instalación")

12. Expanda el menú **Security Controls** (Controles de seguridad) y luego haga clic en **Single Sign-On Settings** (Configuración de inicio de sesión único).
    
    ![Configuración de inicio de sesión único](./media/active-directory-saas-work-com-tutorial/IC794113.png "Configuración de inicio de sesión único")

13. En la página del cuadro de diálogo **Single Sign-On Settings** (Configuración de inicio de sesión único), siga estos pasos:
    
    ![SAML habilitado](./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML habilitado")
    
    a. Seleccione **SAML habilitado**.
    
    b. Haga clic en **Nuevo**.

14. En la sección **SAML Single Sign-On Settings** (Configuración del inicio de sesión único de SAML), siga estos pasos:
    
    ![Inicio de sesión único SAML](./media/active-directory-saas-work-com-tutorial/IC794114.png "Inicio de sesión único SAML")
    
    a. En el cuadro de texto **Name** (Nombre), escriba el nombre de la configuración.  
       
    > [!NOTE]
    > Si se proporciona un valor para **Name** (Nombre), el cuadro de texto **API Name** (Nombre de API) se completa automáticamente.
    > 
    > 
    
    b. En el Portal de Azure clásico, en la página del cuadro de diálogo **Configurar inicio de sesión único en Work.com**, copie el valor de **URL del emisor** y péguelo en el cuadro de texto **Emisor**.
    
    c. Para cargar el certificado descargado, haga clic en **Examinar**.
    
    d. En el cuadro de texto **Id. de entidad**, escriba **https://salesforce-work.com**.
    
    e. Como **SAML Identity Type** (Tipo de identidad SAML), seleccione **Assertion contains the Federation ID from the User object** (La aserción contiene el id. de federación del objeto Usuario).
    
    f. Para **///SAML Identity Location** (Ubicación de identidad SAML), seleccione **///Identity is in the NameIdentfier element of the Subject statement** (La identidad está en el elemento NameIdentifier de la instrucción Subject).
    
    g. En el Portal de Azure clásico, en la página del cuadro de diálogo **Configurar inicio de sesión único en Work.com**, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **Identity Provider Login URL** (Dirección URL de inicio de sesión de proveedor de identidades).
    
    h. En el Portal de Azure clásico, en la página del cuadro de diálogo **Configurar inicio de sesión único en Work.com**, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **URL de cierre de sesión del proveedor de identidades**.
    
    i. Para **Service Provider Initiated Request Binding** (Enlace de solicitud iniciada por el proveedor de servicio), seleccione **HTTP Post** (Método HTTP Post).
    
    j. Haga clic en **Save**.

15. En el portal de Work.com, en el panel de navegación izquierdo, haga clic en **Domain Management** (Administración de dominios) para expandir la sección relacionada y luego haga clic en la página **My Domain** (Mi dominio) para abrir la página **My Domain** (Mi dominio). 
    
    ![Mi dominio](./media/active-directory-saas-work-com-tutorial/IC794115.png "Mi dominio")

16. En la página **My domain** (Mi dominio), en la sección **Login Page Branding** (Personalización de marca de la página de inicio de sesión), haga clic en **Edit** (Editar).
    
    ![Personalización de marca de página de inicio de sesión](./media/active-directory-saas-work-com-tutorial/IC767826.png "Personalización de marca de página de inicio de sesión")

17. En la página **Login Page Branding** (Personalización de marca de la página de inicio de sesión), en la sección **Authentication Service** (Servicio de autenticación), se muestra el nombre de su **SAML SSO Settings** (Configuración de SSO de SAML). Selecciónelo y luego haga clic en **Save**(Guardar).
    
    ![Personalización de marca de página de inicio de sesión](./media/active-directory-saas-work-com-tutorial/IC784366.png "Personalización de marca de página de inicio de sesión")

18. En el Portal de Azure clásico, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar** para cerrar el cuadro de diálogo **Configurar inicio de sesión único**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-work-com-tutorial/IC794116.png "Configurar inicio de sesión único")

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para que los usuarios de Azure Active Directory puedan iniciar sesión, deben aprovisionarse a Work.com.  
En el caso de Work.com, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en su sitio de la compañía de Work.com como administrador.

2. Acceda a **Setup**(Configuración).
   
    ![Instalación](./media/active-directory-saas-work-com-tutorial/IC794108.png "Instalación")
3. Vaya a **Manage Users \> (Administrar usuarios) Users** (Usuarios).
   
    ![Administración de usuarios](./media/active-directory-saas-work-com-tutorial/IC784369.png "Administración de usuarios")

4. Haga clic en **Nuevo usuario**.
   
    ![Todos los usuarios](./media/active-directory-saas-work-com-tutorial/IC794117.png "Todos los usuarios")

5. En la sección Edición de usuario, lleve a cabo estos pasos:
   
    ![Edición de usuario](./media/active-directory-saas-work-com-tutorial/IC794118.png "Edición de usuario")
   
    a. Escriba los atributos para **Last Name** (Apellido), **Alias**, **Email** (Correo electrónico), **Username** (Nombre de usuario) y **Nickname** (Sobrenombre) de una cuenta de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.
   
    b. Seleccione **Role** (Rol), **User License** (Licencia de usuario) y **Profile** (Perfil).
   
    c. Haga clic en **Save**.  
      
    > [!NOTE]
    > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.
    > 
    > 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-workcom-perform-the-following-steps"></a>Para asignar usuarios a Work.com, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.

2. En la página de integración de la aplicación Work.com, haga clic en **Asignar usuarios**.
   
    ![Asignar usuarios](./media/active-directory-saas-work-com-tutorial/IC794119.png "Asignar usuarios")

3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Sí](./media/active-directory-saas-work-com-tutorial/IC767830.png "Sí")

Ahora debería esperar 10 minutos y comprobar si la cuenta se ha sincronizado en Work.com.com.

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Feb17_HO2-->


