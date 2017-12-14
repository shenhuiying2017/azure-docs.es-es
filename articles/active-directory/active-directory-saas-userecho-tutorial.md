---
title: "Tutorial: Integración de Azure Active Directory con UserEcho | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y UserEcho."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: a3e2d6263b475ca72df93f0f20874971409e4c54
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Tutorial: integración de Azure Active Directory con UserEcho

En este tutorial, aprenderá a integrar UserEcho con Azure Active Directory (Azure AD).

Integrar UserEcho con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a UserEcho.
- Puede permitir que los usuarios inicien sesión automáticamente en UserEcho (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con UserEcho, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción a UserEcho habilitada para inicio de sesión único

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, aquí puede obtener una versión de evaluación de un mes: [Oferta de prueba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de UserEcho desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-userecho-from-the-gallery"></a>Incorporación de UserEcho desde la galería
Para configurar la integración de UserEcho en Azure AD, deberá agregar UserEcho desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar UserEcho desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **UserEcho**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_search.png)

5. En el panel de resultados, seleccione **UserEcho** y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con UserEcho con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo en UserEcho de un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de UserEcho.

Para establecer la relación de vínculo, en UserEcho, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con UserEcho, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de UserEcho](#creating-a-userecho-test-user)**: para tener un homólogo de Britta Simon en UserEcho que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación UserEcho.

**Para configurar el inicio de sesión único de Azure AD con UserEcho, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **UserEcho**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_samlbase.png)

3. En la sección **Dominio y direcciones URL de UserEcho**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.userecho.com/`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de UserEcho](https://feedback.userecho.com/) para obtener estos valores. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de UserEcho**, haga clic en **Configurar UserEcho** para abrir la ventana **Configurar inicio de sesión**. Copie las **direcciones URL del servicio de inicio de sesión único de SAML y de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_configure.png) 

7. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de UserEcho como administrador.

8. En la barra de herramientas de la parte superior, haga clic en el nombre de usuario para expandir el menú y, después, haga clic en **Instalación**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 

9. Haga clic en **Integraciones**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) 

10. Haga clic en **Sitio web** y, después, en **Inicio de sesión único (SAML2)**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) 

11. Siga estos pasos en la página **Inicio de sesión único (SAML)** :
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png)
    
    a. En **Habilitado para SAML**, seleccione **Sí**.
    
    b. Pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que copió de Azure Portal en el cuadro de texto **SAML SSO URL** (Dirección URL de inicio de sesión de SAML).
    
    c. Pegue el valor de la **dirección URL de cierre de sesión** que copió de Azure Portal en el cuadro de texto **Remote logoout URL** (Dirección URL de cierre de sesión remoto).
    
    d. Abra el certificado descargado en el Bloc de notas, copie el contenido y luego péguelo en el cuadro de texto **Certificado X.509** .
    
    e. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-userecho-test-user"></a>Creación de un usuario de prueba de UserEcho

El objetivo de esta sección es crear una usuaria de prueba llamada Britta Simon en UserEcho.

**Para crear una usuaria llamada Britta Simon en UserEcho, realice los pasos siguientes:**

1. Inicie sesión en el sitio de compañía de UserEcho como administrador.

2. En la barra de herramientas de la parte superior, haga clic en el nombre de usuario para expandir el menú y, después, haga clic en **Instalación**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png)

3. Haga clic en **Usuarios** para expandir la sección **Usuarios**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png)

4. Haga clic en **Usuarios**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png)

5. Haga clic en **Invitar a un nuevo usuario**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png)

6. En el cuadro de diálogo **Invitar a un nuevo usuario** , realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png)

    a. En el cuadro de texto **Nombre**, escriba el nombre de un usuario, por ejemplo, Britta Simon.
    
    b.  En el cuadro de texto **Correo electrónico**, escriba la dirección de correo electrónico de un usuario, por ejemplo, Brittasimon@contoso.com.
    
    c. Haga clic en **Invitar**.

Se envía una invitación a Britta que le permite empezar a usar UserEcho. 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a UserEcho para que use el inicio de sesión único de Azure.

![Asignar usuario][200] 

**Para asignar a Britta Simon a UserEcho, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **UserEcho**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.  

Al hacer clic en el icono de UserEcho en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación UserEcho.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png

