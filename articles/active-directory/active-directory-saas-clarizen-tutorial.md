---
title: "Tutorial: integración de Azure Active Directory con Clarizen | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Clarizen."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: 2925f0a9f582d0dfeca9832ca032b0d847f23f6b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Tutorial: integración de Azure Active Directory con Clarizen

En este tutorial, aprenderá a integrar Azure Active Directory (Azure AD) con Clarizen. Esta integración ofrece las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Clarizen.
- Puede permitir que los usuarios inicien sesión automáticamente en Clarizen (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

El escenario de este tutorial consta de dos tareas principales:

1. Incorporación de Clarizen desde la galería.
2. Configuración y prueba del inicio de sesión único en Azure AD.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con Clarizen, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción de Clarizen con inicio de sesión único habilitado

Para probar los pasos de este tutorial, siga estas recomendaciones:

- Pruebe el inicio de sesión único de Azure AD en un entorno de prueba. No use su entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-clarizen-from-the-gallery"></a>Incorporación de Clarizen desde la galería
Para configurar la integración de Clarizen en Azure AD, debe agregar Clarizen desde la galería a la lista de aplicaciones SaaS administradas.

1. En el panel izquierdo de [Azure Portal](https://portal.azure.com), haga clic en el icono de **Azure Active Directory**.

    ![Icono de Azure Active Directory][1]

2. Haga clic en **Aplicaciones empresariales**. A continuación, haga clic en **Todas las aplicaciones**.

    ![Clic en "Aplicaciones empresariales" y "Todas las aplicaciones"][2]

3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Botón "Agregar"][3]

4. En el cuadro de búsqueda, escriba **Clarizen**.

    ![Escribir "Clarizen" en el cuadro de búsqueda](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_000.png)

5. En el panel de resultados, seleccione **Clarizen** y luego haga clic en **Agregar** para agregar la aplicación.

    ![Selección de Clarizen en el panel de resultados](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En las secciones siguientes, va a configurar y probar el inicio de sesión único de Azure AD con Clarizen con el usuario de prueba Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Clarizen para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Clarizen. Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Clarizen.

Para configurar y probar el inicio de sesión único de Azure AD con Clarizen, complete los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**, para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Clarizen](#create-a-clarizen-test-user)**, para tener un homólogo de Britta Simon en Clarizen que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**, para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
Habilite el inicio de sesión único de Azure AD en Azure Portal y configure el inicio de sesión único en la aplicación Clarizen.

1. En Azure Portal, en la página de integración de la aplicación **Clarizen**, haga clic en **Inicio de sesión único**.

    ![Clic en "Inicio de sesión único"][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.

    ![Selección de "Inicio de sesión basado en SAML"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_01.png)

3. En la sección **Dominio y direcciones URL de Clarizen**, lleve a cabo los pasos siguientes:

    ![Cuadros de identificador y dirección URL de respuesta](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_02.png)

    a. En el cuadro **Identificador**, escriba el valor: **Clarizen**

    b. En el cuadro **URL de respuesta**, escriba la dirección URL según este patrón: **https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx**

    > [!NOTE]
    > Estos valores no son reales. Tiene que usar el identificador y la dirección URL de respuesta reales. Aquí se recomienda que utilice el valor de cadena único en el identificador. Para obtener los valores reales, póngase en contacto con el [equipo de soporte técnico de Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

4. En la sección **Certificado de firma de SAML**, haga clic en **Crear nuevo certificado**.

    ![Clic en "Crear un nuevo certificado"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_03.png)  

5. En el cuadro de diálogo **Crear nuevo certificado**, haga clic en el icono del calendario y seleccione una fecha de expiración. A continuación, haga clic en **Guardar**.

    ![Seleccionar y guardar una fecha de expiración](./media/active-directory-saas-clarizen-tutorial/tutorial_general_300.png)

6. En la sección **Certificado de firma de SAML**, seleccione **Activar el certificado nuevo** y haga clic en el botón **Guardar**.

    ![Selección de la casilla para activar el nuevo certificado](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_04.png)

7. En el cuadro de diálogo **Certificado de sustitución**, haga clic en **Aceptar**.

    ![Clic en "Aceptar" para confirmar que desea activar el certificado](./media/active-directory-saas-clarizen-tutorial/tutorial_general_400.png)

8. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Clic en "Certificado (Base64)" para iniciar la descarga](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_05.png)

9. En la sección **Configuración de Clarizen**, haga clic en **Configurar Clarizen** para abrir la ventana **Configurar inicio de sesión**.

    ![Clic en "Configurar Clarizen"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_06.png)

    ![Ventana "Configurar inicio de sesión", incluidos los archivos y las direcciones URL](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_07.png)

10. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Clarizen como administrador.

11. Haga clic en el nombre de usuario y luego haga clic en **Settings** (Configuración).

    ![Clic en "Settings" (Configuración) debajo del nombre de usuario](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_001.png "Configuración")

12. Haga clic en la pestaña **Global Settings** (Configuración global). A continuación, junto a **Federated Authentication** (Autenticación federada), haga clic en **edit** (Editar).

    ![Pestaña "Global Settings"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_002.png "Global Settings")

13. En el cuadro de diálogo **Federated Authentication** (Autenticación federada), realice los pasos siguientes:

    ![Cuadro de diálogo "Federated Authentication"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_003.png "Federated Authentication")

    a. Seleccione **Habilitar autenticación federada**.

    b. Para cargar el certificado descargado, haga clic en **Cargar** .

    c. En el cuadro **URL de inicio de sesión**, coloque el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) en la ventana de configuración de aplicaciones de Azure AD.

    d. En el cuadro **Sign-out URL** (URL de cierre de sesión), coloque el valor de **Sign-out URL** (URL de cierre de sesión) en la ventana de configuración de aplicaciones de Azure AD.

    e. Seleccione **Usar POST**.

    f. Haga clic en **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
En Azure Portal, cree un usuario de prueba denominado Britta Simon.

![Nombre y dirección de correo electrónico del usuario de prueba de Azure AD][100]

1. En el panel izquierdo de Azure Portal, haga clic en el icono de **Azure Active Directory**.

    ![Icono de Azure Active Directory](./media/active-directory-saas-clarizen-tutorial/create_aaduser_01.png)

2. Haga clic en **Usuarios y grupos** y luego en **Todos los usuarios** para mostrar la lista de usuarios.

    ![Clic en "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo, haga clic en **Agregar** para abrir el cuadro de diálogo **Usuario**.

    ![Botón "Agregar"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo "Usuario" con los campos nombre, dirección de correo electrónico y contraseña cumplimentados](./media/active-directory-saas-clarizen-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico de la cuenta de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).

### <a name="create-a-clarizen-test-user"></a>Creación de un usuario de prueba de Clarizen
Para permitir que los usuarios de Azure AD inicien sesión en Clarizen, debe aprovisionar las cuentas de usuario. En el caso de Clarizen, el aprovisionamiento es una tarea manual.

1. Inicie sesión en el sitio de la compañía de Clarizen como administrador.

2. Haga clic en **Contactos**.

    ![Clic en "People"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_001.png "People")

3. Haga clic en **Invitar a usuario**.

    ![Botón "Invite User"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_002.png "Invite Users")

4. En el cuadro de diálogo **Invite People** (Invitar a personas), siga estos pasos:

    ![Cuadro de diálogo "Invite People"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_003.png "Invite People")

    a. En el cuadro **Email** (Correo electrónico), escriba la dirección de correo electrónico de la cuenta de Britta Simon.

    b. Haga clic en **Invitar**.

    > [!NOTE]
    > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta antes de que se active.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD
Habilite a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Clarizen.

![Usuario de prueba asignado][200]

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y haga clic en **Aplicaciones empresariales**. Después, haga clic en **Todas las aplicaciones**.

    ![Clic en "Aplicaciones empresariales" y "Todas las aplicaciones"][201]

2. En la lista de aplicaciones, seleccione **Clarizen**.

    ![Selección de Clarizen en la lista](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_50.png)

3. En el panel izquierdo, haga clic en **Usuarios y grupos**.

    ![Clic en "Usuarios y grupos"][202]

4. Haga clic en el botón **Add** (Agregar). Después, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Botón "Agregar" y cuadro de diálogo "Agregar asignación"][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. En el cuadro de diálogo **Usuarios y grupos**, haga clic en el botón **Seleccionar**.

7. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único
Pruebe la configuración de inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Clarizen en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Clarizen.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_203.png
