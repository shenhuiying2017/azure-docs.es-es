---
title: 'Tutorial: integrar Azure Active Directory con Envi MMIS | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Envi MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 0d338f7f56e668c25378403e88a6532b8b8573c5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344276"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Tutorial: integrar Azure Active Directory con Envi MMIS

En este tutorial, obtendrá información sobre cómo integrar Envi MMIS en Azure Active Directory (Azure AD).

La integración de Envi MMIS con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Envi MMIS.
- Puede permitir que los usuarios inicien sesión automáticamente en Envi MMIS (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Envi MMIS, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Envi MMIS

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Agregar Envi MMIS desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-envi-mmis-from-the-gallery"></a>Agregar Envi MMIS desde la galería
Para configurar la integración de Envi MMIS en Azure AD, será preciso que agregue Envi MMIS desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Envi MMIS desde la galería, siga los pasos a continuación:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Envi MMIS**, seleccione **Envi MMIS** en el panel de resultados y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Envi MMIS en la lista de resultados](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, se configura y se prueba el inicio de sesión único de Azure AD con Envi MMIS con una usuaria de prueba llamada "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Envi MMIS para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Envi MMIS.

Para configurar y probar el inicio de sesión único de Azure AD con Envi MMIS, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Envi MMIS](#create-an-envi-mmis-test-user)**: para tener un homólogo de Britta Simon en Envi MMIS que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación de Envi MMIS.

**Para configurar el inicio de sesión único de Azure AD con Envi MMIS, siga los pasos a continuación:**

1. En Azure Portal, en la página de integración de la aplicación **Envi MMIS**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_samlbase.png)

3. En la sección **Dominio y direcciones URL de Envi MMIS**, siga los pasos a continuación si quiere configurar la aplicación en el modo que inició **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Envi MMIS](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`.

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Envi MMIS](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://www.<CUSTOMER DOMAIN>.com/Account`.
     
    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de cliente de Envi MMIS](mailto:support@ioscorp.com) para obtener estos valores.

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-envimmis-tutorial/tutorial_general_400.png)

7. En otra ventana del explorador web, inicie sesión como administrador en el sitio de Envi MMIS.

8. Haga clic en la pestaña **My Domain** (Mi dominio).

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-envimmis-tutorial/configure1.png)

9. Haga clic en **Editar**.

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-envimmis-tutorial/configure2.png)

10. Seleccione la casilla **Use remote authentication** (Usar autenticación remota) y, a continuación, seleccione **Redirección HTTP** (Redirección HTTP) desde la lista desplegable **Tipo de autenticación**.

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-envimmis-tutorial/configure3.png)

11. Seleccione la pestaña **Recursos** y, a continuación, haga clic en **Cargar metadatos**.

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-envimmis-tutorial/configure4.png)

12. En la ventana emergente **Cargar metadatos**, realice los siguientes pasos:

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-envimmis-tutorial/configure5.png)

    a. Seleccione la opción **Archivo** desde la lista desplegable **Upload From** (Cargar desde).

    b. Para cargar el archivo de metadatos descargado de Azure Portal, seleccione el **icono para elegir archivo**.

    c. Haga clic en **Aceptar**.

13. Después de cargar el archivo de metadatos descargado, los campos se rellenarán automáticamente. Haga clic en **Update** (Actualizar).

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-envimmis-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-envimmis-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-envimmis-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-envimmis-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-an-envi-mmis-test-user"></a>Crear un usuario de prueba de Envi MMIS

Para permitir que los usuarios de Azure AD inicien sesión en Envi MMIS, deben aprovisionarse en Envi MMIS.  
En el caso de Envi MMIS, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión como administrador en el sitio de la compañía de Envi MMIS.

2. Haga clic en la pestaña **Lista de usuarios**.

    ![Agregar empleado](./media/active-directory-saas-envimmis-tutorial/user1.png)

3. Haga clic en el botón **Agregar usuario**.

    ![Agregar empleado](./media/active-directory-saas-envimmis-tutorial/user2.png)

4. En la sección **Add User** (Agregar usuario), realice estos pasos:

    ![Agregar empleado](./media/active-directory-saas-envimmis-tutorial/user3.png)

    a. En el cuadro de texto **Nombre de usuario**, escriba el nombre del usuario de la cuenta de Britta Simon, como **brittasimon@contoso.com**.
    
    b. En el cuadro de texto **Nombre**, escriba el nombre del usuario de BrittaSimon, como **Britta**.

    c. En el cuadro de texto **Apellido**, escriba el nombre del usuario de Britta Simon, como **Simon**.

    d. Escriba el tratamiento del usuario en el cuadro de texto **Título**.
    
    e. En el cuadro de texto **Dirección de correo electrónico**, escriba la dirección de correo electrónico de la cuenta de Britta Simon, como **brittasimon@contoso.com**.

    f. En el cuadro de texto **SSO User Name** (Nombre de usuario de SSO), escriba el nombre del usuario de la cuenta de Britta Simon, como **brittasimon@contoso.com**.

    g. Haga clic en **Save**(Guardar).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure al concederle acceso a Envi MMIS.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Envi MMIS, siga los pasos a continuación:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Envi MMIS**.

    ![Vínculo de Envi MMIS en la lista de aplicaciones](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Envi MMIS en el panel de acceso, debe iniciar sesión automáticamente en su aplicación de Envi MMIS.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_203.png

