---
title: "Tutorial: integración de Azure Active Directory con DocuSign | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y DocuSign."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ac53d0a2c1c0a257e86fe74e10c8781aa6305975
ms.openlocfilehash: 662e853ca31d426a71121d22733256556ca56fc6
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutorial: Integración de Azure Active Directory con DocuSign
El objetivo de este tutorial es mostrar la integración de Azure y DocuSign.
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Un inquilino en DocuSign

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. [Habilitación de la integración de aplicaciones para DocuSign](#enabling-the-application-integration-for-docusign) 
2. [Configuración del inicio de sesión único](#configuring-single-sign-on) 
3. [Configuración del aprovisionamiento de cuentas](#configuring-account-provisioning) 
4. [Asignación de usuarios](#assigning-users) 
   
![Configuración del inicio de sesión único][0]

## <a name="enabling-the-application-integration-for-docusign"></a>Habilitación de la integración de aplicaciones para DocuSign
El objetivo de esta sección es describir cómo se habilita la integración de aplicaciones para DocuSign.

### <a name="to-enable-the-application-integration-for-docusign-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para DocuSign:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
    ![Configuración del inicio de sesión único][1]
2. En la lista Directory, seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Configuración del inicio de sesión único][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Applications][3]
5. En el cuadro de diálogo ¿Qué desea hacer?, haga clic en **Agregar una aplicación de la galería**.
   
    ![Configuración del inicio de sesión único][4]
6. En el cuadro de búsqueda, escriba **DocuSign**.
   
    ![Configuración del inicio de sesión único][5]
7. En el panel de resultados, seleccione **DocuSign** y haga clic en **Completar** para agregar la aplicación.
   
    ![Configuración del inicio de sesión único][6]

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo permitir que los usuarios se autentiquen en DocuSign con su cuenta de Azure AD a través de la federación basada en el protocolo SAML.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de **Integración de la aplicación DocuSign**, haga clic en **Configurar inicio de sesión único** para abrir el cuadro de diálogo Configurar inicio de sesión único.
   
    ![Configuración del inicio de sesión único][7]
2. En la página **¿Cómo desea que los usuarios inicien sesión en DocuSign?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en Siguiente.
   
    ![Configuración del inicio de sesión único][8]
3. En la página **Configurar las opciones de la aplicación** , realice los pasos siguientes:
   
    ![Configuración del inicio de sesión único][61]
   
    a. En el cuadro de texto **URL de inicio de sesión**, escriba `https://account.docusign.com/*`.  
   
    b. En el cuadro de texto **Identificador**, escriba: `https://account.docusign.com/*`.  
   
    c. Haga clic en **Next**. 

    > [!TIP] 
    > Los valores de URL de inicio de sesión e Identificador son solo marcadores de posición. Las instrucciones para recuperar los valores reales de su entorno se proporcionan más adelante en este tema.


1. En la página **Configurar inicio de sesión único en DocuSign**, haga clic en **Descargar certificado** y, después, guarde el archivo de certificado localmente en el equipo.
   
    ![Configuración del inicio de sesión único][10]
2. En otra ventana del explorador web, inicie sesión en el **Portal de administración de DocuSign** como administrador.
3. En el menú de navegación de la izquierda, haga clic en **Domains**(Dominios).
   
    ![Configuración del inicio de sesión único][51]
4. En el panel derecho, haga clic en **Claim Domain**(Reclamar dominio).
   
    ![Configuración del inicio de sesión único][52]
5. En el cuadro de diálogo **Claim a domain** (Reclamar un dominio), en el cuadro de texto **Domain Name** (Nombre de dominio), escriba el dominio de la compañía y haga clic en **Claim** (Reclamar). Asegúrese de que comprueba el dominio y que su estado es activo.
   
    ![Configuración del inicio de sesión único][53]
6. En el menú de la izquierda, haga clic en **Identity Providers**  
   
    ![Configuración del inicio de sesión único][54]
7. En el panel derecho, haga clic en **Add Identity Provider**(Agregar proveedor de identidades). 
   
    ![Configuración del inicio de sesión único][55]
8. En la página **Identity Provider Settings** (Configuración del proveedor de identidades), siga estos pasos:
   
    ![Configuración del inicio de sesión único][56]

    a. En el cuadro de texto **Name** (Nombre), escriba un nombre único para la configuración. No utilice espacios.

    b. En el Portal de Azure clásico, copie el valor de URL del emisor y péguelo en el cuadro de texto **Identity Provider Issuer** (Emisor del proveedor de identidades).

    c. En el Portal de Azure clásico, copie el valor de **Dirección URL de inicio de sesión remoto** y péguelo en el cuadro de texto **URL de inicio de sesión del proveedor de identidades**.

    d. En el Portal de Azure clásico, copie el valor de **Dirección URL de cierre de sesión remoto** y péguelo en el cuadro de texto **URL de cierre de sesión del proveedor de identidades**.

    e. Seleccione **Sign AuthN Request**(Firmar solicitud de autenticación).

    f. En **Send AuthN request by** (Enviar solicitud de autorización por), seleccione **POST**.

    g. En **Send logout request by** (Enviar solicitud de cierre de sesión por), seleccione **POST**. 


1. En la sección **Custom Attribute Mapping** (Asignación de atributos personalizados), elija el campo que desea asignar con la notificación de Azure AD. En este ejemplo, la notificación **emailaddress** está asociada con el valor de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Este es el nombre de notificación predeterminado de Azure AD para la notificación de correo electrónico. 
   
    > [!NOTE]
    > Utilice el **identificador de usuario** adecuado para asignar el usuario de Azure AD a la asignación de usuarios de Docusign. Seleccione el campo apropiado y escriba el valor adecuado según la configuración de la organización.
    > 
    > 
   
    ![Configuración del inicio de sesión único][57]
2. En la sección **Identity Provider Certificate** (Certificado del proveedor de identidades), haga clic en **Add Certificate** (Agregar certificado) y cargue el certificado que ha descargado del Portal de Azure AD clásico.   
   
    ![Configuración del inicio de sesión único][58]
3. Haga clic en **Save**.
4. En la sección **Identity Providers** (Proveedores de identidades), haga clic en **Actions** (Acciones) y luego en **Endpoints** (Puntos de conexión).   
   
    ![Configuración del inicio de sesión único][59]
5. En el Portal de Azure clásico, vaya a la página **Configurar las opciones de la aplicación** . 
6. En el **Portal de administración de DocuSign**, en la sección **View SAML 2.0 Endpoints** (Ver puntos de conexión de SAML 2.0), siga estos pasos:
   
    ![Configuración del inicio de sesión único][60]
   
    a. Copie el valor de **Service Provider Issuer URL** (Dirección URL del emisor del proveedor de servicios) y péguelo en el cuadro de texto **Identificador** del Portal de Azure clásico.
   
    b. Copie el valor de **Service Provider Login URL** (Dirección URL de inicio de sesión del proveedor de servicios) y péguelo en el cuadro de texto **URL de inicio de sesión** del Portal de Azure clásico.
   
    c.  Haga clic en **Close**  
7. En el Portal de Azure clásico, haga clic en **Siguiente**. 
8. En el Portal de Azure clásico, seleccione la **confirmación de la configuración de inicio de sesión único** y haga clic en **Siguiente**.
   
    ![Aplicaciones][14]
9. En la página **Confirmación del inicio de sesión único**, haga clic en **Completar**.
   
    ![Aplicaciones][15]

## <a name="configuring-account-provisioning"></a>Configuración del aprovisionamiento de cuentas
El objetivo de esta sección es describir cómo habilitar el aprovisionamiento de usuarios de Active Directory para DocuSign.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. En el **Portal de Azure clásico**, en la página de **integración de la aplicación DocuSign**, haga clic en **Configure account provisioning** (Configurar aprovisionamiento de cuentas) para abrir el cuadro de diálogo Configure User Provisioning (Configurar aprovisionamiento de usuarios).
   
    ![Configuración del aprovisionamiento de cuentas][30]
2. En la página **Configuración y credenciales de administrador**, para habilitar el aprovisionamiento automático de usuarios, especifique las credenciales de una cuenta de DocuSign con derechos suficientes y después haga clic en **Siguiente**. 
   
    ![Configuración del aprovisionamiento de cuentas][31]
3. En el cuadro de diálogo **Probar conexión**, haga clic en **Iniciar prueba** y, cuando el resultado de la prueba sea satisfactorio, haga clic en **Siguiente**.
   
    ![Configuración del aprovisionamiento de cuentas][32]
4. En la página **Confirmación**, haga clic en **Completar**.
   
    ![Configuración del aprovisionamiento de cuentas][33]

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-docusign-perform-the-following-steps"></a>Para asignar usuarios a DocuSign, lleve a cabo los siguientes pasos:
1. En el **Portal de Azure clásico**, cree una cuenta de prueba.
2. En la página de **integración de la aplicación DocuSign**, haga clic en **Asignar usuarios**.
   
    ![Asignación de usuarios][40]
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
    ![Asignación de usuarios][41]

Ahora debería esperar 10 minutos y comprobar si la cuenta se ha sincronizado con DocuSign.

Como primer paso de la comprobación, puede comprobar el estado del aprovisionamiento, para lo que debe hacer clic en Panel en la D de la página de integración de la aplicación DocuSign en el Portal de Azure clásico.

![Asignación de usuarios][42]

Un ciclo de aprovisionamiento de usuarios completado correctamente se indica con un estado relacionado:

![Asignación de usuarios][43]

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso.

Para obtener más información sobre el Panel de acceso, consulte Introducción al Panel de acceso.

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png

