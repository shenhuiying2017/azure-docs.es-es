---
title: "Tutorial: integración de Azure Active Directory con DocuSign | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y DocuSign."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 29c99fdf39d366df90abc070f7b836320935035c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutorial: Integración de Azure Active Directory con DocuSign

En este tutorial, obtendrá información sobre cómo integrar DocuSign con Azure Active Directory (Azure AD).

La integración de DocuSign con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a DocuSign.
- Puede permitir que los usuarios inicien sesión automáticamente en DocuSign (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con DocuSign, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en DocuSign

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de DocuSign desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-docusign-from-the-gallery"></a>Adición de DocuSign desde la galería
Para configurar la integración de DocuSign en Azure AD, deberá agregar DocuSign desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar DocuSign desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **DocuSign**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_search.png)

5. En el panel de resultados, seleccione **DocuSign** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con DocuSign con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de DocuSign para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de DocuSign.

Para establecer esta relación de vínculo, se asigna el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en DocuSign.

Para configurar y probar el inicio de sesión único de Azure AD con DocuSign, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de DocuSign](#creating-a-docusign-test-user)**: para tener un homólogo de Britta Simon en DocuSign que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación DocuSign.

**Para configurar el inicio de sesión único de Azure AD con DocuSign y, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **DocuSign**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_samlbase.png)

3. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_certificate.png) 

4. En la sección **Configuración de DocuSign** de Azure Portal, haga clic en **Configure DocuSign** (Configurar DocuSign) para abrir la ventana de configuración de inicio de sesión. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_configure.png)

5. En otra ventana del explorador web, inicie sesión en el **Portal de administración de DocuSign** como administrador.

6. En el menú de navegación de la izquierda, haga clic en **Domains**(Dominios).
   
    ![Configuración del inicio de sesión único][51]

7. En el panel derecho, haga clic en **Claim Domain**(Reclamar dominio).
   
    ![Configuración del inicio de sesión único][52]

8. En el cuadro de diálogo **Claim a domain** (Reclamar un dominio), en el cuadro de texto **Domain Name** (Nombre de dominio), escriba el dominio de la compañía y haga clic en **Claim** (Reclamar). Asegúrese de que comprueba el dominio y que su estado es activo.
   
    ![Configuración del inicio de sesión único][53]

9. En el menú de la izquierda, haga clic en **Identity Providers**  
   
    ![Configuración del inicio de sesión único][54]
10. En el panel derecho, haga clic en **Add Identity Provider**(Agregar proveedor de identidades). 
   
    ![Configuración del inicio de sesión único][55]

11. En la página **Identity Provider Settings** (Configuración del proveedor de identidades), siga estos pasos:
   
    ![Configuración del inicio de sesión único][56]

    a. En el cuadro de texto **Name** (Nombre), escriba un nombre único para la configuración. No utilice espacios.

    b. Pegue el **identificador de entidad de SAML** en el cuadro de texto **Emisor de proveedor de identidades**.

    c. Pegue la **dirección URL de servicio de inicio de sesión único de SAML** en el cuadro de texto **Identity Provider Login URL** (URL de inicio de sesión del proveedor de identidades).

    d. Pegue la **dirección URL de cierre de sesión** en el cuadro de texto **Identity Provider Login URL** (URL de cierre de sesión del proveedor de identidades).

    e. Seleccione **Sign AuthN Request**(Firmar solicitud de autenticación).

    f. En **Send AuthN request by** (Enviar solicitud de autorización por), seleccione **POST**.

    g. En **Send logout request by** (Enviar solicitud de cierre de sesión por), seleccione **GET**.

12. En la sección **Custom Attribute Mapping** (Asignación de atributos personalizados), elija el campo que desea asignar con la notificación de Azure AD. En este ejemplo, la notificación **emailaddress** está asociada con el valor de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Este es el nombre de notificación predeterminado de Azure AD para la notificación de correo electrónico. 
   
    > [!NOTE]
    > Utilice el **identificador de usuario** adecuado para asignar el usuario de Azure AD a la asignación de usuarios de DocuSign. Seleccione el campo apropiado y escriba el valor adecuado según la configuración de la organización.
          
    ![Configuración del inicio de sesión único][57]

13. En la sección **Identity Provider Certificate** (Certificado del proveedor de identidades), haga clic en **Add Certificate** (Agregar certificado) y cargue el certificado que ha descargado del Portal del portal de Azure AD.   
   
    ![Configuración del inicio de sesión único][58]

14. Haga clic en **Save**.

15. En la sección **Identity Providers** (Proveedores de identidades), haga clic en **Actions** (Acciones) y luego en **Endpoints** (Puntos de conexión).   
   
    ![Configuración del inicio de sesión único][59]
 
16. En el **Portal de administración de DocuSign**, en la sección **View SAML 2.0 Endpoints** (Ver puntos de conexión de SAML 2.0), siga estos pasos:
   
    ![Configuración del inicio de sesión único][60]
   
    a. Copie la **dirección URL del emisor del proveedor de servicios** y, a continuación, pegue en el cuadro de texto **Identificador** en la sección **Dominio y direcciones URL de DocuSign** de Azure Portal siguiendo el siguiente patrón: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>`.
   
    b. Copie la **dirección URL de inicio del proveedor de servicios** y, a continuación, pegue en el cuadro de texto **URL de inicio de sesión** en la sección **Dominio y direcciones URL de DocuSign** de Azure Portal siguiendo el siguiente patrón: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/`.

    ![Configurar inicio de sesión único](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_url.png)
      
    c.  Haga clic en **Close**
    
17. En Azure Portal, haga clic en **Guardar**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-docusign-test-user"></a>Creación de un usuario de prueba de DocuSign

La aplicación admite **aprovisionamiento de usuarios Just-In-Time** y, tras la autenticación, los usuarios se crean automáticamente en la aplicación.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a DocuSign.

![Asignar usuario][200] 

**Para asignar Britta Simon a DocuSign, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **DocuSign**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de DocuSign en el panel de acceso, debería iniciar sesión automáticamente en su aplicación DocuSign.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configuración del aprovisionamiento de usuarios](active-directory-saas-docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
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
[100]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_203.png

