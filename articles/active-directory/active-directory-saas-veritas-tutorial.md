---
title: "Tutorial: Integración de Azure Active Directory con Veritas Enterprise Vault.cloud SSO | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Veritas Enterprise Vault.cloud SSO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 1f8c7fd97021f320a23bc78466a7b61f2d7e513e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Tutorial: Integración de Azure Active Directory con Veritas Enterprise Vault.cloud SSO

En este tutorial, aprenderá a integrar Veritas Enterprise Vault.cloud SSO con Azure Active Directory (Azure AD).

La integración de Veritas Enterprise Vault.cloud SSO con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Veritas Enterprise Vault.cloud SSO.
- Puede permitir que los usuarios inicien sesión automáticamente en Veritas Enterprise Vault.cloud SSO (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Veritas Enterprise Vault.cloud SSO, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en Veritas Enterprise Vault.cloud SSO

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Veritas Enterprise Vault.cloud SSO desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Adición de Veritas Enterprise Vault.cloud SSO desde la galería
Para configurar la integración de Veritas Enterprise Vault.cloud SSO en Azure AD, deberá agregar Veritas Enterprise Vault.cloud SSO desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Veritas Enterprise Vault.cloud SSO desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Veritas Enterprise Vault.cloud SSO**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_search.png)

5. En el panel de resultados, seleccione **Veritas Enterprise Vault.cloud SSO** y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Veritas Enterprise Vault.cloud SSO con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Veritas Enterprise Vault.cloud SSO para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Veritas Enterprise Vault.cloud SSO.

Para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario** de Veritas Enterprise Vault.cloud SSO.

Para configurar y probar el inicio de sesión único de Azure AD con Veritas Enterprise Vault.cloud SSO, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Veritas Enterprise Vault.cloud SSO](#creating-a-veritas-enterprise-vaultcloud-sso-test-user)**: para tener un homólogo de Britta Simon en Veritas Enterprise Vault.cloud SSO que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure portal y configurará el inicio de sesión único en la aplicación Veritas Enterprise Vault.cloud SSO.

**Para configurar el inicio de sesión único en Azure AD con Veritas Enterprise Vault.cloud SSO, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **Veritas Enterprise Vault.cloud SSO**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_samlbase.png)

3. En la sección **Dominio y direcciones URL de Veritas Enterprise Vault.cloud SSO**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`.
    
    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html) para obtener este valor. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-veritas-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Veritas Enterprise Vault.cloud SSO**, haga clic en **Configurar Veritas Enterprise Vault.cloud SSO** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_configure.png) 

7. Para configurar el inicio de sesión único en **Veritas Enterprise Vault.cloud SSO**, es preciso enviar el **certificado (Base64)** descargado y la **dirección URL del servicio de inicio de sesión único de SAML** al [equipo de soporte técnico de Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-veritas-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-veritas-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-veritas-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-veritas-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-veritas-enterprise-vaultcloud-sso-test-user"></a>Creación de un usuario de prueba de Veritas Enterprise Vault.cloud SSO

En esta sección, creará un usuario llamado Britta Simon en Veritas Enterprise Vault.cloud SSO. Trabaje con el [equipo de soporte técnico de Veritas Enterprise Vault.cloud SSO](https://www.veritas.com/support/.html) para agregar los usuarios a la plataforma de Veritas Enterprise Vault.cloud SSO. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Veritas Enterprise Vault.cloud SSO.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Veritas Enterprise Vault.cloud SSO, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Veritas Enterprise Vault.cloud SSO**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Veritas Enterprise Vault.cloud SSO en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Veritas Enterprise Vault.cloud SSO.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_203.png

