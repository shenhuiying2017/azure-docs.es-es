---
title: "Tutorial: Integración de Azure Active Directory con Bime | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Bime."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bdcf0729-c880-4c95-b739-0f6345b17dd8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: f48c784fe2c1fa30a034147571fac218919fe574
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bime"></a>Tutorial: Integración de Azure Active Directory con Bime

En este tutorial, obtendrá información sobre cómo integrar Bime con Azure Active Directory (Azure AD).

La integración de Bime con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Bime
- Puede permitir que los usuarios inicien sesión automáticamente en Bime (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Bime, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción a Bime con el inicio de sesión único habilitado

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, aquí puede obtener una versión de evaluación de un mes: [Oferta de prueba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Bime desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-bime-from-the-gallery"></a>Adición de Bime desde la galería
Para configurar la integración de Bime en Azure AD, es preciso agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Bime desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Bime**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bime-tutorial/tutorial_bime_search.png)

5. En el panel de resultados, seleccione **Bime** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bime-tutorial/tutorial_bime_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, se configura y prueba el inicio de sesión único de Azure AD con Bime con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Bime para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Bime.

Para establecer la relación de vínculo, en Bime, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Bime, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Bime](#creating-a-bime-test-user)**: para tener un homólogo de Britta Simon en Bime que esté vinculado a la representación de Azure AD de usuario.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en su aplicación Bime.

**Para configurar el inicio de sesión único de Azure AD con Bime, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Bime**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-bime-tutorial/tutorial_bime_samlbase.png)

3. En la sección **Dominio y direcciones URL de Bime**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-bime-tutorial/tutorial_bime_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.Bimeapp.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.Bimeapp.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de Bime](https://bime.zendesk.com/hc/categories/202604307-Support-tech-notes-and-tips-) para obtener estos valores. 
 
4. En la sección **Certificado de firma de SAML**, copie el valor de **HUELLA DIGITAL** del certificado.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bime-tutorial/tutorial_bime_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-bime-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Bime**, haga clic en **Configurar Bime** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bime-tutorial/tutorial_bime_configure.png) 

7. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Bime.

8. En la barra de herramientas, haga clic en **Admin** y, luego, en **Account** (Cuenta).
   
    ![Administración](./media/active-directory-saas-bime-tutorial/ic775558.png "Administración")

9. En la página de configuración de la cuenta, realice los siguientes pasos:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-bime-tutorial/ic775559.png "Configurar inicio de sesión único")
   
    a. Seleccione **Enable SAML authentication** (Habilitar autenticación SAML).

    b. En el cuadro de texto **Remote Login URL** (Dirección URL de inicio de sesión remoto), pegue el valor de **Dirección URL de servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    c.  Pegue el valor de **Huella digital** de Azure Portal en el cuadro de texto **Certificate fingerprint** (Huella digital de certificado).       
   
    d. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bime-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bime-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bime-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bime-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-bime-test-user"></a>Creación de usuario de prueba de Bime

Para permitir que los usuarios de Azure AD inicien sesión en Bime, tienen que aprovisionarse en Bime. En el caso de Bime, el aprovisionamiento es una tarea manual.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. Inicie sesión en su inquilino de **Bime** .

2. En la barra de herramientas, haga clic en **Admin** y, luego, en **Users** (Usuarios).
   
    ![Administración](./media/active-directory-saas-bime-tutorial/ic775561.png "Administración")

3. En **Users List** (Lista de usuarios), haga clic en **Agregar nuevo usuario** (+).
   
    ![Usuarios](./media/active-directory-saas-bime-tutorial/ic775562.png "Usuarios")

4. En la página de diálogo **User Details** (Detalles del usuario), siga estos pasos:
   
    ![Detalles del usuario](./media/active-directory-saas-bime-tutorial/ic775563.png "Detalles del usuario")
   
    a. En el cuadro de texto **Nombre**, escriba el nombre de usuario, en este caso **Britta**.

    b. En el cuadro de texto **Apellidos**, escriba el apellido del usuario, en este caso **Simon**.
 
    c. En el cuadro de texto **E-mail** (Correo electrónico), escriba el correo electrónico del usuario con el siguiente formato **brittasimon@contoso.com**.

    d. Haga clic en **Guardar**.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Bime que proporcione Bime para aprovisionar cuentas de usuario de AAD.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Bime.

![Asignar usuario][200] 

**Para asignar Britta Simon a Bime, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Bime**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bime-tutorial/tutorial_bime_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Bime en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Bime.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bime-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bime-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bime-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bime-tutorial/tutorial_general_203.png

