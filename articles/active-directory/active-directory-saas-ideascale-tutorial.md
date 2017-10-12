---
title: "Tutorial: integración de Azure Active Directory con IdeaScale | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory e IdeaScale."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 88099e942319f16dd721da83e4e69b8fcb836c0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Tutorial: integración de Azure Active Directory con IdeaScale

En este tutorial, aprenderá a integrar IdeaScale con Azure Active Directory (Azure AD).

La integración de IdeaScale con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a IdeaScale
- Puede permitir que los usuarios inicien sesión automáticamente en IdeaScale (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar las cuentas en una sola ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con IdeaScale, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en IdeaScale

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de IdeaScale desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-ideascale-from-the-gallery"></a>Adición de IdeaScale desde la galería
Para configurar la integración de IdeaScale en Azure AD, deberá agregar IdeaScale desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar IdeaScale desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **IdeaScale**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_search.png)

5. En el panel de resultados, seleccione **IdeaScale** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con IdeaScale con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de IdeaScale para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de IdeaScale.

Para establecer la relación de vínculo en IdeaScale, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con IdeaScale, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de IdeaScale](#creating-an-ideascale-test-user)**: para tener un homólogo de Britta Simon en IdeaScale vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación IdeaScale.

**Para configurar el inicio de sesión único de Azure AD con IdeaScale, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **IdeaScale**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_samlbase.png)

3. En la sección de **Dominio y direcciones URL de IdeaScale**, siga estos pasos:

    ![Configurar inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.ideascale.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón:
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Para obtener estos valores, póngase en contacto con el [equipo de soporte técnico de clientes de IdeaScale](http://support.ideascale.com/). 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de IdeaScale**, haga clic en **Configurar IdeaScale** para abrir la ventana **Configurar inicio de sesión**. Copie los valores de **Sign-Out URL, and SAML Entity ID** (Dirección URL de cierre de sesión e identificador de entidad de SAML) de la **sección Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_configure.png) 

7. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de IdeaScale como administrador.

8. Vaya a **Configuración de la comunidad**.
   
    ![Configuración de la Comunidad](./media/active-directory-saas-ideascale-tutorial/ic790847.png "Configuración de la Comunidad")

9. Vaya a **Security \> Single Signon Settings** (Seguridad > Configuración de inicio de sesión único).
   
    ![Configuración de inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/ic790848.png "Configuración de inicio de sesión único")

10. En **Single-Signon Type** (Tipo de inicio de sesión único), seleccione **SAML 2.0**.
   
    ![Tipo de inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/ic790849.png "Tipo de inicio de sesión único")

11. En el cuadro de diálogo **Configuración de inicio de sesión único** , siga estos pasos:
   
    ![Configuración de inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/ic790850.png "Configuración de inicio de sesión único")
   
    a. En el cuadro de texto **SAML IdP Entity ID** (Identificador de entidad del IdP de SAML), pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que copió de Azure Portal.

    b. Copie el contenido del archivo de metadatos descargado de Azure Portal y péguelo en el cuadro de texto **SAML IdP Metadata** (Metadatos del IdP de SAML).

    c. En el cuadro de texto **Logout Success URL** (Dirección URL de cierre de sesión correcto), pegue el valor de **Sign-Out URL** (Dirección URL de cierre de sesión) que copió de Azure Portal.

    d. Haga clic en **Guardar cambios**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ideascale-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ideascale-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ideascale-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-ideascale-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-an-ideascale-test-user"></a>Creación de un usuario de prueba de IdeaScale

Para permitir que los usuarios de Azure AD inicien sesión en IdeaScale, deben aprovisionarse en IdeaScale. En el caso de IdeaScale, el aprovisionamiento es una tarea manual.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. Inicie sesión en el sitio de la compañía de **IdeaScale** como administrador.

2. Vaya a **Configuración de la comunidad**.
   
    ![Configuración de la Comunidad](./media/active-directory-saas-ideascale-tutorial/ic790847.png "Configuración de la Comunidad")

3. Vaya a **Basic Settings \> Member Management** (Configuración básica > Administración de miembros).

4. Haga clic en **Agregar miembro**.
   
    ![Administración de miembros](./media/active-directory-saas-ideascale-tutorial/ic790852.png "Administración de miembros")

5. En la sección Add New Member (Agregar nuevo miembro), lleve a cabo estos pasos:
   
    ![Agregar nuevo miembro](./media/active-directory-saas-ideascale-tutorial/ic790853.png "Agregar nuevo miembro")
   
    a. En el cuadro de texto **Direcciones de correo electrónico** , escriba la dirección de correo electrónico de la cuenta de AAD válida que quiera aprovisionar.
   
    b. Haga clic en **Guardar cambios**. 
   
    >[!NOTE]
    >El titular de la cuenta de Azure Active Directory recibe un mensaje de correo electrónico con un vínculo para confirmar la cuenta antes de que se active.
      
>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de IdeaScale que proporcione IdeaScale para aprovisionar cuentas de usuario de AAD.
 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a IdeaScale.

![Asignar usuario][200] 

**Para asignar Britta Simon a IdeaScale, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **IdeaScale**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 


El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de IdeaScale en el panel de acceso, debería iniciar sesión automáticamente en su aplicación IdeaScale.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_203.png

