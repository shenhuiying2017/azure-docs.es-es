---
title: "Tutorial: Integración de Azure Active Directory con Questetra BPM Suite | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Questetra BPM Suite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: e2515d571aa1615f14b597ef82b1087df3827f82
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Tutorial: Integración de Azure Active Directory con Questetra BPM Suite

En este tutorial, aprenderá a integrar Questetra BPM Suite con Azure Active Directory (Azure AD).

La integración de Questetra BPM Suite con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Questetra BPM Suite
- Puede permitir que los usuarios inicien sesión automáticamente en Questetra BPM Suite (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Questetra BPM Suite, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Questetra BPM Suite

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Questetra BPM Suite desde la galería
2. Configuración y prueba del inicio de sesión único en Azure AD

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>Incorporación de Questetra BPM Suite desde la galería
Para configurar la integración de Questetra BPM Suite en Azure AD, deberá agregar Questetra BPM Suite desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Questetra BPM Suite desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Questetra BPM Suite**, seleccione **Questetra BPM Suite** en el panel de resultados y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Incorporación desde la galería](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Questetra BPM Suite con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Questetra BPM Suite para un usuario de Azure AD. Es decir, se requiere establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Questetra BPM Suite.

Para establecer la relación de vínculo, en Questetra BPM Suite, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Questetra BPM Suite, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Questetra BPM Suite](#create-a-questetra-bpm-suite-test-user)**: para tener un homólogo de Britta Simon en Questetra BPM Suite vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Questetra BPM Suite.

**Para configurar el inicio de sesión único de Azure AD con Questetra BPM Suite, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Questetra BPM Suite**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Inicio de sesión basado en SAML](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

3. En la sección **Questetra BPM Suite Domain and URLs** (Dominio y direcciones URL de Questetra BPM Suite), lleve a cabo los pasos siguientes:

    ![Sección Questetra BPM Suite Domain and URLs (Dominio y direcciones URL de Questetra BPM Suite)](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Puede obtener estos valores de la sección **SP Information** (Información de soporte técnico) del sitio de **Questetra BPM Suite** de la empresa, que se explica más adelante en el tutorial, o al ponerse en contacto con el [equipo de atención al cliente de Questetra BPM Suite](https://www.questetra.com/contact/). 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Sección Certificado de firma SAML](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_400.png)

6. En la sección **Questetra BPM Suite Configuration** (Configuración de Questetra BPM Suite), haga clic en **Configure Questetra BPM Suite** (Configurar Questetra BPM Suite) para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Sección Questetra BPM Suite Configuration (Configuración de Questetra BPM Suite)](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

7. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de **Questetra BPM Suite** como administrador.

8. En el menú de la parte superior, haga clic en **Configuración del sistema**. 
   
    ![Inicio de sesión único de Azure AD ][10]

9. Para abrir la página **SingleSignOnSAML**, haga clic en **SSO (SAML)**. 
   
    ![Inicio de sesión único de Azure AD ][11]

10. En el sitio **Questetra BPM Suite** de la empresa, en la sección **SP información** (Información de soporte técnico), lleve a cabo los pasos siguientes:

    a. Copie el valor de **ACS URL** (URL de ACS) y péguelo en el cuadro de texto **URL de inicio de sesión** de la sección **Questetra BPM Suite Domain and URLs** (Dominios y direcciones URL de Questetra BPM Suite) de Azure Portal.
    
    b. Copie el valor de **Entity ID** (Id. de identidad) y péguelo en el cuadro de texto **Identificador** de la sección **Questetra BPM Suite Domain and URLs** (Dominios y direcciones URL de Questetra BPM Suite) de Azure Portal.

11. En el sitio **Questetra BPM Suite** de la empresa, realice los pasos siguientes: 
   
    ![Configurar inicio de sesión único][15]
   
    a. Seleccione **Enable Single Sign-On**(Habilitar inicio de sesión único).
   
    b. En el cuadro de texto **Entity ID** (Id. de entidad), pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que copió de Azure Portal.
    
    c. Copie el valor de **SAML Single Sign-On Service URL** (Dirección URL de inicio de sesión único de SAML) que ha copiado de Azure Portal en el cuadro de texto **Sign-in page URL** (Dirección URL de la página de inicio de sesión).
    
    d. En el cuadro de texto **Sign-out page URL** (Dirección URL de la página de cierre de sesión), pegue el valor de **Dirección URL de cierre de sesión** que copió de Azure Portal.
    
    e. En el cuadro de texto**NameID format** (Formato de id. de nombre), escriba `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Abra el certificado codificado en **base 64** descargado de Azure Portal en el bloc de notas, copie su contenido en el portapapeles y péguelo en el cuadro de texto **Certificado de verificación**. 

    g. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Creación de un usuario de prueba de Questetra BPM Suite

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Questetra BPM Suite.

**Para crear un usuario llamado Britta Simon en Questetra BPM Suite, realice los pasos siguientes:**

1. Inicie sesión en el sitio Questetra BPM Suite de la empresa como administrador.
2. Vaya a **System Settings > User List > New User** (Configuración del sistema > Lista de usuarios > Nuevo usuario). 
3. En el cuadro de diálogo Nuevo usuario, realice los pasos siguientes: 
   
    ![Creación de un usuario de prueba][300] 
   
    a. En el cuadro de texto **Name** (Nombre), escriba el **nombre** del usuario: **britta.simon@contoso.com**.
   
    b. En el cuadro de texto **Email** (Correo electrónico), escriba el **correo electrónico del usuario**: **britta.simon@contoso.com**
   
    c. En el cuadro de texto **Password** (Contraseña), escriba una **contraseña** para el usuario.
    
    d. Haga clic en **Add new user**(Agregar nuevo usuario).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure al concederle acceso a Questetra BPM Suite.

![Asignar usuario][200] 

**Para asignar Britta Simon a Questetra BPM Suite, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Questetra BPM Suite**.

    ![Questetra BPM Suite en la lista de aplicaciones](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono Questetra BPM Suite en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de Questetra BPM Suite.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

