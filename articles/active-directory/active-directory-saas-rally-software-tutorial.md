---
title: "Tutorial: Integración de Azure Active Directory con Rally Software | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Rally Software."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: jeedes
ms.openlocfilehash: 9e3b5ad4487ff1309923a1b0ffac9589084e715b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Tutorial: Integración de Azure Active Directory con Rally Software

En este tutorial, obtendrá información sobre cómo integrar Rally Software con Azure Active Directory (Azure AD).

La integración de Rally Software con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a Rally Software.
- Puede permitir que los usuarios inicien sesión automáticamente en Rally Software (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Rally Software, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Rally Software

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Rally Software desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-rally-software-from-the-gallery"></a>Incorporación de Rally Software desde la galería
Para configurar la integración de Rally Software en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Rally Software desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Rally Software**, seleccione **Rally Software** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Rally Software en la lista de resultados](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Rally Software con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, es preciso que Azure AD sepa cuál es el usuario homólogo en Rally Software de un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Rally Software.

En Rally Software, asigne el valor del **nombre de usuario** en Azure AD como valor de **nombre de usuario** para establecer la relación de vínculo.

Para configurar y probar el inicio de sesión único de Azure AD con Rally Software, debe completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Rally Software](#create-a-rally-software-test-user)**: para tener un homólogo de Britta Simon en Rally Software que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Rally Software.

**Para configurar el inicio de sesión único de Azure AD con Rally Software, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Rally Software**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_samlbase.png)

3. En la sección **Dominio y direcciones URL de Rally Software**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Rally Software](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.rally.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.rally.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de Rally Software](https://help.rallydev.com/) para obtener estos valores. 
 


4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Guardar de Configuración de inicio de sesión único](./media/active-directory-saas-rally-software-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Rally Software**, haga clic en **Configurar Rally Software** para abrir la ventana **Configurar inicio de sesión**. Copie los valores de **Sign-Out URL (Dirección URL de cierre de sesión) y SAML Entity ID (Identificador de entidad de SAML)** de la sección **Referencia rápida**.

    ![Configuración de Rally Software](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_configure.png) 

7. Inicie sesión en su inquilino de **Rally Software** .

8. En la barra de herramientas de la parte superior, haga clic en **Configuración** y seleccione **Suscripción**.
   
    ![Suscripción](./media/active-directory-saas-rally-software-tutorial/ic769531.png "Suscripción")

9. Haga clic en el botón **Acción**. Seleccione **Editar suscripción** en la parte superior derecha de la barra de herramientas.

10. En la página de diálogo **Suscripción**, realice los pasos siguientes y haga clic en **Guardar y cerrar**:
   
    ![Autenticación](./media/active-directory-saas-rally-software-tutorial/ic769542.png "Autenticación")
   
    a. Seleccione **Rally o autenticación de SSO** en la lista desplegable de autenticación.

    b. En el cuadro de texto **Identity provider URL** (Dirección URL del proveedor de identidad), pegue el valor del **identificador de entidad de SAML** que ha copiado de Azure Portal. 

    c. En el cuadro de texto **SSO Logout** (Dirección URL de inicio de sesión), pegue el valor de **dirección URL de cierre de sesión** que copió de Azure Portal.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/active-directory-saas-rally-software-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/active-directory-saas-rally-software-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/active-directory-saas-rally-software-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/active-directory-saas-rally-software-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-rally-software-test-user"></a>Creación de un usuario de prueba de Rally Software

Para que los usuarios de Azure AD puedan inician sesión, deben aprovisionarse para la aplicación Rally Software con sus nombres de usuario de Azure Active Directory.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. Inicie sesión en su inquilino de Rally Software.

2. Vaya a **Configuración \> USUARIOS** y haga clic en **+ Agregar nuevo**.
   
    ![Usuarios](./media/active-directory-saas-rally-software-tutorial/ic781039.png "Usuarios")

3. Escriba el nombre en el cuadro de texto Nuevo usuario y luego haga clic en **Agregar con detalles**.

4. En la sección **Crear usuario** , lleve a cabo estos pasos:
   
    ![Creación de usuarios](./media/active-directory-saas-rally-software-tutorial/ic781040.png "Creación de usuarios")

    a. En el cuadro de texto **Nombre de usuario**, escriba el nombre de un usuario, por ejemplo, **Brittsimon**.
   
    b. En el cuadro de texto **Email address** (Dirección de correo electrónico), escriba el correo electrónico del usuario, en este caso, **brittasimon@contoso.com**.

    c. En el cuadro de texto **First Name** (Nombre), escriba el nombre de usuario, en este caso **Britta**.

    d. En el cuadro de texto **Last Name** (Apellidos), escriba el nombre de usuario, en este caso **Simon**.

    e. Haga clic en **Guardar y cerrar**.

   >[!NOTE]
   >Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Rally Software ofrecida por Rally Software para aprovisionar cuentas de usuario de Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Rally Software.

![Asignación del rol de usuario][200] 

**Para asignar un usuario llamado Britta Simon a Rally Software, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Rally Software**.

    ![Vínculo a Rally Software en la lista de aplicaciones](./media/active-directory-saas-rally-software-tutorial/tutorial_rallysoftware_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Rally Software en el panel de acceso, debería iniciar sesión automáticamente en su aplicación de Rally Software.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-rally-software-tutorial/tutorial_general_203.png

