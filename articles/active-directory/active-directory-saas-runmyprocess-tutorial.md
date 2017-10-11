---
title: "Tutorial: Integración de Azure Active Directory con RunMyProcess | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y RunMyProcess."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: f8a08ef4f90d5cb98e7648ae6001055a3f4696e8
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Tutorial: Integración de Azure Active Directory con RunMyProcess

En este tutorial, aprenderá a integrar RunMyProcess con Azure Active Directory (Azure AD).

La integración de RunMyProcess con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a RunMyProcess
- Puede permitir que los usuarios inicien sesión automáticamente en RunMyProcess (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con RunMyProcess se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en RunMyProcess

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, aquí puede obtener una versión de evaluación de un mes: [Oferta de prueba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de RunMyProcess desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-runmyprocess-from-the-gallery"></a>Adición de RunMyProcess desde la galería
Para configurar la integración de RunMyProcess en Azure AD, será preciso que agregue RunMyProcess desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar RunMyProcess desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **RunMyProcess**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_search.png)

5. En el panel de resultados, seleccione **RunMyProcess** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con RunMyProcess con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de RunMyProcess para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de RunMyProcess.

Para establecer la relación de vínculo, en RunMyProcess, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con RunMyProcess, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de RunMyProcess](#creating-a-runmyprocess-test-user)**: para tener un homólogo de Britta Simon en RunMyProcess que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación RunMyProcess.

**Para configurar el inicio de sesión único de Azure AD con RunMyProcess, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **RunMyProcess**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_samlbase.png)

3. En la sección **Dominio y direcciones URL de RunMyProcess**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://live.runmyprocess.com/live/<tenant id>`.

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte al cliente de RunMyProcess](mailto:support@runmyprocess.com) para obtener este valor. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de RunMyProcess**, haga clic en **Configurar RunMyProcess** para abrir la ventana **Configurar inicio de sesión**. Copie los valores **Sign-Out URL y SAML Single Sign-On Service URL** (Dirección URL de cierre de sesión y Dirección URL del servicio de inicio de sesión único de SAML) de la **sección de referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_configure.png) 

7. En otra ventana del explorador web, inicie sesión en su inquilino de RunMyProcess como administrador.

8. En el panel de navegación izquierdo, haga clic en **Account** (Cuenta) y seleccione **Configuration** (Configuración).
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)

9. Vaya a la sección **Authentication method** (Método de autenticación) y realice los siguientes pasos:
   
    ![Configuración del inicio de sesión único en la aplicación](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. En **Método**, seleccione **SSO con Samlv2**. 

    b. En el cuadro de texto **SSO redirect** (Redirección de SSO), pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    c. En el cuadro de texto **Logout redirect** (Redirección de cierre de sesión), pegue el valor de la **dirección URL de cierre de sesión** que copió de Azure Portal.

    d. En el cuadro de texto **Name Id Format** (Formato de identificador de nombre), escriba el valor del **formato de identificador de nombre** como en **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.

    e. Copie el contenido del archivo de certificado descargado y péguelo en el cuadro de texto **Certificate** (Certificado). 
 
    f. Haga clic en el icono **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-runmyprocess-test-user"></a>Creación de un usuario de prueba de RunMyProcess

Para permitir que los usuarios de Azure AD inicien sesión en RunMyProcess, deben aprovisionarse en RunMyProcess. En el caso de RunMyProcess, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. Inicie sesión en su sitio de la compañía de RunMyProcess como administrador.

2. Haga clic en **Account** (Cuenta) y seleccione **Users** (Usuarios) en el panel de navegación izquierdo y, a continuación, haga clic en **New User** (Usuario nuevo).
   
    ![Nuevo usuario](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "Nuevo usuario")

3. En la sección **Configuración del usuario** , lleve a cabo estos pasos:
   
    ![Perfil](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "Perfil") 
  
    a. Escriba el **nombre** y la **dirección de correo electrónico** de una cuenta de Azure AD válida que quiera aprovisionar en los cuadros de texto correspondientes. 

    b. Seleccione un **lenguaje IDE**, un **lenguaje** y un **perfil**. 

    c. Seleccione **Enviarme mensaje de correo electrónico de creación de cuenta**. 

    d. Haga clic en **Guardar**.
   
    >[!NOTE]
    >Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de RunMyProcess que proporcione RunMyProcess para aprovisionar cuentas de usuario de Azure Active Directory. 
    > 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a RunMyProcess para que use el inicio de sesión único de Azure.

![Asignar usuario][200] 

**Para asignar Britta Simon a RunMyProcess, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **RunMyProcess**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de RunMyProcess en el panel de acceso, debería iniciar sesión automáticamente en su aplicación RunMyProcess.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png

