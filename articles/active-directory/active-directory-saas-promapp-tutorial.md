---
title: "Tutorial: Integración de Azure Active Directory con Promapp | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Promapp."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: jeedes
ms.openlocfilehash: 4a0630bf015361833ed3a6949ea7b29450d53701
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Tutorial: Integración de Azure Active Directory con Promapp

En este tutorial, obtendrá información sobre cómo integrar Promapp con Azure Active Directory (Azure AD).

Integrar Promapp con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Promapp.
- Puede permitir que los usuarios inicien sesión automáticamente en Promapp (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Promapp, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Promapp

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Promapp desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-promapp-from-the-gallery"></a>Incorporación de Promapp desde la galería
Para configurar la integración de Promapp en Azure AD, deberá agregar Promapp desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Promapp desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Promapp**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_search.png)

5. En el panel de resultados, seleccione **Promapp** y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Promapp con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Promapp para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Promapp.

Para establecer la relación de vínculo, en Promapp, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Promapp, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Promapp](#creating-a-promapp-test-user)**: Para tener un homólogo de Britta Simon en Promapp que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en su aplicación Promapp.

**Para configurar el inicio de sesión único de Azure AD con Promapp, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Promapp**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_samlbase.png)

3. En la sección **Dominio y direcciones URL de Promapp**, realice los siguientes pasos si desea configurar la aplicación en el modo iniciado por **IDP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón:
    | |
    |--|
    | `https://demo.promapp.com/TENANTNAME`|
    | `https://go.promapp.com/TENANTNAME`|
    | `https://demoau.promapp.com/TENANTNAME`|
    | `https://au.promapp.com/TENANTNAME`|
    | `https://demous.promapp.com/TENANTNAME`|
    | `https://us.promapp.com/TENANTNAME`|
    | `https://dev.promapp.com/TENANTNAME`|
    | `https://test.promapp.com/TENANTNAME`|
    | `https://staging.promapp.com/TENANTNAME`|
    
    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://DOMAINNAME.promapp.com/azuread/saml/authenticate.aspx`.

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Configurar inicio de sesión único](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://DOMAINNAME.promapp.com/TENANTNAME/saml/authenticate`.

    > [!NOTE] 
    > Estos valores no son reales. Actualícelos con la dirección URL de inicio de sesión, el identificador y la dirección URL de respuesta reales. Póngase en contacto con el [equipo de soporte técnico de Promapp](https://www.promapp.com/about-us/contact-us/) para obtener estos valores.

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-promapp-tutorial/tutorial_general_400.png)

7. En la sección **Configuración de Promapp**, haga clic en **Configurar Promapp** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_configure.png) 

8. Inicie sesión en su sitio de la empresa Promapp como administrador. 

9. En el menú de la parte superior, haga clic en **Administrador**. 
   
    ![Inicio de sesión único de Azure AD][12]

10. Haga clic en **Configurar**. 
   
    ![Inicio de sesión único de Azure AD][13]

11. En la pestaña **Seguridad** , lleve a cabo estos pasos:
   
    ![Inicio de sesión único de Azure AD][14]
    
    a. Pegue el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) que ha copiado de Azure Portal en el cuadro de texto **SSO-Login URL** (Dirección URL de inicio de sesión único).
    
    b. En **SSO - Single Sign-on Mode** (SSO - Modo de inicio de sesión único), seleccione **Optional** (Opcional) y haga clic en **Save** (Guardar).

    > [!NOTE]
    > El modo **Opcional** es solo para pruebas. Una vez que esté satisfecho con la configuración, seleccione el modo **Requerido** para exigir a todos los usuarios que se autentiquen mediante Azure AD.

    c. Abra el certificado descargado en el Bloc de notas, copie su contenido sin la primera línea (-----**BEGIN CERTIFICATE**-----) ni la última (-----**END CERTIFICATE**-----), péguelo en el cuadro de texto **SSO-Certificado X.509** y haga clic en **Guardar**.
        
> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-promapp-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-promapp-test-user"></a>Creación de un usuario de prueba de Promapp

La aplicación Promapp admite aprovisionamiento Just-in-Time. Esto significa que, si es necesario, se crea automáticamente una cuenta de usuario durante un intento de acceso a la aplicación mediante el Panel de acceso.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Promapp.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Promapp, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Promapp**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-promapp-tutorial/tutorial_promapp_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Para probar la aplicación en modo iniciado por **IDP**, al hacer clic en el icono de Promapp del panel de acceso, debería iniciar sesión automáticamente en su aplicación Promapp.

Para probar la aplicación en modo iniciado por **SP**, tendrá que iniciar la autenticación desde el sitio de Promapp. Para hacerlo, deje en blanco el campo de contraseña al iniciar sesión mientras el modo **Opcional** está habilitado.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_04.png
[12]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/active-directory-saas-promapp-tutorial/tutorial_promapp_07.png

[100]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-promapp-tutorial/tutorial_general_203.png

