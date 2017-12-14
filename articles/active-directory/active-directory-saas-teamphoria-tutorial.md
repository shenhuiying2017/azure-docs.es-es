---
title: "Tutorial: Integración de Azure Active Directory con Teamphoria | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Teamphoria."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: 260c85b14032e17def01ded4a461e6337d66239b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Tutorial: Integración de Azure Active Directory con Teamphoria

En este tutorial, obtendrá información sobre cómo integrar Teamphoria con Azure Active Directory (Azure AD).

La integración de Teamphoria con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Teamphoria
- Puede permitir que los usuarios inicien sesión automáticamente en Teamphoria (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Teamphoria, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Teamphoria.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Teamphoria, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Teamphoria

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar Teamphoria desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-teamphoria-from-the-gallery"></a>Agregar Teamphoria desde la galería
Para configurar la integración de Teamphoria en Azure AD, es preciso agregar Teamphoria desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Teamphoria desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Teamphoria**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_search.png)

5. En el panel de resultados, seleccione **Teamphoria** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Teamphoria con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Teamphoria para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Teamphoria.

Para establecer esta relación de vínculo, se asigna el valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Teamphoria.

Para configurar y probar el inicio de sesión único de Azure AD con Teamphoria, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba para Teamphoria](#creating-a-teamphoria-test-user)** : para tener un homólogo de Britta Simon en Teamphoria que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de administración de Azure y configurará el inicio de sesión único en la aplicación Teamphoria.

**Para configurar el inicio de sesión único de Azure AD con Teamphoria, realice los pasos siguientes:**

1. En el Portal de administración de Azure, en la página de integración de la aplicación **Teamphoria**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

3. En la sección de **dominio y direcciones URL de Teamphoria**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL con el siguiente patrón: `https://<sub-domain>.teamphoria.com/login`.    

    > [!NOTE] 
    > Tenga en cuenta que estos no son valores reales. Tendrá que actualizar estos valores con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de Teamphoria](https://www.teamphoria.com/) para obtener la dirección URL de inicio de sesión. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-teamphoria-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Teamphoria**, haga clic en **Configurar Teamphoria** para abrir la ventana **Configurar inicio de sesión**. Copie la **SAML Single Sign-On Service URL** (URL del servicio de inicio de sesión único de SAML) de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_configure.png) 

7. Para configurar el inicio de sesión único en **Teamphoria**, inicie sesión en la aplicación Teamphoria como administrador.

8. Vaya a la opción **ADMIN SETTINGS** (Configuración de administración) de la barra de herramientas izquierda y, en la pestaña Configure (Configurar), haga clic en **SINGLE SIGN-ON** (Inicio de sesión único) para abrir la ventana de configuración de SSO.

    ![Configurar inicio de sesión único](./media/active-directory-saas-teamphoria-tutorial/admin_sso_configure.png)

9. Haga clic en la opción **ADD NEW IDENTITY PROVIDER** (Agregar nuevo proveedor de identidades) en la esquina superior derecha para abrir el formulario para agregar la configuración de SSO.

    ![Configurar inicio de sesión único](./media/active-directory-saas-teamphoria-tutorial/add_new_identity_provider.png)

10. Escriba la información en los campos, tal como se describe a continuación:

    ![Configurar inicio de sesión único](./media/active-directory-saas-teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **DISPLAY NAME**: escriba el nombre para mostrar del complemento en la página de administración.

    b. **BUTTON NAME**: el nombre de la pestaña que se mostrará en la página de inicio de sesión mediante SSO.

    c. **CERTIFICATE**: abra el certificado descargado anteriormente desde Azure Portal con el Bloc de notas, copie el contenido del mismo y péguelo en este cuadro de texto.

    d. **ENTRY POINT**: pegar la **dirección URL del servicio Single Sign-On de SAML** copiada anteriormente de Azure Portal.

    e. Cambie la opción a **ON** y haga clic en **SAVE**.   

<!--### Next steps

To ensure users can sign-in to Teamphoria after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Teamphoria prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Teamphoria in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Teamphoria users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-teamphoria-test-user"></a>Crear un usuario de prueba Teamphoria

Para permitir que los usuarios de Azure AD inicien sesión en Teamphoria, deben aprovisionarse en Teamphoria. En el caso de Teamphoria, el aprovisionamiento es una tarea manual.

**Para aprovisionar cuentas de usuario, realice estos pasos:**

1. Inicie sesión en su sitio de la compañía de Teamphoria como administrador.

2. Haga clic en **ADMIN** en la barra de herramientas izquierda y haga clic en la pestaña **MANAGE** en **USERS** para abrir la página de administración para los usuarios.

    ![Agregar empleado](./media/active-directory-saas-teamphoria-tutorial/admin_manage_users.png)

3. Haga clic en la opción **MANUAL INVITE**.

    ![Invitar a contactos](./media/active-directory-saas-teamphoria-tutorial/admin_manage_add_users.png)  

4. En esta página, realizar las acciones siguientes. 
    
    ![Invitar a contactos](./media/active-directory-saas-teamphoria-tutorial/manual_user_invite.png)  

    a. En el cuadro de texto **EMAIL ADDRESS**, escriba la **dirección de correo electrónico** de Britta Simon.

    b. En el cuadro de texto **FIRST NAME**, escriba **Britta**.

    c. En el cuadro de texto **LAST NAME**, escriba **Simon**.

    d. Haga clic en **INVITE 1 USER**. El usuario debe aceptar la invitación para su creación en el sistema.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Teamphoria.

![Asignar usuario][200] 

**Para asignar Britta Simon a Teamphoria, realice los pasos siguientes:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. A continuación, haga clic en **All applications** (Todas las aplicaciones).

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Teamphoria**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_203.png

