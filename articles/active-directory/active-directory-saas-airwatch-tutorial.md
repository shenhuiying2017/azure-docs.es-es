---
title: "Tutorial: Integración de Azure Active Directory con AirWatch | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y AirWatch."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: a0e314aaf59a70107058829ea84dde362f097274
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Tutorial: Integración de Azure Active Directory con AirWatch

En este tutorial, aprenderá a integrar AirWatch con Azure Active Directory (Azure AD).

La integración de AirWatch con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a AirWatch.
- Puede permitir que los usuarios inicien sesión automáticamente en AirWatch (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con AirWatch, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en AirWatch

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de AirWatch desde la Galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-airwatch-from-the-gallery"></a>Incorporación de AirWatch desde la Galería
Para configurar la integración de AirWatch en Azure AD, es preciso agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar AirWatch desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **AirWatch**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_search.png)

5. En el panel de resultados, seleccione **AirWatch** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con AirWatch con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de AirWatch para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario asociado de AirWatch.

Para establecer esta relación de vínculo, se asigna el valor del **nombre de usuario** en Azure AD como valor de **Nombre de usuario** en AirWatch.

Para configurar y probar el inicio de sesión único de Azure AD con AirWatch, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de AirWatch](#creating-a-airwatch-test-user)**: para tener un homólogo de Britta Simon en AirWatch vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación AirWatch.

**Para configurar el inicio de sesión único de Azure AD con AirWatch, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **AirWatch**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. En la sección **AirWatch Domain and URLs** (Dominios y direcciones URL de AirWatch), lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`.

    b. En el cuadro de texto **Identificador**, escriba el valor como `AirWatch`.

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de AirWatch](http://www.air-watch.com/company/contact-us/) para obtenerlo. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo XML en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. En la sección **AirWatch Configuration** (Configuración de AirWatch), haga clic en **Configure AirWatch** (Configurar AirWatch) para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_configure.png) 

6. Haga clic en el botón **Guardar**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-airwatch-tutorial/tutorial_general_400.png)
<CS>
7. En otra ventana del explorador web, inicie sesión en el sitio de AirWatch de la compañía como administrador.

8. En el panel de navegación izquierdo, haga clic en **Accounts** (Cuentas) y, luego, en **Administrators** (Administradores).
   
   ![Administradores](./media/active-directory-saas-airwatch-tutorial/ic791920.png "Administradores")

9. Expanda el menú **Settings** (Configuración) y, a continuación, haga clic en **Directory Services** (Servicios de directorio).
   
   ![Configuración](./media/active-directory-saas-airwatch-tutorial/ic791921.png "Configuración")

10. Haga clic en la pestaña **Usuario**; en el cuadro de texto **DN base**, escriba el nombre de dominio y haga clic en **Guardar**.
   
   ![Usuario](./media/active-directory-saas-airwatch-tutorial/ic791922.png "Usuario")

11. Haga clic en la pestaña **Server** (Servidor).
   
   ![Servidor](./media/active-directory-saas-airwatch-tutorial/ic791923.png "Servidor")

12. Lleve a cabo los siguiente pasos:
    
    ![Cargar](./media/active-directory-saas-airwatch-tutorial/ic791924.png "Cargar")   
    
    a. En **Directory Type** (Tipo de directorio), seleccione **None** (Ninguno).

    b. Seleccione **Use SAML For Authentication**(Usar SAML para autenticación).

    c. Para cargar el certificado descargado, haga clic en **Upload**(Cargar).

13. En la sección **Request** (Solicitud), siga estos pasos:
    
    ![Solicitud](./media/active-directory-saas-airwatch-tutorial/ic791925.png "Solicitud")  

    a. En **Request Binding Type** (Tipo de enlace de solicitud), seleccione **POST**.

    b. En Azure Portal, en la página de diálogo **Configure single sign-on at Airwatch** (Configurar inicio de sesión único en Airwatch), copie el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) y péguelo en el cuadro de texto **Identity Provider Single Sign On URL** (Dirección URL de inicio de sesión del proveedor de identidades).

    c. En la lista **NameID Format** (Formato de NameID), seleccione **Email address** (Dirección de correo electrónico).

    d. Haga clic en **Guardar**.

14. Haga clic de nuevo en la pestaña **User** (Usuario).
    
    ![Usuario](./media/active-directory-saas-airwatch-tutorial/ic791926.png "Usuario")

15. En la sección **Attribute** (Atributo), realice estos pasos:
    
    ![Atributo](./media/active-directory-saas-airwatch-tutorial/ic791927.png "Atributo")

    a. En el cuadro de texto **Identificador de objeto**, escriba **http://schemas.microsoft.com/identity/claims/objectidentifier**.

    b. En el cuadro de texto **Nombre de usuario**, escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    c. En el cuadro de texto **Nombre para mostrar**, escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    d. En el cuadro de texto **Nombre**, escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. En el cuadro de texto **Apellidos**, escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. En el cuadro de texto **Correo electrónico**, escriba **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    g. Haga clic en **Guardar**.

<CE>

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-airwatch-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-airwatch-test-user"></a>Creación de un usuario de prueba de AirWatch

Para permitir que los usuarios de Azure AD inicien sesión en AirWatch, tienen que aprovisionarse en AirWatch.

* En el caso de AirWatch, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en el sitio de la compañía de **AirWatch** como administrador.
2. En el panel de navegación izquierdo, haga clic en **Accounts** (Cuentas) y luego en **Users** (Usuarios).
   
   ![Usuarios](./media/active-directory-saas-airwatch-tutorial/ic791929.png "Usuarios")
3. En el menú **Users** (Usuarios), haga clic en **List View** (Vista de lista) y, a continuación, haga clic en **Add \> Add User** (Agregar > Agregar usuario).
   
   ![Agregar usuario](./media/active-directory-saas-airwatch-tutorial/ic791930.png "Agregar usuario")
4. En el cuadro de diálogo **Add / Edit User** (Agregar/Editar usuario), realice los siguientes pasos:

   ![Agregar usuario](./media/active-directory-saas-airwatch-tutorial/ic791931.png "Agregar usuario")   
   1. Especifique **Username** (Nombre de usuario), **Password** (Contraseña), **Confirm Password** (Confirmar contraseña), **First Name** (Nombre), **Last Name** (Apellido), **Email Address** (Correo electrónico) de una cuenta de Azure Active Directory válida que quiera aprovisionar en los cuadros de texto relacionados.
   2. Haga clic en **Guardar**.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de AirWatch ofrecida por AirWatch para aprovisionar cuentas de usuario de AAD.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a AirWatch.

![Asignar usuario][200] 

**Para asignar Britta Simon a AirWatch, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **AirWatch**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-airwatch-tutorial/tutorial_airwatch_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-airwatch-tutorial/tutorial_general_203.png

