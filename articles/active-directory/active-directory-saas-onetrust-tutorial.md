---
title: "Tutorial: Integración de Azure Active Directory con OneTrust Privacy Management Software | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y el software OneTrust Privacy Management."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 71c2b6d0-3d28-4130-a2c8-1e72ab3d5814
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jeedes
ms.openlocfilehash: 89d657e754f6908a3afbb615a2158c9d7f3380f9
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/16/2017
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Tutorial: Integración de Azure Active Directory con OneTrust Privacy Management Software

En este tutorial, aprenderá a integrar OneTrust Privacy Management Software con Azure Active Directory (Azure AD).

La integración de OneTrust Privacy Management Software con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a OneTrust Privacy Management Software.
- Puede permitir que los usuarios inicien sesión automáticamente en OneTrust Privacy Management Software (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con OneTrust Privacy Management Software, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en OneTrust Privacy Management Software.

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de OneTrust Privacy Management Software desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-onetrust-privacy-management-software-from-the-gallery"></a>Adición de OneTrust Privacy Management Software desde la galería
Para configurar la integración de OneTrust Privacy Management Software en Azure AD, deberá agregar OneTrust Privacy Management Software desde la galería hasta la lista de aplicaciones SaaS administradas.

**Para agregar OneTrust Privacy Management Software desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **OneTrust Privacy Management Software**, seleccione **OneTrust Privacy Management Software** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![OneTrust Privacy Management Software en la lista de resultados](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con OneTrust Privacy Management Software con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de OneTrust Privacy Management Software para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de OneTrust Privacy Management Software.

En OneTrust Privacy Management Software, asigne el valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario** para establecer la relación de vínculo.

Para configurar y probar el inicio de sesión único de Azure AD con OneTrust Privacy Management Software, debe completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de OneTrust Privacy Management Software](#create-a-onetrust-privacy-management-software-test-user)**: para tener un homólogo de Britta Simon en OneTrust Privacy Management Software que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación OneTrust Privacy Management Software.

**Para configurar el inicio de sesión único de Azure AD con OneTrust Privacy Management Software, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **OneTrust Privacy Management Software**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_samlbase.png)

3. En la sección **Dominio y direcciones URL de OneTrust Privacy Management Software**, si quiere configurar la aplicación en modo iniciado por **IDP**, realice los siguientes pasos:

    ![Información de inicio de sesión único de dominio y direcciones URL de OneTrust Privacy Management Software](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL como: `https://www.onetrust.com/saml2`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.onetrust.com/auth/consumerservice`.

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de inicio de sesión único de dominio y direcciones URL de OneTrust Privacy Management Software](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.onetrust.com/auth/login`.
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Para obtener estos valores, póngase en contacto con el [equipo de atención al cliente de Trust Privacy Management Software](mailto:support@onetrust.com). 

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/active-directory-saas-onetrust-tutorial/tutorial_general_400.png)

7. Para configurar el inicio de sesión único en **OneTrust Privacy Management Software**, debe enviar el archivo **XML de metadatos** descargado al [equipo de atención al cliente de OneTrust Privacy Management Software](mailto:support@onetrust.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-onetrust-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-onetrust-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-onetrust-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-onetrust-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-onetrust-privacy-management-software-test-user"></a>Creación de un usuario de prueba de OneTrust Privacy Management Software

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en OneTrust Privacy Management Software. OneTrust Privacy Management Software admite aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Se crea un nuevo usuario durante un intento de acceder a OneTrust Privacy Management Software si aún no existe.

>[!Note]
>Si tiene que crear manualmente un usuario, póngase en contacto con el [equipo de atención al cliente de OneTrust Privacy Management Software](mailto:support@onetrust.com).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure, para lo que se le concederá acceso a OneTrust Privacy Management Software.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a OneTrust Privacy Management Software, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **OneTrust Privacy Management Software**.

    ![El vínculo de OneTrust Privacy Management Software en la lista de aplicaciones](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de OneTrust Privacy Management Software en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación OneTrust Privacy Management Software.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_203.png

