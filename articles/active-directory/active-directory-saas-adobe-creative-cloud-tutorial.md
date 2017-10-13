---
title: "Tutorial: Integración de Azure Active Directory con Adobe Creative Cloud | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Adobe Creative Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: ad2c3999ac8bfc8dfe0fc662ee37b41fff38444f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Tutorial: Integración de Azure Active Directory con Adobe Creative Cloud

En este tutorial, aprenderá a integrar Adobe Creative Cloud con Azure Active Directory (Azure AD).

La integración de Adobe Creative Cloud con Azure AD ofrece las ventajas siguientes:

- En Azure AD puede controlar quién tiene acceso a Adobe Creative Cloud.
- Puede permitir que los usuarios inicien sesión automáticamente en Adobe Creative Cloud (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Adobe Creative Cloud, necesita lo siguiente:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Adobe Creative Cloud

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar Adobe Creative Cloud desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Agregar Adobe Creative Cloud desde la galería
Para configurar la integración de Adobe Creative Cloud en Azure AD, necesita agregar Adobe Creative Cloud desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Adobe Creative Cloud desde la galería, siga este procedimiento:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Adobe Creative Cloud**, seleccione **Adobe Creative Cloud** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Adobe Creative Cloud en la lista de resultados](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Adobe Creative Cloud utilizando usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Adobe Creative Cloud para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Adobe Creative Cloud.

Para establecer la relación de vínculo, en Adobe Creative Cloud, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Adobe Creative Cloud, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Adobe Creative Cloud](#create-an-adobe-creative-cloud-test-user)**: para tener un homólogo de Britta Simon en Adobe Creative Cloud que esté vinculado a la representación de usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Adobe Creative Cloud.

**Para configurar el inicio de sesión único de Azure AD con Adobe Creative Cloud, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de aplicaciones de **Adobe Creative Cloud**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_samlbase.png)

3. En la sección **Dominio y direcciones URL de Adobe Creative Cloud**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por IDP:

    ![Información de dominio y direcciones URL de inicio de sesión único de Adobe Creative Cloud](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://www.okta.com/saml2/service-provider/<token>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<company name>.okta.com/auth/saml20/accauthlinktest`.

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Póngase en contacto con el [equipo de soporte al cliente de Adobe Creative Cloud](https://helpx.adobe.com/in/contact/support.html) para obtener estos valores. 

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Adobe Creative Cloud](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url2.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba el valor como: `https://adobe.com`

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_certificate.png)
     
6. La aplicación Adobe Creative Cloud espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar el valor de estos atributos desde la pestaña **Atributo de usuario** de la aplicación. La siguiente captura de pantalla le muestra un ejemplo de esto.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute.png)

7. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:

    | Nombre del atributo | Valor de atributo |
    | ---------------| ----------------|
    | Nombre |user.givenname |
    | Apellidos |user.surname |
    | Email |user.mail |

    a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_04.png)
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_attribute_05.png)
    
    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
    
    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.
    
8. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_400.png)
    
9. En la sección **Configuración de Adobe Creative Cloud**, haga clic en **Configurar Adobe Creative Cloud** para abrir la ventana **Configurar inicio de sesión**. Copie los valores de **SAML Entity ID y SAML Single Sign-On Service URL** (Identificador de entidad de SAML y Dirección URL del servicio de inicio de sesión único de SAML) de la **sección de referencia rápida**.

    ![Configuración de Adobe Creative Cloud](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_configure.png)
    
10. En otra ventana del explorador web, inicie sesión como administrador en el inquilino de Adobe Creative Cloud.

11. Vaya a **Identity** (Identidad) en el panel de navegación izquierdo y haga clic en su dominio. Realice los pasos siguientes en la sección **Single Sign On Configuration Required** (Configuración de inicio de sesión único necesaria).

    ![Configuración](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_001.png "Configuración")
    
    a. Haga clic en **Browse** (Examinar) para cargar el certificado descargado de Azure AD en **IDP Certificate** (Certificado IDP).
    
    b. En el cuadro de texto **IDP issuer** (Emisor de IDP), pegue el **Id. de entidad de SAML** que copió de la sección **Configurar inicio de sesión** de Azure Portal.
    
    c. En el cuadro de texto **IDP Login URL** (URL de inicio de sesión IDP), pegue la **URL del servicio de SSO de SAML** que copió de la sección **Configurar inicio de sesión** de Azure Portal.
    
    d. Seleccione **HTTP - Redirect** (HTTP - Redireccionamiento) como **IDP Binding** (Enlace IDP).
    
    e. Seleccione **Email Address** (Dirección de correo electrónico) como **User Login Setting** (Configuración de inicio de sesión de usuario).
    
    f. Haga clic en el botón **Guardar** .

12. El panel presentará ahora el archivo XML **"Download Metadata"** (Descargar metadatos). Contiene la URL de EntityDescriptor y la URL de AssertionConsumerService de Adobe. Abra el archivo y configúrelas en la aplicación Azure AD.

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_002.png)

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Use el valor de EntityDescriptor que le proporciona Adobe como **Identificador** en el cuadro de diálogo **Configurar las opciones de la aplicación**.

    b. Use el valor de AssertionConsumerService que le proporciona Adobe como **URL de respuesta** en el cuadro de diálogo **Configurar las opciones de la aplicación**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-an-adobe-creative-cloud-test-user"></a>Creación de un usuario de prueba de Adobe Creative Cloud

Para permitir que los usuarios de Azure AD inicien sesión en Adobe Creative Cloud, deben aprovisionarse en Adobe Creative Cloud. En el caso de Adobe Creative Cloud, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar cuentas de usuario, realice los siguientes pasos:

1. Inicie sesión como administrador en su sitio de la compañía de Adobe Creative Cloud.

2. Agregue el usuario en la consola de Adobe como identificador federado y asígnelo a un grupo de derechos.

    ![Vínculo de Adobe Creative Cloud en la lista de aplicaciones](./media/active-directory-saas-adobe-creative-cloud-tutorial/users.png)  

3. Escriba ahora su dirección de correo electrónico o UPN en el formulario de inicio de sesión de Adobe, presione el tabulador y debería estar federado de nuevo en Azure AD:
    * Acceso web: www.adobe.com/es > Inicio de sesión
    * En la utilidad de una aplicación de escritorio > Inicio de sesión
    * En la aplicación > Ayuda > Inicio de sesión

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, se habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Adobe Creative Cloud.

![Asignación del rol de usuario][200] 

**Para asignar a Britta Simon a Adobe Creative Cloud, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Adobe Creative Cloud**.

    ![Vínculo de Adobe Creative Cloud en la lista de aplicaciones](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Adobe Creative Cloud del panel de acceso, debería iniciar sesión automáticamente en la aplicación Adobe Creative Cloud.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png

