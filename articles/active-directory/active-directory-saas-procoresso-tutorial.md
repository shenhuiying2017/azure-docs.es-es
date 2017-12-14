---
title: "Tutorial: Integración de Azure Active Directory con Procore SSO | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Procore SSO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: c6bae8e24f252a535571308467b428832b542e48
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Tutorial: Integración de Azure Active Directory con Procore SSO

En este tutorial aprenderá a integrar Procore SSO con Azure Active Directory (Azure AD).

La integración de Procore SSO con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Procore SSO.
- Puede permitir que los usuarios inicien sesión automáticamente en Procore SSO (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Procore SSO, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Procore SSO.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Procore SSO, necesita lo siguiente:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Procore SSO

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar Procore SSO desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-procore-sso-from-the-gallery"></a>Agregar Procore SSO desde la galería
Para configurar la integración de Procore SSO en Azure AD, tiene que agregar Procore SSO desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Procore SSO desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Procore SSO**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_search.png)

5. En el panel de resultados, seleccione **Procore SSO** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Procore SSO con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Procore SSO para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Procore SSO.

Esta relación de vínculo se establece mediante la asignación del valor de **nombre de usuario** en Azure AD como valor de **Username** (Nombre de usuario) en Procore SSO.

Para configurar y probar el inicio de sesión único de Azure AD con Procore SSO, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Procore SSO](#creating-a-procore-sso-test-user)**: para tener un homólogo de Britta Simon en Procore SSO que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de administración de Azure y configurará el inicio de sesión único en la aplicación Procore SSO.

**Para configurar el inicio de sesión único de Azure AD con Procore SSO, realice los pasos siguientes:**

1. En el Portal de administración de Azure, en la página de integración de aplicaciones de **Procore SSO**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_samlbase.png)

3. En la sección **Dominio y direcciones URL de Procore SSO**, el usuario no tiene que realizar ningún paso ya que la aplicación se ha integrado previamente con Azure.

    ![Configurar inicio de sesión único](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_url.png)

4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo XML en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-procoresso-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Procore SSO**, haga clic en **Configurar Procore SSO** para abrir la ventana **Configurar inicio de sesión único**. Copie la **SAML Entity ID and SAML Single Sign-On Service URL** (URL del servicio de inicio de sesión único de SAML e Id. de entidad de SAML) de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_configure.png) 

7. Para configurar el inicio de sesión único en **Procore SSO**, inicie sesión como administrador en su sitio de la compañía de procore.

8. En la lista desplegable del cuadro de herramientas, haga clic en **Administrador** para abrir la página de configuración del SSO.

    ![Configurar inicio de sesión único](./media/active-directory-saas-procoresso-tutorial/procore_tool_admin.png)

9. Pegue los valores en los cuadros tal y como se describe a continuación:

    ![Configurar inicio de sesión único](./media/active-directory-saas-procoresso-tutorial/procore_setting_admin.png)    

    a. En el cuadro **Single Sign On Issuer URL** (URL del emisor de inicio de sesión único), pegue el Id. de entidad de SAML que copió de Azure Portal.

    b. En el cuadro **SAML Sign On Target URL** (URL de destino de inicio de sesión de SAML), pegue la URL del servicio de inicio de sesión único de SAML que copio de Azure Portal.

    c. Abra ahora el **XML de metadatos** que descargó antes de Azure Portal y copie el certificado en el etiqueta de nombre **X509Certificate**. Pegue el valor copiado en el cuadro **Single Sign On x509 Certificate** (Certificado x509 de inicio de sesión único).

10. Haga clic en **Guardar cambios**.

11. Al finalizar esta configuración, tiene que enviar el **nombre de dominio** (por ejemplo, **contoso.com**) con el que inicia sesión en Procore al [equipo de soporte técnico de Procore](https://support.procore.com/) y ellos activarán el SSO federado para ese dominio.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-procore-sso-test-user"></a>Creación de un usuario de prueba de Procore SSO

Siga estos pasos para crear un usuario de prueba en Procore.

1. Inicie sesión como administrador en su sitio de la compañía de procore.  

2. En la lista desplegable del cuadro de herramientas, haga clic en **Directorio** para abrir la página de directorio de la compañía.

    ![Configurar inicio de sesión único](./media/active-directory-saas-procoresso-tutorial/Procore_sso_directory.png)

3. Haga clic en la opción **Add a Person** (Agregar una persona) para abrir el formulario y especificar la ejecución de las opciones siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-procoresso-tutorial/Procore_user_add.png)

    a. En el cuadro de texto **Nombre**, escriba el nombre del usuario, por ejemplo, **Britta**.

    b. En el cuadro de texto **Apellido**, escriba el apellido del usuario, por ejemplo, **Simon**.

    c. En el cuadro de texto **Dirección de correo electrónico**, escriba la dirección de correo del usuario**BrittaSimon@contoso.com**.

    d. Seleccione **Permission Template** (Plantilla de permisos) como **Apply Permission Template Later** (Aplicar plantilla de permisos más tarde).

    e. Haga clic en **Crear**.

4. Compruebe los datos del contacto recién agregado y actualícelos.

    ![Configurar inicio de sesión único](./media/active-directory-saas-procoresso-tutorial/Procore_user_check.png)

5. Haga clic en **Save and Send Invitiation** (Guardar y enviar invitación), si se requiere una invitación por correo electrónico, o en **Guardar** (guardar directamente) para completar el registro del usuario.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-procoresso-tutorial/Procore_user_save.png)    

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Procore SSO.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Procore SSO, realice los pasos siguientes:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. A continuación, haga clic en **All applications** (Todas las aplicaciones).

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Procore SSO**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](https://msdn.microsoft.com/library/dn308586). Al hacer clic en el icono de Procore SSO del panel de acceso, debería iniciar sesión automáticamente en su aplicación Procore SSO.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_203.png

