---
title: "Tutorial: Integración de Azure Active Directory con Adaptive Suite | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Adaptive Suite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 8e128ddf53a93fe30350d8e914657f3539701603
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>Tutorial: Integración de Azure Active Directory con Adaptive Suite

En este tutorial, aprenderá a integrar Adaptive Suite con Azure Active Directory (Azure AD).

La integración de Adaptive Suite con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Adaptive Suite
- Puede permitir que los usuarios inicien sesión automáticamente en Adaptive Suite (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Adaptive Suite, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Adaptive Suite

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Adaptive Suite desde la Galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-adaptive-suite-from-the-gallery"></a>Incorporación de Adaptive Suite desde la Galería
Para configurar la integración de Adaptive Suite en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Adaptive Suite desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Adaptive Suite**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_search.png)

5. En el panel de resultados, seleccione **Adaptive Suite** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Adaptive Suite con un usuario de prueba llamado Britta Simon.

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Adaptive Suite para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario asociado en Adaptive Suite.

Para establecer la relación de vínculo, en Adaptive Suite, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Adaptive Suite, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Adaptive Suite](#creating-an-adaptive-suite-test-user)**: para tener un homólogo de Britta Simon en Adaptive Suite vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Adaptive Suite.

**Para configurar el inicio de sesión único de Azure AD con Adaptive Suite, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Adaptive Suite**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_samlbase.png)

3. En la sección **Adaptive Suite Domain and URLs** (Dominio y direcciones URL de Adaptive Suite), lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`.

    >[!NOTE]
    > Puede obtener este valor de la página **SAML SSO Settings** (Configuración SSO de SAML) de Adaptive Suite.
    >  

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_400.png)

6. En la sección **Adaptive Suite Configuration** (Configuración de Adaptive Suite), haga clic en **Configure Adaptive Suite** (Configurar Adaptive Suite) para abrir la ventana **Configurar inicio de sesión**. Copie los valores de **SAML Entity ID y SAML Single Sign-On Service URL** (Identificador de entidad de SAML y URL del servicio de inicio de sesión único de SAML) de la sección de **referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_configure.png) 

7. En otra ventana del explorador web, inicie sesión en el sitio de Adaptive Suite de la compañía como administrador.

8. Vaya a **Administración**.
   
    ![Administración](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "Administración")

9. En la sección **Users and Roles** (Usuarios y roles), haga clic en **Manage SAML SSO Settings** (Administrar configuración de SSO de SAML).
   
    ![Manage SAML SSO Settings (Administrar configuración de SSO de SAML)](./media/active-directory-saas-adaptivesuite-tutorial/IC805645.png "Manage SAML SSO Settings (Administrar configuración de SSO de SAML)")

10. En la página **SAML SSO Settings** (Configuración de SSO de SAML), realice los pasos siguientes:
   
    ![SAML SSO Settings (Configuración de SSO de) SAML](./media/active-directory-saas-adaptivesuite-tutorial/IC805646.png "SAML SSO Settings (Configuración de SSO de) SAML")

    a. En el cuadro de texto **Identity provider name** (Nombre del proveedor de identidades), escriba el nombre de la configuración.
    
    b. Pegue el valor del **SAML Entity ID** (Identificador de entidad de SAML) que ha copiado de Azure Portal en el cuadro de texto **Identity Provider Entity ID** (Id. de entidad del proveedor de identidades).
  
    c. Pegue el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) que copió de Azure Portal en el cuadro de texto **Identity provider SSO URL** (Dirección URL de SSO del proveedor de identidades).
  
    d. Pegue el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) que copió de Azure Portal en el cuadro de texto **Custom logout URL** (Dirección URL de cierre de sesión personalizada).
  
    e. Para cargar el certificado descargado, haga clic en **Choose file**(Elegir archivo).
  
    f. Seleccione las opciones siguientes en cada caso:
    * En **SAML user id** (identificador de usuario de SAML), seleccione **User’s Adaptive Insights user name** (Nombre de usuario de Adaptive Insights del usuario).
    * En **SAML user id location** (Ubicación del id. de usuario de SAML), seleccione **User id in NameID of Subject** (identificador de usuario en NameID del tema).
    * En **SAML NameID format** (Formato de NameID de SAML), seleccione **Email address** (Dirección de correo electrónico).
    * En **Enable SAML** (Habilitar SAML), seleccione **Allow SAML SSO and direct Adaptive Insights login** (Permitir inicio de sesión único de SAML e inicio de sesión directo de Adaptive Insights).
    
    g. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-an-adaptive-suite-test-user"></a>Creación de un usuario de prueba de Adaptive Suite

Para permitir que los usuarios de Azure AD inicien sesión en Adaptive Suite, deben aprovisionarse en este último.  

* En el caso de Adaptive Suite, el aprovisionamiento es una tarea manual.

**Siga estos pasos para configurar el aprovisionamiento de usuario:** 

1. Inicie sesión en el sitio de la compañía de **Adaptive Suite** como administrador.
2. Vaya a **Administración**.
   
   ![Administración](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "Administración")
3. En la sección **Users and Roles** (Usuarios y roles), haga clic en **Add User** (Agregar usuario).
   
   ![Agregar usuario](./media/active-directory-saas-adaptivesuite-tutorial/IC805648.png "Agregar usuario")
4. En la sección **Nuevo usuario** , lleve a cabo estos pasos:
   
   ![Enviar](./media/active-directory-saas-adaptivesuite-tutorial/IC805649.png "Enviar")   

   a. Escriba el **nombre**, el **inicio de sesión**, la **dirección de correo electrónico** y la **contraseña** o de un usuario válido de Azure Active Directory que quiera aprovisionar en los cuadros de texto relacionados.
  
   b. Seleccione un **Role**(rol).
  
   c. Haga clic en **Submit**(Enviar).

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Adaptive Suite ofrecida por Adaptive Suite para aprovisionar cuentas de usuario de AAD.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Adaptive Suite.

![Asignar usuario][200] 

**Para asignar Britta Simon a Adaptive Suite, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Adaptive Suite**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Microsoft Azure AD mediante el panel de acceso.

Al hacer clic en el icono Adaptive Suite en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Adaptive Suite.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_203.png

