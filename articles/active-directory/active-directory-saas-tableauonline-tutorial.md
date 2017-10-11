---
title: "Tutorial: Integración de Azure Active Directory con Tableau Online | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Tableau Online."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: jeedes
ms.openlocfilehash: 443fab1198a91a4d5749e6421f7b8603fc75a81e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Tutorial: Integración de Azure Active Directory con Tableau Online

En este tutorial, obtendrá información sobre cómo integrar Tableau Online con Azure Active Directory (Azure AD).

Integrar Tableau Online con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Tableau Online.
- Puede permitir que los usuarios inicien sesión automáticamente en Tableau Online (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Tableau Online, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Tableau Online

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Tableau Online desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-tableau-online-from-the-gallery"></a>Incorporación de Tableau Online desde la galería
Para configurar la integración de Tableau Online en Azure AD, será preciso que agregue Tableau Online desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Tableau Online desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Tableau Online**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_search.png)

5. En el panel de resultados, seleccione **Tableau Online** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Tableau Online con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Tableau Online para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Tableau Online.

Para establecer la relación de vínculo, en Tableau Online, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Tableau Online, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Tableau Online](#creating-a-tableau-online-test-user)** : para tener un homólogo de Britta Simon en Tableau Online vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Tableau Online.

**Para configurar el inicio de sesión único de Azure AD con Tableau Online, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Tableau Online**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_samlbase.png)

3. En la sección **Tableau Online Domain and URLs** (Dominio y direcciones URL de Tableau Online), lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_url.png)
    
    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL: `https://sso.online.tableau.com`

    b. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://sso.online.tableau.com/public/sp/<instancename>`

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_general_400.png)

6. En una ventana de explorador diferente, inicie sesión en su aplicación de Tableau Online como administrador. Vaya a **Settings** (Configuración) y luego a **Authentication** (Autenticación).
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)
    
7. Para habilitar SAML, en la sección **Tipos de autenticación**, marque la casilla **Inicio de sesión único con SAML**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)

8. Desplácese hacia abajo hasta la sección **Importar archivo de metadatos en Tableau Online** .  Haga clic en Examinar e importe el archivo de metadatos que ha descargado desde Azure AD. A continuación, haga clic en **Aplicar**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)

9. En la sección **Match assertions** (Aserciones de coincidencia), inserte el nombre de aserción del proveedor de identidades correspondiente para la **dirección de correo electrónico**, el **nombre** y los **apellidos**. Para obtener esta información a partir de Azure AD: 
  
    a. Vaya a la página de integración de aplicaciones de **Tableau Online** de Azure Portal.
    
    b. En la sección de atributos, seleccione la casilla **"Ver y editar todos los demás atributos de usuario"**. 
    
   ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/attributesection.png)
      
    c. Copie el valor del espacio de nombres de estos atributos: givenname, email y surname mediante los pasos siguientes:

   ![Inicio de sesión único de Azure AD ](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)
    
    d. Haga clic en el valor **user.givenname** 
    
    e. Copie el valor del cuadro de texto **espacio de nombres**.

   ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/attributesection2.png)

    f. Para copiar los valores del espacio de nombres del correo electrónico y el apellido, siga los pasos anteriores.

    g. Abra la aplicación Tableau Online y establezca la sección **Tableau Online Attributes** (Atributos de Tableau Online) como sigue:
     * Correo electrónico: **mail** o **userprincipalname**
     * Nombre: **givenname**
     * Apellidos: **surname**
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

> [!TIP]
> Ahora puede leer una versión concisa de estas instrucciones en [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-tableau-online-test-user"></a>Crear un usuario de prueba de Tableau Online

En esta sección, creará una usuaria llamada Britta Simon en Tableau Online.

1. En **Tableau Online**, haga clic en **Settings** (Configuración) y en la sección **Authentication** (Autenticación). Desplácese hacia abajo a la sección **Seleccionar usuarios** . Haga clic en **Add users** (Agregar usuarios) y en **Enter Email Addresses** (Especificar direcciones de correo electrónico).
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. Seleccione **Add users for single sign-on (SSO) authentication**[Agregar usuarios para la autenticación mediante inicio de sesión único (SSO)]. En el cuadro de texto **Especificar direcciones de correo electrónico** agregue britta.simon@contoso.com
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)
3. Haga clic en **Crear**.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Tableau Online.

![Asignar usuario][200] 

**Para asignar Britta Simon a Tableau Online, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Tableau Online**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Tableau Online en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Tableau Online.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png

