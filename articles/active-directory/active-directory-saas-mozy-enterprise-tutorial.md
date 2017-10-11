---
title: "Tutorial: Integración de Azure Active Directory con Mozy Enterprise | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Mozy Enterprise."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.openlocfilehash: ac73aadcb8205f24f9d2dbce5af76f53bbcb9753
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Tutorial: Integración de Azure Active Directory con Mozy Enterprise

En este tutorial, aprenderá a integrar Mozy Enterprise con Azure Active Directory (Azure AD).

La integración de Mozy Enterprise con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Mozy Enterprise.
- Puede permitir que los usuarios inicien sesión automáticamente en Mozy Enterprise (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Mozy Enterprise, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Mozy Enterprise

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Mozy Enterprise desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Adición de Mozy Enterprise desde la galería
Para configurar la integración de Mozy Enterprise en Azure AD, es preciso agregar dicha solución desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Mozy Enterprise desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **Mozy Enterprise**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_search.png)

5. En el panel de resultados, seleccione **Mozy Enterprise** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Mozy Enterprise con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Mozy Enterprise para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Mozy Enterprise.

Para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario** de Mozy Enterprise.

Para configurar y probar el inicio de sesión único de Azure AD con Mozy Enterprise, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Mozy Enterprise](#creating-a-mozy-enterprise-test-user)**: el objetivo es tener un homólogo de Britta Simon en Mozy Enterprise que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Mozy Enterprise.

**Para configurar el inicio de sesión único de Azure AD con Mozy Enterprise, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Mozy Enterprise** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_samlbase.png)

3. En la sección **Dominio y direcciones URL de Mozy Enterprise**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<tenantname>.Mozyenterprise.com`.

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de atención al cliente de Mozy Enterprise](http://support.mozy.com/) para obtener este valor.

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Mozy Enterprise**, haga clic en **Configurar Mozy Enterprise** para abrir la ventana **Configurar inicio de sesión**. Copie **SAML Entity ID and SAML Single Sign-On Service URL** (URL del servicio de inicio de sesión único de SAML e Identificador de entidad de SAML) de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_configure.png) 

7. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Mozy Enterprise.

8. En la sección **Configuración**, haga clic en **Directiva de autenticación**.
   
   ![Directiva de autenticación](./media/active-directory-saas-mozy-enterprise-tutorial/ic777314.png "Directiva de autenticación")

9. En la sección **Authentication Policy** (Directiva de autenticación), realice estos pasos:
   
   ![Directiva de autenticación](./media/active-directory-saas-mozy-enterprise-tutorial/ic777315.png "Directiva de autenticación")
   
   a. Seleccione **Servicio de directorio** como **Proveedor**.
   
   b. Seleccione **Use LDAP Push**(Usar inserción de LDAP).
   
   c. Haga clic en la pestaña **Autenticación SAML** .
   
   d. Pegue el valor de **dirección URL del servicio de inicio de sesión único de SAML** que copió de Azure Portal en el cuadro de texto **IdP Login URL** (Dirección URL de inicio de sesión del proveedor de identidades).
   
   e. Pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que ha copiado de Azure Portal en el cuadro de texto **IdP Entity ID** (Identificador de entidad del proveedor de identidades).
   
   f. Abra el certificado codificado en base 64 descargado en el Bloc de notas, copie su contenido en el Portapapeles y, a continuación, pegue todo el certificado en el cuadro de texto **Certificado SAML**.
   
   g. Seleccione **Habilitar SSO para que los administradores inicien sesión con sus credenciales de red**.
   
   h. Haga clic en **Guardar cambios**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-mozy-enterprise-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-mozy-enterprise-test-user"></a>Creación de un usuario de prueba de Mozy Enterprise

Para permitir que los usuarios de Azure AD inicien sesión en Mozy Enterprise, deben aprovisionarse en Mozy Enterprise. En el caso de Mozy Enterprise, el aprovisionamiento es una tarea manual.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Mozy Enterprise ofrecida por Mozy Enterprise para aprovisionar cuentas de usuario de AAD.

**Para aprovisionar cuentas de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino de **Mozy Enterprise** .

2. Haga clic en **Usuarios** y, luego, en **Agregar nuevo usuario**.
   
   ![Usuarios](./media/active-directory-saas-mozy-enterprise-tutorial/ic777317.png "Usuarios")
   
   >[!NOTE]
   >La opción **Agregar nuevo usuario** solo se muestra si **Mozy** está seleccionado como proveedor en **Directiva de autenticación**. Si la autenticación SAML está configurada, los usuarios se agregan automáticamente al iniciar sesión por primera vez con inicio de sesión único.
    
3. En el cuadro de diálogo Nuevo usuario, realice los pasos siguientes:
   
   ![Agregar usuarios](./media/active-directory-saas-mozy-enterprise-tutorial/ic777318.png "Agregar usuarios")
   
   a. En la lista **Choose a Group** (Elija un grupo), seleccione un grupo.
   
   b. En la lista **Tipo de usuario** , seleccione un tipo.
   
   c. En el cuadro de texto **Username** (Nombre de usuario), escriba el nombre de usuario de Azure AD.
   
   d. En el cuadro de texto **Email** (Correo electrónico), escriba la dirección de correo electrónico del usuario de Azure AD.
   
   e. Seleccione **Send user instruction email**(Enviar correo electrónico al usuario con instrucciones).
   
   f. Haga clic en **Add User(s)**(Agregar usuarios).

     >[!NOTE]
     > Después de crear el usuario, se enviará un correo electrónico al usuario de Azure AD que incluye un vínculo para confirmar la cuenta antes de que se active.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Mozy Enterprise.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Mozy Enterprise, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Mozy Enterprise**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_mozyenterprise_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Mozy Enterprise en el Panel de acceso, debería entrar en la página de inicio de sesión de dicha aplicación.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mozy-enterprise-tutorial/tutorial_general_203.png

