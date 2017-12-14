---
title: "Tutorial: Integración de Azure Active Directory con Origami | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Origami."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 962795cf6dab7231782523e3a850afa84b2b8d1b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Tutorial: Integración de Azure Active Directory con Origami

En este tutorial, obtendrá información sobre cómo integrar Origami con Azure Active Directory (Azure AD).

Integrar Origami con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Origami
- Puede permitir que los usuarios inicien sesión automáticamente en Origami (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Origami, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Pantheon

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Origami desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-origami-from-the-gallery"></a>Incorporación de Origami desde la galería
Para configurar la integración de Origami en Azure AD, deberá agregar Origami desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Origami desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Origami**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_search.png)

5. En el panel de resultados, seleccione **Pantheon** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-origami-tutorial/tutorial_origami_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Origami con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Origami para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Origami.

Para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario** de Pantheon.

Para configurar y probar el inicio de sesión único de Azure AD con Origami, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Origami](#creating-an-origami-test-user)**: el objetivo es tener un homólogo de Britta Simon en Origami que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Origami.

**Para configurar el inicio de sesión único de Azure AD con Origami, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Origami** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_samlbase.png)

3. En la sección **Dominio y direcciones URL de Origami** , lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://live.origamirisk.com/origami/account/login?account=<companyname>`.

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de atención al cliente de Origami ](https://wordpress.org/support/theme/origami) para obtener este valor. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Origami** , haga clic en **Configurar Origami**  para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de cierre de sesión y la dirección URL del servicio de inicio de sesión único de SAML** de la **sección de referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_configure.png) 

7. Inicie sesión en la cuenta de Origami con derechos de administrador.

8. En el menú de la parte superior, haga clic en **Administrador**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)

9. En la página del cuadro de diálogo Single Sign On Setup (Configuración de inicio de sesión único), siga estos pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_531.png)

    a. Seleccione **Enable Single Sign On**(Habilitar el inicio de sesión único).

    b. En el cuadro de texto **Identity Provider's Sign-in Page URL** (Dirección URL de la página de inicio de sesión del proveedor de identidades), pegue el valor de **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    c. En el cuadro de texto **Identity Provider's Sign-out Page URL** (Dirección URL de la página de inicio de sesión del proveedor de identidades, pegue el valor de **URL de cierre de sesión**, que ha copiado de Azure Portal.

    d. Haga clic en **Browse** (Examinar) para cargar el certificado que ha descargado de Azure Portal.

    e. Haga clic en **Guardar cambios**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-origami-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-an-origami-test-user"></a>Creación de un usuario de prueba de Origami

En esta sección, creará un usuario llamado Britta Simon en Origami. 

1. Inicie sesión en la cuenta de Origami con derechos de administrador.

2. En el menú de la parte superior, haga clic en **Administrador**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)

3. En el cuadro de diálogo **Users and Security** (Usuarios y seguridad), haga clic en **Users** (Usuarios).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_54.png)

4. Haga clic en **Add New User**(Agregar nuevo usuario).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_55.png)

5. En el cuadro de diálogo Add New User (Agregar nuevo usuario), realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_56.png)

    a. En el cuadro de texto **Correo electrónico**, escriba el correo electrónico del usuario, en este caso **brittasimon@contoso.com**.

    b. En el cuadro de texto **Password** (Contraseña), escriba una contraseña.

    c. En el cuadro de texto **Confirm Password** (Confirmar contraseña), escriba la contraseña de nuevo.

    d. En el cuadro de texto **First Name** (Nombre), escriba el nombre de usuario, en este caso **Britta**.

    e. En el cuadro de texto **Last Name** (Apellidos), escriba el apellido del usuario, en este caso **Simon**.

    f. Haga clic en **Guardar**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_57.png)

6. Asigne **User Roles** (Roles de usuario) y **Client Access** (Acceso de cliente) al usuario. 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Origami.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Origami, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Origami**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-origami-tutorial/tutorial_origami_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Origami en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Origami.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-origami-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-origami-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-origami-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-origami-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-origami-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-origami-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-origami-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-origami-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-origami-tutorial/tutorial_general_203.png

