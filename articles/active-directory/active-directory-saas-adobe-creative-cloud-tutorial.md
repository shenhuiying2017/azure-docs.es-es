---
title: "Tutorial: Integración de Azure Active Directory con Adobe Creative Cloud | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Adobe Creative Cloud."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9ba1171e-56b1-4475-b308-58637d35e5a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 3d13608612c77236346b0e98551d7fc427d602e1
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Tutorial: Integración de Azure Active Directory con Adobe Creative Cloud

En este tutorial, aprenderá a integrar Adobe Creative Cloud con Azure Active Directory (Azure AD).

La integración de Adobe Creative Cloud con Azure AD ofrece las ventajas siguientes:

- En Azure AD se puede controlar quién tiene acceso a Adobe Creative Cloud.
- Puede permitir que los usuarios inicien sesión automáticamente en Adobe Creative Cloud (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Adobe Creative Cloud, necesita lo siguiente:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Adobe Creative Cloud

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar Adobe Creative Cloud desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Agregar Adobe Creative Cloud desde la galería
Para configurar la integración de Adobe Creative Cloud en Azure AD, necesita agregar Adobe Creative Cloud desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Adobe Creative Cloud desde la galería, siga este procedimiento:**

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Adobe Creative Cloud**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_000.png)

5. En el panel de resultados, seleccione **Adobe Creative Cloud** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Adobe Creative Cloud utilizando usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Adobe Creative Cloud para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Adobe Creative Cloud.

Esta relación de vínculo se establece mediante la asignación del valor de **nombre de usuario** en Azure AD como valor de **Username** (Nombre de usuario) en Adobe Creative Cloud.

Para configurar y probar el inicio de sesión único de Azure AD con Adobe Creative Cloud, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Adobe Creative Cloud](#creating-an-adobe-creative-cloud-test-user)**: para tener un homólogo de Britta Simon en Adobe Creative Cloud que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de administración de Azure y configurará el inicio de sesión único en la aplicación Adobe Creative Cloud.

**Para configurar el inicio de sesión único de Azure AD con Adobe Creative Cloud, realice los pasos siguientes:**

1. En el Portal de administración de Azure, en la página de integración de aplicaciones de **Adobe Creative Cloud**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_01.png)

3. En la sección **Dominio y direcciones URL de Adobe Creative Cloud**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_url1.png)

    a. En el cuadro de texto **Identificador**, escriba el valor como `https://www.okta.com/saml2/service-provider/<token>`.

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<company name>.okta.com/auth/saml20/accauthlinktest`.

    > [!NOTE] 
    > Tenga en cuenta que estos no son valores reales. Estos valores se tienen que actualizar con los valores reales de Identificador y URL de respuesta. Aquí le recomendamos que utilice el valor de cadena único en el identificador. Si necesita crear manualmente un usuario, es preciso que se ponga en contacto con el equipo de soporte técnico de Adobe Creative Cloud.

4. En la sección **Dominio y direcciones URL de Adobe Creative Cloud**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **SP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_url2.png)

    a. Haga clic en la opción **Mostrar configuración avanzada de URL**.

    b. En el cuadro de texto **URL de inicio de sesión**, escriba el valor como: `https://adobe.com`

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_05.png) 

6. En la sección **Configuración de Adobe Creative Cloud**, haga clic en **Configurar Adobe Creative Cloud** para abrir la ventana **Configurar inicio de sesión**. Copie la **SAML Entity ID and SAML Single Sign-On Service URL** (URL del servicio de SSO de SAML e Id. de entidad de SAML) de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_06.png) 

7. En otra ventana del explorador web, inicie sesión como administrador en el inquilino de Adobe Creative Cloud.

8.  Vaya a **Identity** (Identidad) en el panel de navegación izquierdo y haga clic en su dominio. Realice los pasos siguientes en la sección **Single Sign On Configuration Required** (Configuración de inicio de sesión único necesaria).

    ![Configuración](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_001.png "Configuración")

9. Haga clic en **Browse** (Examinar) para cargar el certificado descargado de Azure AD en **IDP Certificate** (Certificado IDP).

10. En el cuadro de texto **IDP issuer** (Emisor de IDP), pegue el **Id. de entidad de SAML** que copió de la sección **Configurar inicio de sesión** de Azure Portal.

11. En el cuadro de texto **IDP Login URL** (URL de inicio de sesión IDP), pegue la **URL del servicio de SSO de SAML** que copió de la sección **Configurar inicio de sesión** de Azure Portal.

12. Seleccione **HTTP - Redirect** (HTTP - Redireccionamiento) como **IDP Binding** (Enlace IDP).

13. Seleccione **Email Address** (Dirección de correo electrónico) como **User Login Setting** (Configuración de inicio de sesión de usuario).
 
14. Haga clic en el botón **Guardar** .

15. El panel presentará ahora el archivo XML **"Download Metadata"** (Descargar metadatos). Contiene la URL de EntityDescriptor y la URL de AssertionConsumerService de Adobe. Abra el archivo y configúrelas en la aplicación Azure AD.

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_002.png)

    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Use el valor de EntityDescriptor que le proporciona Adobe como **Identificador** en el cuadro de diálogo **Configurar las opciones de la aplicación**.

    b. Use el valor de AssertionConsumerService que le proporciona Adobe como **URL de respuesta** en el cuadro de diálogo **Configurar las opciones de la aplicación**.
 
### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**. 

### <a name="creating-an-adobe-creative-cloud-test-user"></a>Creación de un usuario de prueba de Adobe Creative Cloud

Para permitir que los usuarios de Azure AD inicien sesión en Adobe Creative Cloud, deben aprovisionarse en Adobe Creative Cloud.  
En el caso de Adobe Creative Cloud, el aprovisionamiento es una tarea manual.

**Para aprovisionar cuentas de usuario, realice estos pasos:**

1. Inicie sesión como administrador en su sitio de la compañía de Adobe Creative Cloud.

2. Haga clic en **Contactos**.

    ![Personas](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_001.png "Personas")

3. Haga clic en **Invitar a usuario**.

    ![Invitación de usuarios](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_002.png "Invitación de usuarios")

4. En la página de diálogo **Invitar a personas**, realice los siguientes pasos:

    ![Invitar a personas](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_003.png "Invitar a personas")

    a. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de la cuenta de Britta Simon.
    
    b. Haga clic en **Invitar**.

    > [!NOTE]
    > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta antes de que se active.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, se habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Adobe Creative Cloud.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Adobe Creative Cloud, siga estos pasos:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. A continuación, haga clic en **All applications** (Todas las aplicaciones).

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Adobe Creative Cloud**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_50.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Adobe Creative Cloud del panel de acceso, debería iniciar sesión automáticamente en la aplicación Adobe Creative Cloud.


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
