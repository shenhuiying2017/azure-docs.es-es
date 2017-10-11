---
title: "Tutorial: Integración de Azure Active Directory con TINFOIL SECURITY | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y TINFOIL SECURITY."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 614e4de3335574f4b56c7d641af4fcfafdb17d12
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Tutorial: Integración de Azure Active Directory con TINFOIL SECURITY

En este tutorial, aprenderá a integrar TINFOIL SECURITY con Azure Active Directory (Azure AD).

Integrar TINFOIL SECURITY con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a TINFOIL SECURITY.
- Puede permitir que los usuarios inicien sesión automáticamente en TINFOIL SECURITY (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con TINFOIL SECURITY, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en TINFOIL SECURITY

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar TINFOIL SECURITY desde la galería
2. Configuración y prueba del inicio de sesión único en Azure AD

## <a name="add-tinfoil-security-from-the-gallery"></a>Agregar TINFOIL SECURITY desde la galería
Para configurar la integración de TINFOIL SECURITY en Azure AD, debe agregar TINFOIL SECURITY desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar TINFOIL SECURITY desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **TINFOIL SECURITY**, seleccione **TINFOIL SECURITY** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![TINFOIL SECURITY desde la galería](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con TINFOIL SECURITY con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de TINFOIL SECURITY para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario correspondiente de TINFOIL SECURITY.

Para establecer la relación de vínculo, en TINFOIL SECURITY, asigne el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con TINFOIL SECURITY, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para permitir que los usuarios utilicen esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de TINFOIL SECURITY](#create-a-tinfoil-security-test-user)**: para tener un homólogo de Britta Simon en TINFOIL SECURITY que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**: para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación TINFOIL SECURITY.

**Para configurar el inicio de sesión único de Azure AD con TINFOIL SECURITY, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **TINFOIL SECURITY**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Inicio de sesión basado en SAML](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

3. En la sección **Dominio y direcciones URL de TINFOIL SECURITY**, el usuario no tiene que realizar ningún paso, ya que la aplicación se ha integrado previamente con Azure.

    ![Configurar inicio de sesión único](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


4. En la sección **Certificado de firma de SAML**, copie el valor de **HUELLA DIGITAL**.

    ![Sección Certificado de firma SAML](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

5. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:
    
    ![Atributos](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "Atributos")
    
    | Nombre del atributo    |   Valor de atributo |
    | ------------------- | -------------------- |
    | accountid | UXXXXXXXXXXXXX |
    
    a. Haga clic en **agregar atributo de usuario**.
    
    ![AGREGAR atributo](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "Attributes")
    
    ![AGREGAR atributo](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "Attributes")
    
    b. En el cuadro de texto **Nombre de atributo**, escriba **accountid**.
    
    c. En el cuadro de texto **Valor de atributo**, pegue el valor del identificador de cuenta que obtendrá más adelante en el tutorial.
    
    d. Haga clic en **Aceptar**.    

6. Haga clic en el botón **Guardar** .

    ![Botón Guardar](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_400.png)

7. En la sección **Configuración de TINFOIL SECURITY**, haga clic en **Configurar TINFOIL SECURITY** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configuración de TINFOIL SECURITY](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

8. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de TINFOIL SECURITY.

9. En la barra de herramientas de la parte superior, haga clic en el icono de **Mi cuenta**.
   
    ![Panel](./media/active-directory-saas-tinfoil-security-tutorial/ic798971.png "Panel")

10. Haga clic en **Seguridad**.
   
    ![Seguridad](./media/active-directory-saas-tinfoil-security-tutorial/ic798972.png "Seguridad")

11. Siga estos pasos en la página de configuración **Inicio de sesión único** :
   
    ![Inicio de sesión único](./media/active-directory-saas-tinfoil-security-tutorial/ic798973.png "Inicio de sesión único")
   
    a. Seleccione **Habilitar SAML**.
   
    b. Haga clic en **Configuración manual**.
   
    c. En el cuadro de texto **SAML Post URL** (Dirección URL de publicación de SAML), pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.
   
    d. En el cuadro de texto **SAML Certificate Fingerprint** (Huella digital de certificado de SAML), pegue el valor de **Huella digital** del certificado que haya copiado de la sección **Certificado de firma de SAML**.
  
    e. Copie el valor del **identificador de la cuenta** y péguelo en el cuadro de texto **Valor de atributo** de la sección **Agregar atributo** de Azure Portal.
   
    f. Haga clic en **Guardar**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Usuarios y grupos -> Todos los usuarios ](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Usuario](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="create-a-tinfoil-security-test-user"></a>Creación de un usuario de prueba de TINFOIL SECURITY

Para permitir que los usuarios de Azure AD inicien sesión en TINFOIL SECURITY, deben aprovisionarse en TINFOIL SECURITY. En el caso de TINFOIL SECURITY, el aprovisionamiento es una tarea manual.

**Siga estos pasos para que se aprovisione un usuario:**

1. Si el usuario forma parte de una cuenta de empresa, deberá [ponerse en contacto con el equipo de soporte técnico de TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) para que se cree la cuenta del usuario.

2. Si el usuario es usuario habitual de SaaS de TINFOIL SECURITY, puede agregar un colaborador a cualquiera de sus sitios. Esta acción desencadena un proceso para enviar una invitación al correo electrónico especificado y crear una cuenta de usuario de TINFOIL SECURITY.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de TINFOIL SECURITY que ofrezca TINFOIL SECURITY para aprovisionar cuentas de usuario de Azure AD.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a TINFOIL SECURITY.

![Asignar usuario][200] 

**Para asignar Britta Simon a TINFOIL SECURITY, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **TINFOIL SECURITY**.

    ![Selección de TINFOIL SECURITY](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de TINFOIL SECURITY en el panel de acceso, debería iniciar sesión automáticamente en la aplicación TINFOIL SECURITY. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_203.png

