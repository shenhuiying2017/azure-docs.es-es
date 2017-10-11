---
title: "Tutorial: integración de Azure Active Directory con StatusPage | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y StatusPage."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: fa16cdec7b89404c140435fe57d5aa4b08cfa985
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Tutorial: Integración de Azure Active Directory con StatusPage

En este tutorial, obtendrá información sobre cómo integrar StatusPage con Azure Active Directory (Azure AD).

La integración de StatusPage con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a StatusPage.
- Puede permitir que los usuarios inicien sesión automáticamente en StatusPage (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con StatusPage, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en StatusPage

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Incorporación de StatusPage desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-statuspage-from-the-gallery"></a>Incorporación de StatusPage desde la galería
Para configurar la integración de StatusPage en Azure AD, deberá agregar StatusPage desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar StatusPage desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **StatusPage**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_search.png)

5. En el panel de resultados, seleccione **StatusPage** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con StatusPage con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de StatusPage para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de StatusPage.

Para establecer la relación de vínculo, en StatusPage, asigne el valor del **nombre de usuario** en Azure AD como valor del **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con StatusPage, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de StatusPage](#creating-a-statuspage-test-user)**: para tener un homólogo de Britta Simon en StatusPage que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación StatusPage.

**Para configurar el inicio de sesión único de Azure AD con StatusPage, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **StatusPage**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_samlbase.png)

3. En la sección **Dominio y direcciones URL de StatusPage**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_url.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón:
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |

    b. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con el siguiente patrón: 
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |

    > [!NOTE]
    > Póngase en contacto con el equipo de soporte técnico de StatusPage [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)a fin de solicitar los metadatos necesarios para configurar el inicio de sesión único. 
    >
    >a. En los metadatos, copie el valor de Emisor y luego péguelo en el cuadro de texto **Identificador** .
    >
    >b. En los metadatos, copie el valor de URL de respuesta y luego péguelo en el cuadro de texto **URL de respuesta** .

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de StatusPage**, haga clic en **Configurar StatusPage** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_configure.png) 

7. En otra ventana del explorador, inicie sesión en su sitio de la empresa de StatusPage como administrador.

8. En la barra de herramientas principal, haga clic en **Administrar cuenta**.
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png) 

10. Haga clic en la pestaña **Inicio de sesión único** . 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_07.png) 

11. En la página Configuración de SSO, realice los pasos siguientes:
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_08.png) 

    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_09.png) 
 
    a. En el cuadro de texto **SSO Target URL** (Dirección URL de destino de SSO), pegue el valor de la **dirección URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    b. Abra el certificado descargado en el Bloc de notas, copie el contenido y luego péguelo en el cuadro de texto **Certificado** . 

    c. Haga clic en **GUARDAR CONFIGURACIÓN**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-statuspage-test-user"></a>Creación de un usuario de prueba de StatusPage

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en StatusPage.

StatusPage admite el aprovisionamiento Just-In-Time. Ya lo ha habilitado en [Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on).

**Para crear un usuario llamado Britta Simon en StatusPage, realice los pasos siguientes:**

1. Inicie sesión en su sitio de la empresa StatusPage como administrador.

2. En el menú de la parte superior, haga clic en **Administrar cuenta**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_06.png)

3. Haga clic en la pestaña **Team Members** (Miembros del equipo). 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_10.png) 

4. Haga clic en **ADD TEAM MEMBER**(Agregar miembro del equipo). 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_11.png) 

5. Escriba **Email Address** (Dirección de correo electrónico), **First Name** (Nombre) y **Surname** (Apellido) en los cuadros de texto correspondientes para un usuario válido que quiera aprovisionar. 
   
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_12.png) 

6. En **Rol**, elija **Administrador de clientes**.

7. Haga clic en **CREATE ACCOUNT** (CREAR CUENTA).

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a StatusPage.

![Asignar usuario][200] 

**Para asignar a Britta Simon a StatusPage, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **StatusPage**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-statuspage-tutorial/tutorial_statuspage_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de StatusPage en el panel de acceso, debería iniciar sesión automáticamente en su aplicación StatusPage.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-statuspage-tutorial/tutorial_general_203.png

