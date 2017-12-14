---
title: "Tutorial: integración de Azure Active Directory con ThirdLight | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y ThirdLight."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 559350d5cff6cfc454446b64f8abd580eb42748b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Tutorial: integración de Azure Active Directory con ThirdLight

En este tutorial, aprenderá a integrar ThirdLight con Azure Active Directory (Azure AD).

La integración de ThirdLight con Azure AD proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a ThirdLight
- Puede permitir que los usuarios inicien sesión automáticamente en ThirdLight (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con ThirdLight, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en ThirdLight

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de ThirdLight desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-thirdlight-from-the-gallery"></a>Incorporación de ThirdLight desde la galería
Para configurar la integración de ThirdLight en Azure AD, es preciso agregar ThirdLight desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar ThirdLight desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **ThirdLight**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_search.png)

5. En el panel de resultados, seleccione **ThirdLight** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con ThirdLight con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de ThirdLight para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario asociado de ThirdLight.

Para establecer esta relación de vínculo, se asigna el valor del **nombre de usuario** en Azure AD como valor de **Nombre de usuario** en ThirdLight.

Para configurar y probar el inicio de sesión único de Azure AD con ThirdLight, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de ThirdLight](#creating-a-thirdlight-test-user)**: para tener un homólogo de Britta Simon en ThirdLight vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación ThirdLight.

**Para configurar el inicio de sesión único de Azure AD con ThirdLight, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **ThirdLight**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_samlbase.png)

3. En la sección de **ThirdLight Domain and URLs** (Dominio y direcciones URL de ThirdLight), lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.thirdlight.com/`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.thirdlight.com/saml/sp`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de ThirdLight](https://www.thirdlight.com/support) para obtener estos valores. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo XML en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-thirdlight-tutorial/tutorial_general_400.png)

6. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de ThirdLight como administrador.

7. Vaya a **Configuración \> Administración del sistema** y luego haga clic en **SAML2**.
   
    ![Administración del sistema](./media/active-directory-saas-thirdlight-tutorial/ic805843.png "Administración del sistema")

8. En la sección de configuración de SAML2, realice los pasos siguientes:
   
    ![Inicio de sesión único SAML](./media/active-directory-saas-thirdlight-tutorial/ic805844.png "Inicio de sesión único SAML")   

     a. Seleccione **Habilitar inicio de sesión único de SAML2**.
 
     b. Como **Origen para los metadatos de IdP**, seleccione **Cargar metadatos de IdP desde XML**.
 
     c. Abra el archivo de metadatos descargado, copie el contenido y luego péguelo en el cuadro de texto **XML de metadatos de IdP** . 
     
     d. Haga clic en **Guardar configuración de SAML2**.

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-thirdlight-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-thirdlight-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-thirdlight-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-thirdlight-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-thirdlight-test-user"></a>Creación de un usuario de prueba ThirdLight

Para permitir que los usuarios de Azure AD inicien sesión en ThirdLight, deben aprovisionarse en ThirdLight.  
En el caso de ThirdLight, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su sitio de la compañía de **ThirdLight** como administrador.

2. Vaya a la pestaña **Usuarios** .

3. Seleccione **Usuarios y grupos**.

4. Haga clic en el botón **Agregar nuevo usuario** .

5. Escriba **el nombre de usuario, el nombre o la descripción, el correo electrónico, elegir un valor preestablecido o grupo de nuevos miembros** de una cuenta de AAD válida que quiera suministrar.

6. Haga clic en **Crear**.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Thirdlight ofrecida por Thirdlight para aprovisionar cuentas de usuario de AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a ThirdLight.

![Asignar usuario][200] 

**Para asignar Britta Simon a ThirdLight, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **ThirdLight**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-thirdlight-tutorial/tutorial_thirdlight_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de ThirdLight en el panel de acceso, iniciará sesión automáticamente en la aplicación ThirdLight.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-thirdlight-tutorial/tutorial_general_203.png

