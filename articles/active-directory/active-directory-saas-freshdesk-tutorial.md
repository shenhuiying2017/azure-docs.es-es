---
title: "Tutorial: Integración de Azure Active Directory con Freshdesk | Microsoft Docs"
description: "Aprenda a configurar el inicio de sesión único entre Azure Active Directory y FreshDesk."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 232058fc35c8206c4d8f4e990d1aea5d3d9a5a69
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: Integración de Azure Active Directory con Freshdesk

En este tutorial, obtendrá información sobre cómo integrar FreshDesk con Azure Active Directory (Azure AD).

La integración de FreshDesk con Azure AD proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a FreshDesk.
- Puede permitir que los usuarios inicien sesión automáticamente en FreshDesk (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con FreshDesk, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en FreshDesk

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. La situación descrita en este tutorial consta de dos bloques de creación principales:

1. Agregar FreshDesk desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-freshdesk-from-the-gallery"></a>Agregar FreshDesk desde la galería
Para configurar la integración de FreshDesk en Azure AD, deberá agregar FreshDesk desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar FreshDesk desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones][2]
    
3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Aplicaciones][3]

4. En el cuadro de búsqueda, escriba **FreshDesk**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_search.png)

5. En el panel de resultados, seleccione **FreshDesk** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con FreshDesk con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de FreshDesk para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de FreshDesk.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en FreshDesk.

Para configurar y probar el inicio de sesión único de Azure AD con FreshDesk, es preciso completar los siguientes bloques de creación:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de FreshDesk](#creating-a-freshdesk-test-user)**: para tener un homólogo de Britta Simon en FreshDesk que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de administración de Azure y configurará el inicio de sesión único en la aplicación FreshDesk.

**Para configurar el inicio de sesión único de Azure AD con FreshDesk, realice los pasos siguientes:**

1. En el Portal de administración de Azure, en la página de integración de la aplicación **FreshDesk**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. En la sección **Dominio y direcciones URL de FreshDesk**, escriba la **dirección URL de inicio de sesión** como: `https://<tenant-name>.freshdesk.com` o cualquier otro valor que le sugiera Freshdesk.

    ![Configurar inicio de sesión único](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_url.png)

    > [!NOTE] 
    > Tenga en cuenta que este no es el valor real. Tiene que actualizar este valor con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) para obtener este valor.  

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado** y, a continuación, guarde el certificado en el equipo.

    ![Configurar inicio de sesión único](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/active-directory-saas-freshdesk-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de FreshDesk**, haga clic en **Configurar FreshDesk** para abrir la ventana Configurar inicio de sesión. Copie la dirección URL del servicio de inicio de sesión único de SAML y la URL de cierre de sesión de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_configure.png)

7. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Freshdesk como administrador.

8. En el menú de la parte superior, haga clic en **Administrador**.
   
   ![Administración](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Administración")

9. En la pestaña **Configuración general**, haga clic en **Seguridad**.
   
   ![Seguridad](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Seguridad")

10. En la sección **Seguridad** , realice estos pasos:
   
    ![Inicio de sesión único](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Inicio de sesión único")
   
    a. En **Inicio de sesión único (SSO)**, seleccione **Activado**.

    b. Seleccione **Inicio de sesión único de SAML**.

    c. Escriba la **Dirección URL de inicio de sesión único de SAML** que copió desde Azure Portal en el cuadro de texto **URL de inicio de sesión SAML**.

    d. Escriba la **Dirección URL de cierre de sesión**  que copió desde Azure Portal en el cuadro de texto **URL de cierre de sesión**.

    e. Copie el valor de **Huella digital** desde el certificado descargado de Azure Portal y péguelo en el cuadro de texto **Huella digital del certificado de seguridad**.  
 
    >[!TIP]
    >Para obtener más información, consulte [Recuperación del valor de huella digital de un certificado](http://youtu.be/YKQF266SAxI). 
    
    f. Haga clic en **Guardar**.


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Crear**.
 
### <a name="creating-a-freshdesk-test-user"></a>Creación de un usuario de prueba de FreshDesk

Para permitir que los usuarios de Azure AD inicien sesión en FreshDesk, deben aprovisionarse en FreshDesk.  
En el caso de FreshDesk, el aprovisionamiento es una tarea manual.

**Para aprovisionar cuentas de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino de **Freshdesk** .
2. En el menú de la parte superior, haga clic en **Administrador**.
   
   ![Administración](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Administración")

3. En la pestaña **Configuración general**, haga clic en **Agentes**.
   
   ![Agentes](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agentes")

4. Haga clic en **Nuevo agente**.
   
    ![Nuevo agente](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "Nuevo agente")

5. En el cuadro de diálogo Agent Information (Información de agente), realice los pasos siguientes:
   
   ![Información sobre agentes](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Información sobre agentes")
   
   a. En el cuadro de texto **Nombre completo** , escriba el nombre de la cuenta de Azure AD que quiera aprovisionar.

   b. En el cuadro de texto **Email** (Correo electrónico), escriba la dirección de correo electrónico de la cuenta de Azure AD que quiera aprovisionar.

   c. En el cuadro de texto **Título** , escriba el título de la cuenta de Azure AD que quiera aprovisionar.

   d. Seleccione **Agents role** (Rol de agentes) y luego haga clic en **Asignar**.
       
   e. Haga clic en **Guardar**.     
   
    >[!NOTE]
    >El titular de la cuenta de Azure AD recibirá un mensaje de correo electrónico que incluye un vínculo para confirmar la cuenta antes de que se active. 
    > 
    
    >[!NOTE]
    >Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Freshdesk que proporcione Freshdesk para aprovisionar cuentas de usuario de AAD. a FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a FreshDesk.

![Asignar usuario][200] 

**Para asignar Britta Simon a FreshDesk, realice los pasos siguientes:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. A continuación, haga clic en **All applications** (Todas las aplicaciones).

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **FreshDesk**.

    ![Configurar inicio de sesión único](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de FreshDesk en el panel de acceso, debería obtener la página de inicio de sesión e iniciar sesión automáticamente en su aplicación FreshDesk.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_203.png

