---
title: "Tutorial: integración de Azure Active Directory con BlueJeans | Microsoft Docs"
description: "Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y BlueJeans."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: jeedes
ms.openlocfilehash: 03bf65852b8d3cf14aebf155891a028db86e78d0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Tutorial: Integración de Azure Active Directory con BlueJeans

En este tutorial, obtendrá información sobre cómo integrar BlueJeans con Azure Active Directory (Azure AD).

La integración de BlueJeans con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a BlueJeans
- Puede permitir que los usuarios inicien sesión automáticamente en BlueJeans (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con BlueJeans, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en BlueJeans

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de BlueJeans desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-bluejeans-from-the-gallery"></a>Incorporación de BlueJeans desde la galería
Para configurar la integración de BlueJeans en Azure AD, será preciso que agregue BlueJeans desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar BlueJeans desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **BlueJeans**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_search.png)

5. En el panel de resultados, seleccione **BlueJeans** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con BlueJeans con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de BlueJeans para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de BlueJeans.

Para establecer la relación de vínculo, en BlueJeans, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con BlueJeans, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de BlueJeans](#creating-a-bluejeans-test-user)**: para tener un homólogo de Britta Simon en BlueJeans que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación BlueJeans.

**Para configurar el inicio de sesión único de Azure AD con BlueJeans, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **BlueJeans**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_samlbase.png)

3. En la sección **Dominio y direcciones URL de BlueJeans**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.BlueJeans.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.BlueJeans.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de BlueJeans](https://support.bluejeans.com/contact) para obtener estos valores. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-bluejeans-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de BlueJeans**, haga clic en **Configurar BlueJeans** para abrir la ventana **Configurar inicio de sesión**. Copie las **direcciones URL del servicio de inicio de sesión único de SAML, de cambio de contraseña y de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_configure.png) 

7. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la empresa de **BlueJeans**.

8. Vaya a **ADMIN \> Group Settings \> Security**.
   
   ![Administración](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Administración")

9. En la sección **Seguridad** , realice estos pasos:
   
   ![Inicio de sesión único de SAML](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "Inicio de sesión único de SAML")   
   
   a. Seleccione **SAML Single Sign On**(Inicio de sesión único de SAML).
  
   b. Seleccione **Enable automatic provisioning**(Habilitar aprovisionamiento automático).

10. Continúe con los siguiente pasos:

    ![Ruta del certificado](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Ruta del certificado")
    
    a. Haga clic en **Elegir archivo**y cargue el certificado descargado.
   
    b. Pegue la **dirección URL del servicio de inicio de sesión único de SAML** en el cuadro de texto **Login URL** (URL de inicio de sesión).
   
    c. Pegue el valor de **Cambiar dirección URL de contraseña** en el cuadro de texto **Dirección URL de cambio de contraseña**.
   
    d. Pegue la **dirección URL de cierre de sesión** en el cuadro de texto **Logout URL** (URL de cierre de sesión).

11. Continúe con los siguiente pasos:
    
    ![Guardar cambios](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Guardar cambios")
    
    a. En el cuadro de texto **Id. de usuario**, escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
   
    b. En el cuadro de texto **Correo electrónico**, escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
   
    c. Haga clic en **Guardar cambios**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-bluejeans-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-bluejeans-test-user"></a>Creación de un usuario de prueba de BlueJeans

Para permitir que los usuarios de Azure AD inicien sesión en BlueJeans, tienen que aprovisionarse en BlueJeans.  

En el caso de BlueJeans, el aprovisionamiento es una tarea manual.

**Para aprovisionar cuentas de usuario, realice estos pasos:**

1. Inicie sesión como administrador en el sitio de la compañía de **BlueJeans** .

2. Vaya a **ADMIN \> Manage Users \> Add User**.
   
   ![Administración](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Administración")
   
   >[!IMPORTANT]
   >La pestaña **Add User** (Agregar usuario) está disponible solo si en la pestaña **Security** (Seguridad), la opción **Enable automatic provisioning** (Habilitar aprovisionamiento automático) está desactivada. 
   
3. En la sección **Add User** (Agregar usuario), realice estos pasos:

    ![Agregar usuario](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Agregar usuario")
    
    a. Escriba los datos de una cuenta de AAD válida que desee aprovisionar en los cuadros de texto correspondientes: **BlueJeans Username** (Nombre de usuario de BlueJeans), **Email address** (Dirección de correo electrónico), **BlueJeans Meeting ID** (Id. de reunión de BlueJeans), **Moderator Passcode** (Código de acceso de moderador), **Full Name** (Nombre completo) y **Company** (Empresa).
    
    b. Haga clic en **Agregar usuario**.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de BlueJeans que proporcione BlueJeans para aprovisionar cuentas de usuario de AAD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a BlueJeans.

![Asignar usuario][200] 

**Para asignar Britta Simon a BlueJeans, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **BlueJeans**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-bluejeans-tutorial/tutorial_bluejeans_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de BlueJeans del Panel de acceso, debería entrar en la página de inicio de sesión de la aplicación BlueJeans.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_203.png

