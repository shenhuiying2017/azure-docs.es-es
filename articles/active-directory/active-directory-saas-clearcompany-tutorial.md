---
title: "Tutorial: integración de Azure Active Directory con ClearCompany | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y ClearCompany."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 2819da18-c7eb-43cf-aac3-1403a540bf6e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: jeedes
ms.openlocfilehash: f17256f4302a9fd94e3f4497ed27d5e4929fc276
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-clearcompany"></a>Tutorial: integración de Azure Active Directory con ClearCompany

En este tutorial, aprenderá a integrar ClearCompany con Azure Active Directory (Azure AD).

Integrar ClearCompany con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a ClearCompany.
- Puede permitir que los usuarios inicien sesión automáticamente en ClearCompany (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con ClearCompany, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en ClearCompany

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de ClearCompany desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-clearcompany-from-the-gallery"></a>Incorporación de ClearCompany desde la galería
Para configurar la integración de ClearCompany en Azure AD, es preciso agregar ClearCompany desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar ClearCompany desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **ClearCompany**, seleccione **ClearCompany** en el panel de resultados y haga clic en el botón **Agregar** para agregar la aplicación.

    ![ClearCompany en la lista de resultados](./media/active-directory-saas-clearcompany-tutorial/tutorial_clearcompany_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y proba el inicio de sesión único de Azure AD con ClearCompany con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD tiene que saber cuál es el usuario homólogo de ClearCompany para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de ClearCompany.

Para establecer la relación de vínculo, en ClearCompany, asigne el valor de **nombre de usuario** de Azure AD como valor del **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con ClearCompany, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de ClearCompany](#create-a-clearcompany-test-user)**: para tener un homólogo de Britta Simon en ClearCompany que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación ClearCompany.

**Para configurar el inicio de sesión único de Azure AD con ClearCompany, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **ClearCompany**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-clearcompany-tutorial/tutorial_clearcompany_samlbase.png)

3. En la sección **Dominio y direcciones URL de ClearCompany**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por IDP:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de ClearCompany](./media/active-directory-saas-clearcompany-tutorial/tutorial_clearcompany_url1.png)

    En el cuadro de texto **Identificador**, escriba la dirección URL: `https://api.clearcompany.com`

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de ClearCompany](./media/active-directory-saas-clearcompany-tutorial/tutorial_clearcompany_url2.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.clearcompany.com`.
    
    > [!NOTE] 
    > El valor de dirección URL de inicio de sesión no es un valor real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de ClearCompany](http://www.clearcompany.com/support) para obtener este valor. 

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-clearcompany-tutorial/tutorial_clearcompany_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-clearcompany-tutorial/tutorial_general_400.png)
    
7. En la sección **Configuración de ClearCompany**, haga clic en **Configurar ClearCompany** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configuración de ClearCompany](./media/active-directory-saas-clearcompany-tutorial/tutorial_clearcompany_configure.png) 

8. Para configurar el inicio de sesión único en **ClearCompany**, es preciso enviar el **certificado (Base64)** descargado y la **dirección URL del servicio de inicio de sesión único de SAML** al [equipo de soporte técnico de ClearCompany](http://www.clearcompany.com/support). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-clearcompany-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-clearcompany-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-clearcompany-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-clearcompany-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-clearcompany-test-user"></a>Creación de un usuario de prueba ClearCompany

En esta sección, creará un usuario llamado Britta Simon en ClearCompany. Trabaje con el [equipo de soporte técnico de ClearCompany](http://www.clearcompany.com/support) para agregar los usuarios en la plataforma de ClearCompany. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a ClearCompany.

![Asignación del rol de usuario][200] 

**Para asignar Britta Simon a ClearCompany, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **ClearCompany**.

    ![Vínculo a ClearCompany en la lista de aplicaciones](./media/active-directory-saas-clearcompany-tutorial/tutorial_clearcompany_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de ClearCompany en el panel de acceso, debería iniciar sesión automáticamente en su aplicación ClearCompany.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-clearcompany-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clearcompany-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clearcompany-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clearcompany-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clearcompany-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clearcompany-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clearcompany-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clearcompany-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clearcompany-tutorial/tutorial_general_203.png

