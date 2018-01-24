---
title: "Tutorial: Integración de Azure Active Directory con Cisco Webex | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Cisco Webex."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory7
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 42632dcf8997ec5e987ac8a6615aae24e903399a
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Tutorial: Integración de Azure Active Directory con Cisco Webex

En este tutorial, aprenderá a integrar Cisco Webex con Azure Active Directory (Azure AD).

Integrar Cisco Webex con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Cisco Webex.
- Puede permitir que los usuarios inicien sesión automáticamente en Cisco Webex con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Para obtener más detalles sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Cisco Webex, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Cisco Webex

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, siga estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba gratuita durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Cisco Webex desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="add-cisco-webex-from-the-gallery"></a>Adición de Cisco Webex desde la galería
Para configurar la integración de Cisco Webex en Azure AD, será preciso que agregue Cisco Webex desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Cisco Webex desde la galería, realice los pasos siguientes:**

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), seleccione el icono **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, seleccione el botón **Nueva aplicación** en la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Cisco Webex**. 

5. Seleccione **Cisco Webex** en el panel de resultados. A continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Cisco Webex en la lista de resultados](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, va a configurar y probar el inicio de sesión único de Azure AD con Cisco Webex con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Cisco Webex para un usuario de Azure AD. Es decir, es necesario establecer un vínculo entre un usuario de Azure AD y el usuario relacionado de Cisco Webex.

En Cisco Webex, asigne a **Nombre de usuario** el mismo valor que definió en **nombre de usuario** en Azure AD. Ahora ya ha establecido el vínculo entre los dos usuarios. 

Para configurar y probar el inicio de sesión único de Azure AD con Cisco Webex, complete los siguientes bloques de creación:

1. [Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on), para permitir que los usuarios utilicen esta característica.
2. [Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user), para probar el inicio de sesión único de Azure AD con Britta Simon.
3. [Creación de un usuario de prueba de Cisco Webex](#create-a-cisco-webex-test-user), para tener un homólogo de Britta Simon en Cisco Webex que esté vinculado a la representación de ella en Azure AD.
4. [Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user), para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. [Prueba del inicio de sesión único](#test-single-sign-on) para comprobar que la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Cisco Webex.

**Para configurar el inicio de sesión único de Azure AD con Cisco Webex, siga estos pasos:**

1. En la página de integración de la aplicación **Cisco Webex** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en la lista desplegable **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. En la sección **Dominio y direcciones URL de Cisco Webex**, lleve a cabo los pasos siguientes:

    ![Información de inicio de sesión único de dominio y direcciones URL de Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. En el cuadro **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.webex.com`

    b. En el cuadro **Identificador**, escriba la dirección URL `http://www.webex.com`.

    c. En el cuadro **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico para clientes de Cisco Webex](https://www.webex.co.in/support/support-overview.html) para obtener estos valores. 

5. En la sección **Certificado de firma de SAML**, seleccione **XML de metadatos** y, a continuación, guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. Seleccione **Guardar**.

    ![Configuración del botón Guardar del inicio de sesión único](./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_400.png)
    
6. En la sección **Configuración de Cisco Webex**, seleccione **Configurar Cisco Webex** para abrir la ventana **Configurar inicio de sesión**. Copie la **Dirección URL de cierre de sesión**, el **Identificador de entidad de SAML** y la **Dirección URL del servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía Cisco Webex.

8. En el menú de la parte superior, haga clic en **Administración de sitios**.

    ![Administración de sitios](./media/active-directory-saas-cisco-webex-tutorial/ic777621.png "Administración de sitios")

9. En la sección **Administrar sitio**, haga clic en **Configuración de SSO**.
   
    ![Configuración de SSO](./media/active-directory-saas-cisco-webex-tutorial/ic777622.png "Configuración de SSO")

10. En la sección **Federated Web SSO Configuration** (Configuración de SSO web federado), realice los pasos siguientes:
   
    ![Configuración de SSO federado](./media/active-directory-saas-cisco-webex-tutorial/ic777623.png "Configuración de SSO federado")  

    a. En la lista de **protocolos de federación**, seleccione **SAML 2.0**.

    b. En **SSO profile** (Perfil de SSO), seleccione **SP Initiated** (Iniciado por el proveedor de servicios).

    c. Abra el certificado descargado en el Bloc de notas y, a continuación, copie el contenido.

    d. Seleccione **Import SAML Metadata** (Importar metadatos de SAML) y, a continuación, pegue el contenido copiado del certificado.

    e. En el cuadro de texto **Issuer for SAML (IdP ID)** (Emisor de SAML [id. de IdP]), pegue el valor de **SAML Entity ID** (Identificador de entidad SAML) que ha copiado de Azure Portal.

    f. Pegue la **dirección URL del inicio de sesión único de SAML**, que copió de Azure Portal, en el cuadro de texto **Customer SSO Service Login URL** (Dirección URL de inicio de sesión del servicio SSO para clientes).

    g. En la lista **NameID Format** (Formato de NameID), seleccione **Email address** (Dirección de correo electrónico).

    h. En el cuadro **AuthnContextClassRef**, escriba **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**.

    i. En el cuadro **Customer SSO Service Logout URL** (URL de cierre de sesión del servicio SSO para clientes), pegue la **dirección URL de cierre de sesión** que copió de Azure Portal.
   
    j. Seleccione **Actualizar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación. Después de agregar esta aplicación desde la sección **Active Directory** > **Aplicaciones empresariales**, seleccione la pestaña **Inicio de sesión único** y, a continuación, acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada en la [documentación insertada de Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, seleccione el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, seleccione **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, seleccione **Agregar** para abrir el cuadro de diálogo **Usuario**.

    ![Botón Agregar](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario**, realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Seleccione **Crear**.
 
### <a name="create-a-cisco-webex-test-user"></a>Creación de un usuario de prueba de Cisco Webex

Para permitir que los usuarios de Azure AD inicien sesión en Cisco Webex, tienen que aprovisionarse en Cisco Webex. En el caso de Cisco Webex, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino de **Cisco Webex**.

2. Vaya a **Administrar usuarios** > **Agregar usuario**.
   
    ![Agregar usuarios](./media/active-directory-saas-cisco-webex-tutorial/ic777625.png "Agregar usuarios")

3. En la sección **Agregar usuario**, lleve a cabo estos pasos:
   
    ![Agregar usuario](./media/active-directory-saas-cisco-webex-tutorial/ic777626.png "Agregar usuario")   

    a. En **Tipo de cuenta** seleccione **Host**.

    b. En el cuadro de texto **Nombre**, escriba el nombre del usuario (en este caso, **Britta**).

    c. En el cuadro de texto **Apellidos**, escriba el apellido del usuario (en este caso, **Simon**).

    d. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario (en este caso, **Brittasimon@contoso.com**).

    e. En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico del usuario (en este caso, **Brittasimon@contoso.com**).

    f. En el cuadro de texto **Contraseña** , escriba la contraseña del usuario.

    g. En el cuadro de texto **Confirmar contraseña**, vuelva a escribir la contraseña del usuario.

    h. Seleccione **Agregar**.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario que ofrece Cisco Webex para aprovisionar cuentas de usuario de Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, para permitir a Britta Simon usar el inicio de sesión único de Azure, le concederá acceso a Cisco Webex.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Cisco Webex, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones. Vaya a la vista de directorio y, a continuación, a **Aplicaciones empresariales**.  

2. Seleccione **Todas las aplicaciones**.

    ![Asignar usuario][201] 

3. En la lista de aplicaciones, seleccione **Cisco Webex**.

    ![Vínculo a Cisco Webex en la lista de aplicaciones](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. En el menú de la izquierda, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Seleccione el botón **Agregar**. A continuación, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista **Usuarios**.

6. En el cuadro de diálogo **Usuarios y grupos**, haga clic en el botón **Seleccionar**.

7. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el panel de acceso.

Al seleccionar el icono de Cisco Webex en el panel de acceso, iniciará sesión automáticamente en la aplicación Cisco Webex.

Para más información sobre el panel de acceso, consulte [Introducción al panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_203.png

