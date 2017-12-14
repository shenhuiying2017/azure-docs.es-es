---
title: "Tutorial: Integración de Azure Active Directory con Skydesk Email | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Skydesk Email."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: c495bd85fa2c55eec93f2e1100505b64389207f8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Tutorial: Integración de Azure Active Directory con Skydesk Email

En este tutorial, aprenderá a integrar SkyDesk Email con Azure Active Directory (Azure AD).

Integrar Skydesk Email con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Skydesk Email.
- Puede permitir que los usuarios inicien sesión automáticamente en Skydesk Email (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Skydesk Email, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Skydesk Email

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, aquí puede obtener una versión de prueba de un mes [oferta de prueba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Adición de Skydesk Email desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-skydesk-email-from-the-gallery"></a>Adición de Skydesk Email desde la galería
Para configurar la integración de Skydesk Email en Azure AD, será preciso que agregue Skydesk Email desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Skydesk Email desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro Buscar, escriba **Skydesk Email**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_search.png)

5. En el panel de resultados, seleccione **SkyDesk Email** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con SkyDesk Email utilizando un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Skydesk Email para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Skydesk Email.

Para establecer la relación de vínculo, en SkyDesk Email, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Skydesk Email, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Skydesk Email](#creating-a-skydesk-email-test-user)**: para tener un homólogo de Britta Simon en Skydesk Email que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación SkyDesk Email.

**Para configurar el inicio de sesión único de Azure AD con Skydesk Email, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **SkyDesk Email**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_samlbase.png)

3. En la sección **Dominio y direcciones URL de SkyDesk Email**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_url.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://mail.skydesk.jp/portal/<companyname>`.

    > [!NOTE] 
    > Este valor no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de SkyDesk Email](https://www.skydesk.sg/support/) para obtener este valor. 
 
4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de SkyDesk Email**, haga clic en **Configurar SkyDesk Email** para abrir la ventana **Configurar inicio de sesión**. Copie los valores **Sign-Out URL y SAML Single Sign-On Service URL** (Dirección URL de cierre de sesión y Dirección URL del servicio de inicio de sesión único de SAML) de la **sección de referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_configure.png) 

7. Para habilitar SSO en **Skydesk Email**, siga estos pasos:

    a. Inicie sesión en su cuenta de correo de Skydesk Email como administrador.

    b. En el menú en la parte superior, haga clic en **Setup** (Configurar) y seleccione **Org** (Organización). 
    
      ![Configurar inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
    c. En el panel izquierdo, haga clic en **Domains** (Dominios).
    
      ![Configurar inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    d. Haga clic en **Add Domain** (Agregar dominio).
    
      ![Configurar inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    e. Escriba el nombre de dominio y, a continuación, compruebe el dominio.
    
      ![Configurar inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    f. Haga clic en **SAML Authentication** (Autenticación SAML) desde el panel izquierdo.
    
      ![Configurar inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_52.png)

8. En la página de diálogo **SAML Authentication** (Autenticación SAML), siga estos pasos:
   
      ![Configurar inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
    >[!NOTE]
    >Para usar la autenticación basada en SAML, necesita **comprobar el dominio** o configurar la **dirección URL del portal**. Puede configurar la dirección URL del portal con el nombre único.
    > 
    > 
   
    ![Configurar inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. En el cuadro de texto **Login URL** (Dirección URL de inicio de sesión), pegue el valor de **SAML Single Sign-On Service URL** (Dirección URL de inicio de sesión único de SAML) que copió de Azure Portal.
   
    b. En el cuadro de texto **Logout** (Dirección URL de cierre de sesión), pegue el valor de **dirección URL de cierre de sesión** que copió de Azure Portal.

    c. **Cambiar dirección URL de contraseña** es opcional, déjelo en blanco.

    d. Haga clic en **Get Key From File** (Obtener clave de archivo) para seleccionar el certificado descargado desde Azure Portal y, después, haga clic en **Abrir** para cargar el certificado.

    e. En **Algoritmo**, seleccione **RSA**.

    f. Haga clic en **Aceptar** para guardar los cambios.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_01.png) 

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_02.png) 

3. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-skydesk-email-test-user"></a>Creación de un usuario de prueba de Skydesk Email

En esta sección, creará un usuario llamado Britta Simon en Skydesk Email.

1. Haga clic en **User Access** (Acceso de usuario) en el panel de la izquierda de Skydesk Email y, a continuación, escriba su nombre de usuario. 

    ![Configurar inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_58.png)

>[!NOTE] 
>Si necesita crear usuarios de forma masiva, debe ponerse en contacto con el [equipo de soporte al cliente de Skydesk Email](https://www.skydesk.sg/support/).


### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a SkyDesk Email para que use el inicio de sesión único de Azure.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Skydesk Email, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego, haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Skydesk Email**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Skydesk Email en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Skydesk Email.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_203.png

