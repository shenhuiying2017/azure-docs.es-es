---
title: "Tutorial: Integración de Azure Active Directory con Dealpath | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Dealpath."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 51ace608-5a4f-48c0-9446-d9f86ad2e890
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: jeedes
ms.openlocfilehash: 268df1b91f458279b7d79d963fe1ab318d759f65
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2017
---
# <a name="tutorial-azure-active-directory-integration-with-dealpath"></a>Tutorial: Integración de Azure Active Directory con Dealpath

En este tutorial, aprenderá a integrar Dealpath con Azure Active Directory (Azure AD).

La integración de Dealpath con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Dealpath.
- Puede permitir que los usuarios inicien sesión automáticamente en Dealpath (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Dealpath, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Dealpath.

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Dealpath desde la Galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-dealpath-from-the-gallery"></a>Adición de Dealpath desde la Galería
Para configurar la integración de Dealpath en Azure AD, será preciso que agregue Dealpath desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Dealpath desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Dealpath**, seleccione **Dealpath** en el panel de resultados y, luego, haga clic en el botón **Agregar** para añadir la aplicación.

    ![Dealpath en la lista de resultados](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, va a configurar y probar el inicio de sesión único de Azure AD con Dealpath con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Dealpath para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Dealpath.

En Dealpath, para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Dealpath, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Dealpath](#create-a-dealpath-test-user)**: para tener un homólogo de Britta Simon en Dealpath que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Dealpath.

**Para configurar el inicio de sesión único de Azure AD con Dealpath, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Dealpath**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_samlbase.png)

3. En la sección **Dominio y direcciones URL de Dealpath**, realice los siguientes pasos:

    ![Información de dominio y direcciones URL de inicio de sesión único de Dealpath](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://app.dealpath.com/account/login`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://api.dealpath.com/saml/metadata/<ID>`

    > [!NOTE] 
    > El identificador no es real. Actualícelo con el identificador real. Póngase en contacto con el [equipo de atención al cliente de Dealpath](mailto:kenter@dealpath.com) para obtener el valor. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-dealpath-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Dealpath**, haga clic en **Configurar Dealpath** para abrir la ventana **Configurar inicio de sesión**. Copie **SAML Entity ID and SAML Single Sign-On Service URL** (URL del servicio de inicio de sesión único de SAML e Identificador de entidad de SAML) de la sección **Referencia rápida**.

    ![Configuración de Dealpath](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_configure.png) 

7. En otra ventana del explorador web, inicie sesión en Dealpath como administrador.

8. En la parte superior derecha, haga clic en **Admin Tools** (Herramientas de administración) y navegue a **Integrations** (Integraciones); a continuación, en la sección **SAML 2.0 Authentication** (Autenticación de SAML 2.0), haga clic en **Update Settings** (Actualizar configuración):

    ![Configuración de Dealpath](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_admin.png)

9. En la página **Set up SAML 2.0 authentication** (Configurar la autenticación de SAML 2.0), realice los pasos siguientes:

    ![Configuración de Dealpath](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_saml.png) 

    a. En el cuadro de texto **SAML SSO URL** (Dirección URL de inicio de sesión único de SAML), pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    b. En el cuadro de texto **Identity Provider Issuer** (Emisor de proveedor de identidades), pegue el valor del **identificador de entidad de SAML** que ha copiado de Azure Portal.

    c. Copie el contenido del archivo de **certificado(Base64)** descargado en el Bloc de notas y, luego, péguelo en el cuadro de texto **Public Certificate** (Certificado público).

    d. Haga clic en **Update settings** (Actualizar configuración).


> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-dealpath-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-dealpath-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-dealpath-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-dealpath-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-dealpath-test-user"></a>Creación de un usuario de prueba de Dealpath

En esta sección, creará un usuario llamado Britta Simon en Dealpath. Trabaje con [equipo de atención al cliente de Dealpath](mailto:kenter@dealpath.com) para agregar los usuarios en la plataforma de Dealpath. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Dealpath.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Dealpath, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Dealpath**.

    ![Vínculo a Dealpath en la lista de aplicaciones](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Dealpath en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Dealpath.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_203.png

